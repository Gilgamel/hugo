---
title: "Hugo | Hugo-PaperMod 主题修改"
description: 基于 Hugo-PaperMod 主题上装修
date: 2022-09-17T22:24:59-04:00
image: 
math:
license: 
categories:
tags:
- Hugo
  # summary->在列表页展现的摘要内容，自动生成，内容默认前70个字符，可通过此参数自定义，一般无需专门设置
hidden: false
comments: true
draft: false
---
<!--more-->

# 在文章页面添加字数统计

打开任意文章页面，右击选择 “检查” （或 F12），点击 elements。
利用 Devtools 定位相关位置 （PaperMod 主题在 post-meta 位置）如下图：

![](https://raw.githubusercontent.com/Gilgamel/img-host/main/hugo/20220918012845.png)

用 VS 打开 single.html，目录如下：
themes - PaperMod - layouts - _default - single.html

![](https://raw.githubusercontent.com/Gilgamel/img-host/main/hugo/20220918012552.png)

CTRL + F 快速搜索 post-meta class 

![](https://raw.githubusercontent.com/Gilgamel/img-host/main/hugo/20220918013200.png)

在 post-meta 内插入以下代码：

``` rust {linenos=table}
<div class="article-read-time"> 🐈‍ {{ .WordCount }} words</div>
    </div>
```

EMOJI 位置可任意修改文字、EMOJI 等


# 自定义文章模板

找到 hugo 根目录下的 archetypes 文件夹，打开 default.md 并修改成自己想要的模板。

![](https://raw.githubusercontent.com/Gilgamel/img-host/main/hugo/20220917230200.png)

**Before:**
``` rust {linenos=table}
---
title: "{{ replace .Name "-" " " | title }}"
date: {{ .Date }}
draft: false
---

```

**After:**

```rust {linenos=table}
---
title: "{{ replace .Name "-" " " | title }}"
description: 
date: {{ .Date }}
image: 
math:
license: 
categories:
tags:
hidden: false
comments: true
draft: false
---
```

# 修改页尾信息

打开相关主题文件下的 footer.html 进行修改

themes - PaperMod - layouts - partials - footer.html

找到 class = "footer"，如下：

**Before**

``` rust {linenos=table}
<footer class="footer">

    {{- if site.Copyright }}
    <span>{{ site.Copyright | markdownify }}</span>
    {{- else }}
    <span>&copy; {{ now.Year }} <a href="{{ "" | absLangURL }}">{{ site.Title }}</a></span>
    {{- end }}
    <span>
        Powered by
        <a href="https://gohugo.io/" rel="noopener noreferrer" target="_blank">Hugo</a> &
        <a href="https://github.com/adityatelange/hugo-PaperMod/" rel="noopener" target="_blank">PaperMod</a>
    </span>
    <span>

    </span>
</footer>
{{- end }}
```

复制黏贴以下代码：

``` rust {linenos=table}


{{$scratch := newScratch}}
{{ range (where .Site.Pages "Kind" "page" )}}
{{$scratch.Add "total" .WordCount}}
{{ end }}


    {{- if site.Copyright }}
    <span>{{ site.Copyright | markdownify }}</span>
    {{- else }}
    <span>&copy; 2022 - {{ now.Year }} <a href="{{ "" | absLangURL }}">{{ site.Title }}</a> · 
        <i class="fas fa-bell"></i> <a id="days">0</a>Days</br></span>
    {{- end }}
    {{$var :=  $scratch.Get "total"}}{{$var = div $var 100.0}}{{$var = math.Ceil $var}}{{$var = div $var 10.0}}共书写了{{$var}}k字 · 共 {{ len (where .Site.RegularPages "Section" "post") }}篇文章</br><span>
    <span>
        Powered by
        <a href="https://gohugo.io/" rel="noopener noreferrer" target="_blank">Hugo</a> &
        <a href="https://github.com/adityatelange/hugo-PaperMod/" rel="noopener" target="_blank">PaperMod</a></br>
    </span>

    <script>
        var s1 = '2022-04-05';//设置为建站时间
        s1 = new Date(s1.replace(/-/g, "/"));
        s2 = new Date();
        var days = s2.getTime() - s1.getTime();
        var number_of_days = parseInt(days / (1000 * 60 * 60 * 24));
        document.getElementById('days').innerHTML = number_of_days;
    </script>


```

最终效果如下：

![](https://raw.githubusercontent.com/Gilgamel/img-host/main/hugo/20220920171617.png)


# Ref
> [stack theme mod](https://mantyke.icu/posts/2022/stack-theme-mod/)
> [hugo papermod](https://github.com/adityatelange/hugo-PaperMod)
> [hugo 部署个人博客 2 (config.yaml 设置)](https://333rd.net/posts/tech/hugo%E9%83%A8%E7%BD%B2%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A22/)
> [字数统计](https://mogeko.me/posts/zh-cn/033/)
