---
title: git stash 贮藏小技巧
date: 2020-08-02 15:27:29
tags:
- Git
- stash
categories:
- Git
toc: true
---

## 目录

* Git stash save
* Git stash list
* Git stash apply
* Git stash pop
* Git stash show
* Git stash branch <name>
* Git stash clear
* Git stash drop

## 详解

### Git stash save

* Git stash with message
  贮藏(`stash`)时候增加备注信息，格式：`git stash save “Your stash message”`。
* Stashing untracked files
  贮藏区(`stash`)未被git追踪的文件，格式：`git stash save -u` 或者 `git stash save --include-untracked`。
  
### Git stash list

* `git stash` 实际是将变更放在一个提交对象中（Git commit object）中，存放在本地仓库中，并且存储是栈的形式。所以可以使用`list`命令查看`stash`中的所有记录，格式：`git stash list`。

### Git stash apply

* 拿出贮藏区（`stash`）栈中的变更应用到当前的工作区，默认拿栈顶的`stash@{0}`，如果想要拿其他贮藏区的变更，可以使用命令：`git stash apply stash@{1}`。此操作后，贮藏区（`stash`）中**不会将**贮藏列表中使用的块移除。

### Git stash pop

* 弹出贮藏区（`stash`）栈中的变更应用到当前的工作区，默认拿栈顶的`stash@{0}`，如果想要拿其他贮藏区的变更，可以使用命令：`git stash pop stash@{1}`。此操作后，贮藏区（`stash`）中**会将**贮藏列表中使用的块移除。

### Git stash show

* `git stash show`显示贮藏区（`stash`）最后的一个的贮藏提交中的变更摘要信息，如果想要看详细变更比对信息，可以使用命令：`git stash show -p`，其中`-p`是 patch form 的意思。如果要看某个特定的贮藏区块，可以使用命令：`git stash show stash@{1}`。

### Git stash branch <branch-name>

* `git stash branch <branch-name>` 命令使用贮藏区（`stash`）最后的一个的贮藏提交中的变更创建一个分支，分支名字是`<branch-name>`，此操作后，贮藏区（`stash`）中**会将**贮藏列表中使用的块移除。如果要拿特定的变更，可以使用命令：`git stash branch <name> stash@{1}`。

### Git stash clear

* 移除贮藏区（`stash`）中所有存储的栈内容，此操作无法恢复。

### Git stash drop

* `git stash drop` 命令会移除贮藏区（`stash`）中最顶块的贮藏块内容，不可恢复。如果要移除特定的块，可以使用命令：`git stash drop stash@{1}`。


-----
## 参考
* [Useful tricks you might not know about Git stash](https://www.freecodecamp.org/news/useful-tricks-you-might-not-know-about-git-stash-e8a9490f0a1a/)


