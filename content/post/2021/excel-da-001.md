---
title: "Excel | 用 Excel 进行时间、分类统计"
description: 
date: 2021-10-25T01:57:05-04:00
image: 
math:
license: 
categories:
tags:
- Excel
- analysis
hidden: false
comments: true
draft: false
summary:
---

## 在分析过程中使用到的相关函数及功能
1. Pivottable 
2. Split Cells
3. Date 函数
4. DATEDIF 函数
5. IF 函数

## 分析需求
根据要求，需要针对数据提供以下几项分析：
- Submit 时间分析：每个月的数据量，分析出来高峰期在什么时候
- Shipment 时间分析：可以关注下 submit-shipment 直接的时间先后顺序 lead time
- 区域分析：区域数据量
- 患者年龄段：
    - genz：1997 之后
    - sophiscated：1986 - 1997 之间
    - senior：1986 之前
- 客户性别比例
- 治疗步数分析：
    - 20步
    - 20-40
    - 40-60
    - 60以上

</br>

## EXCEL 操作
### Submit Date 时间分析

根据提供的算法，在 RAW DATA 里找到 submit date，并发现已提供了 **submit year & submit month**，不需要再做任何的数据处理，直接即可应用

![](https://raw.githubusercontent.com/Gilgamel/img-host/main/hugo/2021/1635392316886.png)

<font color=LightSeaGreen> submit month value 每出现一次，则代表一次订单产生，因此要知道每个月的订单数量，只需要通过 pivottable 统计 submit month 出现的次数即可。</font> 

方法如下：
1. 选中 submit month & submit year 两列
2. 选择菜单栏上的 插入 → 数据透视表 （英文版则为 Insert → PivotTable）
3. 弹出“来自表格或区域的数据透视表”框后，选择新工作表，然后点确定 （见下图）
![](https://raw.githubusercontent.com/Gilgamel/img-host/main/hugo/2021/1635392984103.png)
4. 在新建工作表上点击“数据透视表”，右侧会出现数据透视表字段，将 submit year & submit month 拖拽到相应位置即可
![](https://raw.githubusercontent.com/Gilgamel/img-host/main/hugo/2021/1635393091985.png)
5. 此时新的工作表上即出现了关于 submit date 的相关统计数据

</br>

### Shipment Date 时间分析
根据提出的需求，关于 shipment date 的时间分析有两个方面：
1. shipment 高峰期
2. submit date - shipment date 的间隔时间 （一般这方面的分析用于研究、衡量公司从接收到订单到出货的时间，从间距时间可以反映出公司内部的流程是否通顺，也可在精细化运营时使用。

通过 DATEDIF 公式可以计算出两个日期的相差值

```
DATEDIF(submit_date,shipment_date,"d")

# d = day

```
![](https://raw.githubusercontent.com/Gilgamel/img-host/main/hugo/2021/1635396999931.png)

#### 日期格式无法修改
针对修改格式但没反应的情况，在确认单元格已经去掉锁定后，**用分列 → 合并的思路进行处理**，具体操作如下；

1. 选择**数据 → 分列**，分隔符选其他，输入“-”，点击下一步
![](https://raw.githubusercontent.com/Gilgamel/img-host/main/hugo/2021/1635399718240.png)

2. 列数据格式选择日期，并根据原有的日期格式选择 DMY，目标区域需要选择一个新的空列，然后选择完成
![](https://raw.githubusercontent.com/Gilgamel/img-host/main/hugo/2021/1635399794604.png)

在这个部分处理时，我直接新建了3个空列，避免拆分出来的数据将其他已有数据覆盖掉。

3. 通过 DATE 函数将拆分出来的日期重新合并。 
![](https://raw.githubusercontent.com/Gilgamel/img-host/main/hugo/2021/1635399883980.png)

</br>

### 区域分析、患者性别比例分析
这两个部分由于不需要针对数据进行处理，直接插入 pivottable 进行统计即可，方法跟 submit date 部分一样。

</br>

### 患者年龄段
根据需求需要将所有客户的年龄分为 3 个种类：
    - genz：1997 之后
    - sophiscated：1986 - 1997 之间
    - senior：1986 之前

由于 RAW DATE 里客户出生日期是具体到天的，因此需要将出生年份拆分出来。
这个时候可以直接**用 RIGHT 函数选取客户出生日期的后 4 位。**

![](https://raw.githubusercontent.com/Gilgamel/img-host/main/hugo/2021/1635400640771.png)
**记得将公式值复制黏贴为数字格式**

之后可以用 IF 函数对客户的出生年份进行判断，即：
- genz：x > 1997 （因为在1997之后出生的日期，如 1998、1999 均大于 1997）
- sophiscated： 1986 < x < 1997 
- senior：x < 1986 （在1986之前出生的日期，加 1985、1984 均小于 1986）

由于判断条件有3个，因此需要用 IF 函数进行套娃，而 IF 函数是采取递进关系的，因此年龄的判断先后 MATTERS。

```
=IF(X>1997,"genz",IF(X>1985,"sophiscated","senior"))

# 如果 X > 1997，将它命名为 genz；
# 否则如果 X > 1985，将它命名为 sophiscated；
# 否则则将它命名为 senior
```

**因为 RAW DATA 中有少量 null 值，在分类后复制黏贴值以后直接将关于 null 的错误种类手动修改，避免疯狂使用 IF函数进行套娃出现错误。**

分类完成后使用 pivottable 进行统计即可。

</br>

### 治疗步数分析
**已知：**
1. 步数后面是单颌，需要*2=上下颌才是整体步数
2. RAW DATA 中有上下颌分类说明
3. RAW DATA 中的步数为单颌步数 （即非完整步数）

**所以：**
1. 需要将整体的步数通过上下颌计算出来
2. 针对完整步数对步数进行分类


#### 计算整体步数
通过 IF 函数将上下颌列为筛选条件：

```
=IF(X="BOTH",X*2,X)

# 如果是 BOTH，则步数*2，否则则保持原样
```
**随后将函数值复制黏贴为值即可针对整体步数进行分类。**

根据需求可以将分类逻辑整理如下：
    - 20及以下：x < 20
    - 20-40：19 < x < 41
    - 40-60：39 < x < 61
    - 60以上：x > 60

再通过 IF 函数疯狂套娃：

``` 
=IF(X>60,"60以上",IF(X>39,"40-60",IF(X>19,"20-40","20及以下")))

# 如果 X > 60，则将它命名为 60以上
# 如果 X > 39，则将它命名为 40 - 60
# 如果 X > 19， 则将它命名为 20 - 40
# 否则将名为 20及以下
```

同理最后复制黏贴值后，将 null 值手动替换则完成所有分类
最后再通过 pitvottable 统计即可。

</br>

## Ref
> https://support.microsoft.com/en-us/office/datedif-function-25dba1a4-2812-480b-84dd-8b32a451b35c
> https://support.microsoft.com/zh-cn/office/date-%E5%87%BD%E6%95%B0-e36c0c8c-4104-49da-ab83-82328b832349
