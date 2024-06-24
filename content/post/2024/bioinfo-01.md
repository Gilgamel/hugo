---
title: "Bioinfo | "
description: 
date: 2024-06-23T17:48:53-07:00
image: 
math:
license: 
categories:
- Bioinformatics
tags:
hidden: false
comments: true
draft: false
summary:
---


```
rm(list = ls()) 
options()$repos 
options()$BioC_mirror
#options(BioC_mirror="https://mirrors.ustc.edu.cn/bioc/")
options(BioC_mirror="http://mirrors.tuna.tsinghua.edu.cn/bioconductor/")
options("repos" = c(CRAN="https://mirrors.tuna.tsinghua.edu.cn/CRAN/"))
options()$repos 
options()$BioC_mirror

# https://bioconductor.org/packages/release/bioc/html/GEOquery.html
if (!requireNamespace("BiocManager", quietly = TRUE))
 install.packages("BiocManager") 
BiocManager::install(c("GSEABase","GSVA","clusterProfiler" ),ask = F,update = F)
BiocManager::install(c("GEOquery","limma","impute" ),ask = F,update = F)
BiocManager::install(c("org.Hs.eg.db","hgu133plus2.db" ),ask = F,update = F)

# 下面代码被我注释了，意思是这些代码不需要运行，因为它过时了，很多旧教程就忽略
# 在代码前面加上 # 这个符号，代码代码被注释，意思是不会被运行
# source("https://bioconductor.org/biocLite.R") 
# library('BiocInstaller') 
# options(BioC_mirror="https://mirrors.ustc.edu.cn/bioc/") 
# BiocInstaller::biocLite("GEOquery")
# BiocInstaller::biocLite(c("limma"))
# BiocInstaller::biocLite(c("impute"))

# 但是接下来的代码又需要运行啦
options()$repos
install.packages('WGCNA')
install.packages(c("FactoMineR", "factoextra"))
install.packages(c("ggplot2", "pheatmap","ggpubr"))
library("FactoMineR")
library("factoextra")

library(GSEABase)
library(GSVA)
library(clusterProfiler)
library(ggplot2)
library(ggpubr)
library(hgu133plus2.db)
library(limma)
library(org.Hs.eg.db)
library(pheatmap)
```


# Ref
> [你真的需要这些R视频吗](http://www.bio-info-trainee.com/6431.html)
> [生信准备工作](http://www.bio-info-trainee.com/3727.html)
> [2020学习主旋律B站74小时免费教学视频为你领路](http://www.bio-info-trainee.com/6160.html)
> [生信分析人员如何系统入门Linux(2019更新版)](https://mp.weixin.qq.com/s/2RqjIWEARgMmk3h_sAYtUw?poc_token=HOvDeGaj98J8G6wmZufkSZbwvmMo8osT5htATbPe)
> [生信分析人员如何系统入门R(2019更新版)](https://mp.weixin.qq.com/s?__biz=MzAxMDkxODM1Ng==&mid=2247491094&idx=1&sn=3a8ececefdf5894f4ef98b0823f204b8&scene=21#wechat_redirect)
> [Jimmy GEO GIthub](https://github.com/jmzeng1314/GEO)
> [生信菜鸟团](http://www.bio-info-trainee.com/)
> [生物统计学专题 -StatQuest教学视频学习笔记](https://mp.weixin.qq.com/s?__biz=MzAxMDkxODM1Ng==&mid=2247487662&idx=1&sn=a52862807122261813a23d9cb16bfafb&chksm=9b485015ac3fd9031def60d523a29cc0e4765b5a3b161b1ff4f4d679453f563ee50d6f84ba4c&scene=21#wechat_redirect)
> [STATQUEST](https://statquest.org/video-index/)
