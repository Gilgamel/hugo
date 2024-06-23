---
title: "Hugo | 利用 github 和 cloudflare 创建 portfolio page"
description: 
date: 2024-06-18T05:15:04-07:00
image: 
math:
license: 
categories:
tags:
- hugo
hidden: false
comments: true
draft: false
summary:
---

萌生创建 portfolio 的想法来自于在 indeed 上投简历后，招聘方（国人）说岗位要求有 coding skill，要求把自己的网站发送给对方看。但在那之前我的日常记录也好，做数据等方面遇到的问题及解决方式也有，通通都方在了这个blog上了，如果将这个 blog 发给对方，会让自己有一种 ”底裤都被看光了“ 的感觉。因此决定新建一个 hugo site 作为展示 portfolio 的地方，同时也将其全英文化。

在这的过程中看到了 github repo + github action 的形式，也有 github repo + cloudflare 的形式。在网上搜了一下，发现即使国内访问 cloudflare 的速度也不低，且同时能接触一些新的东西，遂用后者方案。

# 架设
## 本地部署
 
 由于是 Mac，所以需要安装 git、go 等，一切都跟着 hugo 网站走即可。
 跟创建这个 blog 不一样的时，安装主题的代码从 git clone 变成了 git submodule add
 
 在这里由于我选了 hugo-coder 这个主题，因此直接将代码替换成如下：
 
 ```
 git init
 git submodule add https://github.com/luizdepra/hugo-coder.git themes/hugo-coder
echo "theme = 'hugo-coder'" >> hugo.toml
hugo server
 ```
 
替换完毕以后利用 hugo server 在本地观察了一下，发现 hugo-coder 的 hugo.toml 基本是空白，一切均需要自己自定义。利用了作者提供的 minimal configuration 作为模版，按照自己的需求修改了一下，作者的 hugo.toml 代码如下：

```
baseurl = "http://www.example.com"
title = "johndoe"
theme = "hugo-coder"
languagecode = "en"
defaultcontentlanguage = "en"

paginate = 20

[services]
[services.disqus]
disqusShortname = "yourdiscussshortname"

[markup.highlight]
style = "github-dark"

[params]
  author = "John Doe"
  info = "Full Stack DevOps and Magician"
  description = "John Doe's personal website"
  keywords = "blog,developer,personal"
  avatarurl = "images/avatar.jpg"
  #gravatar = "john.doe@example.com"

  faviconSVG = "/img/favicon.svg"
  favicon_32 = "/img/favicon-32x32.png"
  favicon_16 = "/img/favicon-16x16.png"

  since = 2019

  enableTwemoji = true

  colorScheme = "auto"
  hidecolorschemetoggle = false

  # customCSS = ["css/custom.css"]
  # customSCSS = ["scss/custom.scss"]
  # customJS = ["js/custom.js"]

[taxonomies]
  category = "categories"
  series = "series"
  tag = "tags"
  author = "authors"

# Social links
[[params.social]]
  name = "Github"
  icon = "fa-brands fa-github fa-2x"
  weight = 1
  url = "https://github.com/johndoe/"
[[params.social]]
  name = "Gitlab"
  icon = "fa-brands fa-gitlab fa-2x"
  weight = 2
  url = "https://gitlab.com/johndoe/"
[[params.social]]
  name = "Twitter"
  icon = "fa-brands fa-x-twitter fa-2x"
  weight = 3
  url = "https://twitter.com/johndoe/"

# Menu links
[[menu.main]]
  name = "Blog"
  weight = 1
  url  = "posts/"
[[menu.main]]
  name = "About"
  weight = 2
  url = "about/"
```

## 利用 github desktop 推送
按照 hugo 网站的说明，本地创建好文件后需要 push 到 github 上，但 hugo 本身提供的 push 方法会提示 GitHub 在 2021 年以后不再支持在 terminal 输入账号密码，需要用其他方法设置。

于是我选择了最简单的去代码法法：GitHub desktop 
在 github desktop 中选择 add repo - 选择 portfolio 文件夹， 选择 publish，默认勾选上设为 private repo 确认便会在 GitHub 自动新建 repo 并上传文件。

## 在 cloudflare 上创建 page

登陆 cloudflare 后，选择 workers and pages -> create page -> connect to github 并选择 portfolio repo 如下图：

![](https://raw.githubusercontent.com/Gilgamel/img-host/main/hugo/2024/cloudflare-1.png)

- framework preset: Hugo
- Build command: hugo
- Build output directory: 默认为 public

以上均是默认的，在这里的重点是需要在 environment variables 添加 HUGO_VERSION 及对应版本。截止到目前为止的 hugo version 为 0.127.0，因此我设置如下图：

![](https://raw.githubusercontent.com/Gilgamel/img-host/main/hugo/2024/cloudflare-2.png)

最后就是等部署成功了
![](https://raw.githubusercontent.com/Gilgamel/img-host/main/hugo/2024/cloudflare-3.png)


# 将content - post 中的路径略去

由于 portfolio 我主要想通过 menu 的方式来展示自己，例如有 projects page， experiences page，但像 projects 的话有考虑要不要在里面根据不同的 projects 创建不同的 post，因此在 contents 文件夹下创建了不同的文件，如 about、experiences、projects

当在about文件夹下创建 about.md 后浏览路径就会变成 xxxx/about/about，十分奇怪。找了许多方法，在这个过程中也了解了 leaf bundle是什么，index.md 跟 _index.md 的区别等等，最后参考了 stack overflow 上的解决方法，在 content/_index.md 写上以下代码：

```
---
cascade:
  _build:
    render: always
    list: always

_build:
  render: never
  list: never
---
```

过后发现成功缩短了文件路径，但会出现了 render 失效的情况，也就是在修改完内容后不管是 hugo server，还是直接推送，public/index.html 的内容都不会修改。经过 GPT DEBUG，发现了在 content/_index.md 加入以上代码会导致子页面可刷新，但主页无法刷新，因此将代码重新改：

```
---
title: "Home"
_build:
  render: always
  list: always
---

```

至此，问题解决


Ref:
[Cloudflare Framework Guide - Hugo](https://developers.cloudflare.com/pages/framework-guides/deploy-a-hugo-site/)
[将Hugo博客部署到Cloudflare Pages上的体验与踩坑小记](https://tutuis.me/deploy-blog-to-cloudflare-pages/)
[Hugo Page bundles](https://gohugo.io/content-management/page-bundles/)
[Hugo URL management](https://gohugo.io/content-management/urls/#permalinks)
[Creating a Layout for Content with multiple Subfolders?](https://discourse.gohugo.io/t/creating-a-layout-for-content-with-multiple-subfolders/45618)
[Trying to get rid of some index pages](https://discourse.gohugo.io/t/trying-to-get-rid-of-some-index-pages/41855)
[Subpages without _index.md file](https://stackoverflow.com/questions/67076616/subpages-without-index-md-file)