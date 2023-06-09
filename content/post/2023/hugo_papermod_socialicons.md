---
title: "Hugo | 升级博客及主体版本，解决一些小 bug"
description: 
date: 2023-06-09T02:20:40-04:00
image: 
math:
license: 
categories:
tags:
- Hugo
hidden: false
comments: true
draft: false
summary: 一些个修复了 vercel 错误提醒之后，想更新主题做做装修，从而发现主题版本太高跟 hugo version 不匹配，于是更新 hugo 的故事
---


# Vercel Domain Error

这个 Hugo blog 是架设在 Github 上，但由 Vercel 进行推送的（当时有一个伟大的理想：想在 ipad 上不设置 Hugo 环境也能更新 blog，事实有点打脸）。最近 Vercel 一直往邮箱里发送了 Domain Error 的邮件，blog 也由于域名的原因无法访问了。  

之前一直以为是阿里云DNS的问题，但由于忘记了账号，所以一直摆烂没排查。今天再次打开 Vercel 的时候看到相关指引：让在 Domain DNS 里设置一条新的 CNAME 记录。怀着搏一搏单车变摩托的心态，绕开阿里云DNS，在 Godaddy DNS Setting 里直接添加一条新的记录如下：

> Type: CNAME  
> Name: blog  
> Data: cname.vercel-dns.com  
> TTL: 1 Hour  


设置过后三分钟，再尝试着打开 blog，发现恢复正常了！于是找回阿里云DNS账号的这个事情就再让我拖延一下吧。

</br>

# Hugo 更新

在更新完 PaperMod 的版本后，利用 hugo new 来创建新的 md file 已经提示版本不匹配了，于是又继续开始了一波新的更新。


在 Hugo Github 里找到 [Releases](https://github.com/gohugoio/hugo/releases)，点击进去以后在最新版本的 **Assets** 选择 **Show All**，接着点击 [hugo_extended_0.113.0_windows-amd64.zip
](https://github.com/gohugoio/hugo/releases/download/v0.113.0/hugo_extended_0.113.0_windows-amd64.zip) 下载（如果是 Windows 系统的话）

</br>

下载之后将文件夹里三个文件复制黏贴到原来 Hugo 的安装位置，当系统提示是否覆盖原有文件时选 “是”。

</br>

最后在安装位置打开 PowerShell 输入 **hugo version** 确认是否是最新版本即可（如下图）


![](https://raw.githubusercontent.com/Gilgamel/img-host/main/hugo/20230609025419.png)

</br>

# Ref
> [Hugo | 一起动手搭建个人博客吧](https://mantyke.icu/posts/2021/hugo-build-blog/)  
> [Hugo-PaperMod Installation](https://github.com/adityatelange/hugo-PaperMod/wiki/Installation)  

