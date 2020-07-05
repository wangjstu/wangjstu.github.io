---
title: hexo blog 创建指导手册
date: 2020-07-05 22:20:21

tags:
- Hexo
categories:
- Hexo 
toc: true
---

## Github仓库建站
1.  仓库建站
    * 进入个人github
    * Your Repositories
    * Create a new repository
    * 仓库名：wangjstu.github.io
    * 点击创建

2.  配置SSH keys
    * cd ~/.ssh
    * ssh-keygen -t rsa -C "wangjstu@gmail.com"  //一路回车
    * ssh-add id_rsa //添加秘钥
    * cat id_rsa.pub  //输出公钥，并添加到github上
    * 完毕
    
3.  初始化本地仓库
    * mkdir wangjstu.github.io && cd wangjstu.github.io
    * echo "# wangjstu.github.io" >> README.md
    * git init
    * git add README.md
    * git commit -m "first commit"
    * git remote add origin https://github.com/wangjstu/wangjstu.github.io.git
    * git push -u origin master
 
 4. 验收
    * 访问 https://wangjstu.github.io

## Node.js安装
1.  安装node，有多种方法，我使用了第二种：
    * 参考[官网教程](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm#osx-or-linux-node-version-managers)，但是这个方法，我因为墙放弃了
        * 执行命令`curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.3/install.sh | bash`
        * command -v nvm
        * nvm install node
    * 使用`brew install node`一键安装，我选择了这个方法


## Hexo安装
1.  安装hexo
    * 执行命令 `npm install -g hexo-cli`
2.  初始化blog
    * 切换到`wangjstu.github.io`目录上一级，并执行命令`hexo init blog`，先将blog文件初始化到一个blog目录。建议先单独放一个目录，后面有用。
    * `cd blog`
    * `npm istall`
3.  本地测试
    * hexo g
    * hexo server
    * 打开浏览器，访问`http://localhost:4000` 就可以看到效果
4.  目录略讲
    * node_modules: 依赖包
    * public：存放生成的页面
    * scaffolds：生成文章的一些模板
    * source：用来存放你的文章
    * themes：主题
    * \_config.yml: 博客的配置文件
5.  命令略讲解
    * hexo g   //生产静态文件
    * hexo server   //启动服务
    * hexo clean   //清除了你之前生成的东西
    * hexo generate   //顾名思义，生成静态文章，可以用 hexo g缩写
    * hexo deploy   //部署文章，可以用hexo d缩写

## 部署到github
1.  执行命令 `hexo clean`
2.  cp -R blog/ wangjstu.github.io/
3.  cd wangjstu.github.io
4.  npm install
5.  编辑配置文件 `_config.yml`
```config
# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
  type: 'git'
  repo: https://github.com/wangjstu/wangjstu.github.io.git
  branch: master
```
6.  npm install hexo-deployer-git --save  //一定要在`wangjstu.github.io`目录下执行，不如执行后面deploy命令会报错
7.  执行以下命令部署
    * hexo clean
    * hexo generate
    * hexo deploy
8.  访问`https://wangjstu.github.io` 查看效果


## 写文章及提交流程
1.  hexo new "hello"
2.  编辑`source/_posts/hello.md`
3.  hexo clean
4.  hexo g
5.  hexo d   //可以先hexo s，本地看下效果再执行提交


## Hexo主题修改

1.  修改主题流程
    *  选择主题
    *  按照自己主题的github上或官网文档进行修改
2.  实际操作
    * 我个人选择了主题 [pure](https://github.com/cofess/hexo-theme-pure/blob/master/README.cn.md)
    * 安装主题
    ```CODE
    git clone https://github.com/cofess/hexo-theme-pure.git themes/pure
    ```
    * 更新主题
    ```CODE
    cd themes/pure
    git pull
    ```
    * 打开站点配置文件，找到theme字段，将其值更改为 `pure`
    ```CODE
    # vim wangjstu.github.io/_config.yml
    theme: pure
    ```
    * 安装主题需要的插件
        * npm install hexo-wordcount --save
        * npm install hexo-generator-json-content --save
        * npm install hexo-generator-feed --save
        * npm install hexo-generator-sitemap --save
        * npm install hexo-generator-baidu-sitemap --save
        * npm install hexo-neat --save
    * 主题配置
        * 修改 `wangjstu.github.io/_config.yml`
        ```CODE
        language: zh-CN
        ```
        * 修改 `wangjstu.github.io/themes/pure/_config.yml`

## 双分支保存源文件
1.  创建分支并上传blog源代码，执行以下命令：
    *  cd wangjstu.github.io
    *  rm -r themes/pure/.git
    *  hexo clean
    *  git add .  //一定要确保代码只有源代码文件，其他不要
    *  git stash  //将源文件保存到stash
    *  git status //这里检查一定要是干净的没有变化的
    *  git checkout -b Hexo
    *  git stash pop
    *  git commit -m "add Hexo branch to save source file"
    *  git push -u origin Hexo:Hexo
2.  到github上，将默认分支修改成Hexo
3.  更换新工作环境后操作
    *  初始化仓库
        *  git clone https://github.com/wangjstu/wangjstu.github.io.git
        *  cd wangjstu.github.io
        *  npm install hexo
        *  npm install hexo-deployer-git --save
        *  安装其他对应主题需要的插件
    *  提交文章
        *  hexo clean
        *  hexo g
        *  hexo d
