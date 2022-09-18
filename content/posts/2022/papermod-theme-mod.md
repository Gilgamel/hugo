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
summary: ""
hidden: false
comments: true
draft: false
---

# 在文章页面添加字数统计




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


# Home-Info Mode



# Ref
> https://mantyke.icu/posts/2022/stack-theme-mod/
> https://github.com/adityatelange/hugo-PaperMod
> 