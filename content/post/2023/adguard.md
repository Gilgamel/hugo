---
title: "Adguard | VPS 安装 AdGuard Home 为全屋设备过滤广告"
description: 
date: 2023-07-01T02:32:27-04:00
image: 
math:
license: 
categories:
- 新建就是成功
tags:
- adguard home
hidden: false
comments: true
draft: false
summary:
---

楼下的电视APP更新以后开始出现广告了，于是一直在思考着有没有什么办法能够过滤全屋设备的广告，做了一番搜索以后再次将 AdGuard 翻了出来。之前已经购买过 AdGuard Ad Blocker，这次看上的是开源的 [AdGuard Home](https://adguard.com/en/adguard-home/overview.html)。AdGuard Home 作为一款全网广告拦截与反跟踪软件，可以在一次安装完成后，通过修改路由DNS从而让连接上特定网络的设备均受到保护。

</br>

关于安装 AdGuard Home 的位置，一开始选择的是在 QNAP NAS 上进行，但后来反复试了很多次均失败。于是趁着美国独立日，在 RackNerd 上购入年费约为 $16 加币的廉价 VPS。

# 安装

根据 AdGuard Home Github 的教程，在连接 VPS 以后直接输入以下代码：

```
curl -s -S -L https://raw.githubusercontent.com/AdguardTeam/AdGuardHome/master/scripts/install.sh | sh -s -- -v

```

在显示安装完成后（如下图），直接启动 AdGuard 
![](https://raw.githubusercontent.com/Gilgamel/img-host/main/hugo/2023/20230702004621.png)

```
sudo /opt/AdGuardHome/AdGuardHome -s start
```

接着就可以在浏览器输入 VPS IP:3000 进行配置

# 配置 AdGuard Home

将网页监听端口改成3000，或许是其他自己喜欢的端口。比较重要的是 DNS 服务器端口。DNS 服务器端口默认是 53，如果想要设置双 AdGuard 进行国内外分流的话，需要用两个端口。但由于家里使用的是 BELL 自带的路由，无法修改具体端口，因此只创建一个 AdGuard，使用默认端口 53。

而在使用默认端口 53时，经常会出现“validating ports: listen tcp 0.0.0.0:53: bind: address already in use” 的情况（如下图）

![](https://raw.githubusercontent.com/Gilgamel/img-host/main/hugo/2023/20230702004938.png)

此时只需要输入以下代码即可：

```
systemctl stop systemd-resolved  
systemctl disable systemd-resolved  

#删除后重新创建resolv.conf
rm -rf /etc/resolv.conf
```

</br>

## 常规设置

勾选 **使用过滤器和 Hosts 文件以拦截指定域名** 以及 **使用 AdGuard 【浏览安全】网页服务**，查询日志保留时间跟统计保留根据个人喜好，选择了7天。

</br>

## DNS 设置

查看了很多 AdGuard Home 设置相关的文章，最后由于使劲主场景在国外，因此在上游 DNS 仅设置以下三个：

![](https://raw.githubusercontent.com/Gilgamel/img-host/main/hugo/2023/20230701231409.png)

</br>

海外上游 DNS
> 1.1.1.1 # Cloudflare  
> tls://dns.google  
> https://dns.google/dns-query  

海外 Bootstrap DNS
> 9.9.9.10
> 149.112.112.10

国内上游 DNS  
> 119.29.29.29  
> 119.28.28.28  
> 223.5.5.5  
> 223.6.6.6

国内 Bootstrap DNS
> 119.29.29.29  
> 119.28.28.28  
> 223.5.5.5  
> 223.6.6.6


</br>

## 过滤规则

过滤规则除了默认的 AdGuard DNS filter 以外，针对中文网站添加了 CHN: anti-AD，以及 Halflife PC 版及移动端版。PC 版合并自乘风视频广告过滤规则、Easylist、EasylistChina、EasyPrivacy、CJX'sAnnoyance，以及补充了一些规则。而移动端版则合并自乘风视频广告过滤规则、EasylistChina、EasylistLite、CJX'sAnnoyance，以及补充的一些规则。

Halflife-list 之前更新在 gitee，而目前则更新在 [halflife-list on github](https://github.com/sbwml/halflife-list)，但具体文件在 jsdelivr。


PC LIST：  
https://cdn.jsdelivr.net/gh/sbwml/halflife-list@master/ad-pc.txt

MOBILE LIST:  
https://cdn.jsdelivr.net/gh/sbwml/halflife-list@master/ad.txt

AD EDENTW LIST （合并自Adblock Warning Removal List、ABP filters、anti-adblock-killer-filters）：

https://cdn.jsdelivr.net/gh/sbwml/halflife-list@master/ad-edentw.txt

</br>

## 设备设置

为了避免全屋设备挨个设置，最终选择在 BELL 路由器上修改 DNS，方法如下：

1. 浏览器输入 192.168.2.1 选择 Advanced tools and settings，并输入密码登入 (默认密码是路由上的S/N)
2. 选择 DNS
![](https://raw.githubusercontent.com/Gilgamel/img-host/main/hugo/2023/20230701231004.png)
3. 选择 Manually specify DNS information，接着输入自己的 VPS IP 地址，选择 Change
![](https://raw.githubusercontent.com/Gilgamel/img-host/main/hugo/2023/20230701231039.png)


至此，AdGuard 已经开始运作

![](https://raw.githubusercontent.com/Gilgamel/img-host/main/hugo/2023/20230702011200.png)

</br>

# 遇到的问题

一开始选择的是利用 docker 安装 Adguard，在前面一切正常，但在新增设备时一直出现 error，搜了一下发现应该版本问题，因此最终还是选择了 github 直接安装。

另外还会出现一模一样的网页（比方说 google 搜索），电脑能够打开，但 android 无法打开的情况，出现的错误是 QUIC ERR，说是 google 通过 QUIC 推送广告，但 AdGuard 会过滤掉，可在 log 里无法找到 QUIC 相关明细。

观察一段时间卡看。


# Ref
> [AdGuard Home 安装及使用指北](https://sspai.com/post/63088)  
> [AdGuard Home 自建 DNS 防污染、去广告教程 #2 – 优化增强设置详解](https://www.jarods.org/2191.html)  
> [Set Up an AdGuard Home Server to Protect Multiple Locations on Linode](https://www.youtube.com/watch?v=z-QdOOR553I&t=1122s)  
> [AdGuard Home Github](https://github.com/AdguardTeam/AdGuardHome#getting-started)  
> [AdGuard Home 是什么？为什么无法去广告？](https://p3terx.com/archives/use-adguard-home-to-build-dns-to-prevent-pollution-and-remove-ads-0.html)  
>[Linux 53 端口被 systemd-resolve 占用](https://www.bujj.org/index.php/2022/01/28/285/)  

