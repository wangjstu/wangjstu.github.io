---
title: >-
  error The following untracked working tree files would be overwritten by
  checkout
date: 2020-07-09 01:30:49
categories:
- Git
tags:
- Git
toc: true
---


## 问题
今天在服务器上更新了一些代码文件，想着在本地也更新一下，之前在本地也编辑了部分，没有服务器上的全，要废弃了。到对应代码目录执行`git pull origin master`，蹦出了一堆错误：
```ERROR
error: The following untracked working tree files would be overwritten by checkout:
        Learn_C_the_HARD_WAY/Exercise22/dbg.h
        Learn_C_the_HARD_WAY/Exercise22/ex22.c
        Learn_C_the_HARD_WAY/Exercise22/ex22.h
        Learn_C_the_HARD_WAY/Exercise22/ex22_main.c
Please move or remove them before you switch branches.
Aborting
fatal: Could not detach HEAD
First, rewinding head to replay your work on top of it...
```
通过错误提示可知，是由于一些untracked working tree files引起的问题。所以只要解决了这些untracked的文件就能解决这个问题。

## 解决方式：
在对应目录下运行`git clean -d -fx`即可。可能很多人都不明白-d，-fx到底是啥意思，其实git clean -d -fx表示：删除 一些 没有 git add 的 文件；

```SHELL
    git clean 参数 
    -n 显示将要删除的文件和目录；
    -x -----删除忽略文件已经对git来说不识别的文件
    -d -----删除未被添加到git的路径中的文件
    -f -----强制运行
    git clean -n
    git clean -df
    git clean -f
```
