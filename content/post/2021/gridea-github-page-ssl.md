---
title: "Gridea | 为 Gridea Github Page 自定义域名并开启 https"
description: 
date: 2021-02-21T5:25:53-04:00
image: 
math:
license: 
categories:
tags:
- Github
- SSL
- Gridea
hidden: false
comments: true
draft: false
summary:
---

最近发现了 Gridea 这个静态博客，感觉十分方便，重新将持续性写 blog 提上日程

就算是2021年立一个 flag 吧。

Gridea 真的是一个非常方便的静态博客，连自定义域名的方式都非常的傻瓜。

## 具体操作

在 Gridea 左侧选择**远程**，然后直接在 **CNAME** 输入自己的域名，点击保存同步即可。

![gridea_setting.png](https://i.loli.net/2021/02/26/q2FPwmiz7ajsS3O.png)


登陆 GITHUB，看到多了一个 CNAME 文件即代表同步成功。

点击 **Setting** 拖到下方，确认域名已经被填写进去，并且 **ENFORCE HTTPS** 已被勾选成功。

![GITHUB_PAGE_SETTING.png](https://i.loli.net/2021/02/26/zyaxo9NQlfJVB7L.png)


## 其他

其实一开始有在搜了很多教程，包括在 github page 直接 create new file CNAME， 然后输入域名。

后来发现这个操作在 Gridea 更新同步后，CNAME file 会被删除，因此作罢。

其后试过直接在 Gridea 源目录下新建 CNAME file，但发现同步不成功。

最后发现 Gridea 十分贴心的在客户端已经自带了设置。

