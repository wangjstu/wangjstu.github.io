---
title: 关于shell文件中换行符和回车的故事
date: 2023-07-23 15:27:29
tags:
- 回车
- 换行符
categories:
- Shell
toc: true
---

## 故事概览
- 这个故事发生持续了1个小时，现象是一个shell脚本报了`ambiguous redirect`错，过程是自己花了一个小时查了一圈，经验教训是shell脚本换行一定要是LF(\n)，文件格式一定要是unix的。
- 这个故事很简单，但是有些许知识或经验值得记录，以备参考。

## 故事
### 问题

- 一个简单的shell脚本运行报如下错误：
```SHELL
[wangjstu@wangjstupc]$  sh /tmp/test.sh
: ambiguous redirect /tmp/test.sh: line 2: 1
: ambiguous redirect /tmp/test.sh: line 3: 1

[wangjstu@wangjstupc]$  cat /tmp/test.sh
#!/bin/sh
php /tmp/cache1.php  >/dev/null 2>&1
php /tmp/cache2.php  >/dev/null 2>&1
```

### 分析排查过程
- 第一眼看到错误`ambiguous redirect`，结合“字面意思”+stackoverflow，怀疑的是重定向输入`>/dev/null`这里有问题，结合前人经验+单独运行单个php命令成功，判定我这边不是这个原因。
  > 网上`ambiguous redirect`大部分原因是重定向输入的对象用变量的传递，变量没有正确赋值导致
- 排除了重定向输入问题，再仔细看了几篇stackoverflow，有个牛人说，这类报错还有一种原因是上下文有问题，朝着这个思路去排查，shell脚本里面添加`set -ex`，看下是否有详细报错，结果报错还是类似的，但确定一点，我的脚本肯定上下文肯定有问题。
- 直接用`strace`跟了一下，看到了一行`read(3, "#!/bin/sh\r\nphp5 /tmp/cache1.php"..., 80) = 80`，当看到`\r\n`，自己顿时就秒清醒，是文件格式问题。

### 解决方法
- 本人实际问题原因：shell脚本格式是dos类型的，内容里面缓缓是windows下的换行`\r\n`，而标准要求是`\n`，所以会有如上错误。以前开发中使用编辑器会注意，但是本次是直接在服务器上，一开始没有往这个方向想，所以绕了弯路，不过沿途“风景”略过，还是值得记录一下。
- 解决问题命令：`sed -i 's/\r//' /tmp/test.sh`

## 拓展思考到的问题
### 如何检查shell脚本语法
- `bash -n script_name.sh`  //-n选项只做语法检查，而不执行脚本。然而换行的问题无法检查出来。
- shell脚本里面添加 `set -x` ，将shell实际运行命令输出，可以查看具体执行语句，也可以查出语法的一些问题。

### `CRLF(\r\n)`、`LF(\n)`区别
- `LF(\n)`，`Carriage Return`（回车符），ASCII代码是10，十六制为`0x0A`，适用于Unix系统(包括Linux, MacOS近些年的版本)。
- `CR(\r)`，`Line Feed`（换行符，返回光标到左边），十进制ASCII代码是13，十六进制代码为`0x0D`，一般显示`^M`，这个`^M`要用`ctrl + v ctrl + m`打出按出，在MAC OS 9或之前版本中用。
- `CRLF(\r\n)`，`Carriage Return Line Feed `（回车换行符），适用于windows操作系统。

### 如何查看shell脚本中换行是`CRLF(\r\n)`还是`LF(\n)`
#### cat
-  `cat -A filename.txt` //windows下创建的文件换行符为`^M$`，linux格式是换行符`$`

#### vim
-  `:set ff? ffs?` //查看文件格式
- `:verbose set ff? ffs?` //查看文件格式及最后一次设置源头
> 输出结果和对应操作系统的换行：
> ffs=unix,dos  Unix based systems
> ffs=dos,unix  Windows and DOS systems
> ffs=mac,unix,dos  Mac OS 9 systems
- `:e ++ff=unix`，使用unix格式读取重新读取文件，如果是`CRLF(\r\n)`，可以看到`^M`，对应的`:e ++ff=dos `使用dos查看文件格式。

#### ob
- `ob -t x1 filename.txt` //使用二进制查看文件格式，如果有`0D 0A`则是`CRLF(\r\n)`，有`0A` 则是`LF(\n)`

### 转换文件格式
#### 如何转换文件内容中`CRLF(\r\n)`为`LF(\n)`
- dos2unix
- vim方式一，不要求文件中换行统一一样，也就是`CRLF(\r\n)`、`LF(\n)`都转为`CRLF(\r\n)`
```SHELL
:update  //保存修改
:e ++ff=dos  //使用dos格式打开文件，同时可以有CRLF(\r\n)、LF(\n)
:setlocal ff=unix   //将缓冲区的内容使用LF(\n)作为行结尾写入文件，setlocal=setl，避免更改全局默认值。
:w  //将缓冲区写入文件
```
- vim方式二，要求文件中换行统一一样都是`CRLF(\r\n)`，如果有`LF(\n)`就会报错
```SHELL
:args *.c *.h   //限定后缀的文件修改
:argdo set ff=unix|update   //修改文件格式并修改为LF(\n)换行。如果同时打开多个文件，可以使用命令`:bufdo! set ff=unix|w`一次全修改。
```
- sed：`sed 's/\r//g' filename.txt > modified.txt`

#### 如何转换文件内容中`LF(\n)`为`CRLF(\r\n)`
- dos2unix
- vim方式一，不要求文件中换行统一一样，也就是`CRLF(\r\n)`、`LF(\n)`都转为`CRLF(\r\n)`
```SHELL
:update  //保存修改
:e ++ff=dos   //使用dos格式打开文件
:w  //将缓冲区写入文件
```
- vim方式二，要求文件中换行统一一样`LF(\n)`，当存在`CRLF(\r\n)`，就会报错
```SHELL
:args *.c *.h   //限定文件格式
:argdo set ff=dos|update    //转换文件换行为CRLF(\r\n).如果同时打开多个文件，可以使用命令`:bufdo! set ff=dos|w`一次全修改。
```
- sed
```SHELL
sed ":a;N;s/\n//g;$!ba" filename.txt
sed ":a;N;s/\n//g;ta" filename.txt
```

## 参考
* [Carriage_return](http://en.wikipedia.org/wiki/Carriage_return)
* [Line_feed](http://en.wikipedia.org/wiki/Line_feed)
* [File format](https://vim.fandom.com/wiki/File_format#File_format_options)
