---
title: 'Email协议： POP3，SMTP，IMAP 初识'
date: 2020-12-28 12:02:23

tags:
- Protocols
- POP3
- SMTP
- IMAP
categories:
- Protocols
toc: true
---

## 概要
主要通过以下三个方面进行介绍（what、which）：
* POP3是什么及使用端口
* IMAP是什么及使用端口
* SMTP是什么及使用端口

## POP3协议内容及端口
* POP3(Post Office Protocol version 3)是本地邮箱客户端从远程服务端**收取**邮件的标准协议。POP3协议允许你将邮件内容下载到本地电脑，离线阅读。需要注意的是，使用POP3协议，邮件收取下载到本地后，远程服务端的邮件会被移除。使用POP3的**好处**：邮件一经下载到本地，远程服务端会删除，减少了邮箱服务端存储邮件内容的空间；**弊端**：对多应用端配置同一个邮箱不友好，你在多台设备上查看邮件，可能会因为前面的某一设备下载了，后面的设备就看不到这个邮件了。
* 默认情况下，POP3可选用以下2个端口：
    * 端口110：默认设置端口，不加密；
    * 端口995：如果你期望加密安全，使用这个端口。

## IMAP协议内容及端口
* IMAP(Internet Message Access Protocol)是与POP3协议极为类似的**收取**邮件的协议，类似云存储，异地异端可同时获取。最大的区别是：IMAP协议允许同时多个客户端从邮箱远程服务端收取邮件，邮件最终存储在远程服务端，本地仅是缓存的邮件，适用于多端同一邮箱场景使用。
* 默认情况下，IMAP可选用以下2个端口：
    * 端口143：默认设置端口，不加密；
    * 端口993：如果你期望加密安全，使用这个端口。

## SMTP协议内容及端口
* SMTP(Simple Mail Transfer Protocol)是标准的互联网邮件**发送**协议。
* 默认情况下，SMTP可选用以下3个端口：
    * 端口25：默认设置端口，不加密；
    * 端口2525：备用设置端口，当托管服务器25被屏蔽时使用，不加密；
    * 端口465：加密发送邮件协议端口。

## 个人画外音
* 写这篇文章原因是，身边有同事邮件莫名其名丢了，主要原因是使用了POP3协议收取邮件，次要原因是办公硬盘小，有时候清理～误操作。建议大家收取邮件都选用协议 IMAP，993端口。SMTP发送邮件选用465端口。


----
## 英文原文
* [Email Protocols - POP3, SMTP and IMAP Tutorial](https://www.siteground.com/tutorials/email/protocols-pop3-smtp-imap/#What_is_POP3_and_which_are_the_default_POP3_ports)
