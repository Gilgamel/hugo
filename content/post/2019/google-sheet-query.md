---
title: "Google Sheets | 在Google Sheets 上使用query、if、isna将符合条件的分表数据汇总到总表"
description: 
date: 2019-08-18T12:35:27-04:00
image: 
math:
license: 
categories:
- 新建就是成功
tags:
- query
- google sheets
hidden: false
comments: true
draft: false
summary:
---

因为工作的缘故，需要将各个平台的宣传日期都放在一张google sheet上，然后 **通过检测关键词，将关键的event自动pop up到总表上**。得到这个需求的时候，脑海里立刻想到的是VBA，然而google sheet上压根没VBA啊！
</br>
于是想着是否能用 vlookup/hlookup/index & match解决问题，最后发现果然还是解决不了：）最后在朋友的提醒下，开始摸索着使用 **query**。

## Query

根据 Google 简陋的说明，query 是对数据运行一项采用 Google Visualization API 查询语言的查询。


### 用法示例

```Excel
QUERY(A2:E6,"select avg(A) pivot B")

QUERY(A2:E6,F2,FALSE)
```

### 语法

**Query（数据，查询，[标题数]）**

- **数据** - 要执行查询的单元格范围。
  - 每列**数据**中的数值类型只能是布尔值、数字（包括日期/时间类型）或字符串。
  - 如果有多种数据类型掺杂在单个列中，为便于查询，该列的数据类型由其中大多数数据的类型决定。其他少数数据的类型则被视为空值。
- **查询** - 要执行的查询操作，以 **Google Visualization API查询语言**编写。
  - **查询**的值必须括在**引号**中，否则该值必须是指向包含相关文本的单元格的引用。
- **标题数** - [可选]- `数据`上方的标题行的数目。如果省略或设置为`-1`，将根据**数据**的内容估算该值。

### 可以怎么用？

| name  | dept      | lunchTime | salary | hireDate   | age  | isSenior | seniorityStartTime  |
| ----- | --------- | --------- | ------ | ---------- | ---- | -------- | ------------------- |
| John  | Eng       | 12:00:00  | 1000   | 3/19/2005  | 35   | TRUE     | 12/2/2007 15:56:00  |
| Dave  | Eng       | 12:00:00  | 500    | 4/19/2006  | 27   | FALSE    | null                |
| Sally | Eng       | 13:00:00  | 600    | 10/10/2005 | 30   | FALSE    | null                |
| Ben   | Sales     | 12:00:00  | 400    | 10/10/2002 | 32   | TRUE     | 3/9/2005 12:30:00   |
| Dana  | Sales     | 12:00:00  | 350    | 9/8/2004   | 25   | FALSE    | null                |
| Mike  | Marketing | 13:00:00  | 800    | 1/10/2005  | 24   | TRUE     | 12/30/2007 14:40:00 |



1. 使用**Select**和**Where**返回满足指定条件的行。

```Excel
QUERY('Example Data'!$A$2:$H$7, "select A where (B<>'Eng' and G=true) or (D > "&A2&")")
```

2. 使用**Select**和**Group by**子句统计所有行的**工资**数据

```Excel
QUERY('Example Data'!$A$2:$H$7, "select B, MAX(D) group by B")
```

3. 将各列不重复的值转换到新列中。

```Excel
QUERY('Example Data'!$A$2:$H$7, "select avg(D) pivot B")
```

4. 统计各行的**部门**值，并按**工资**的最大值进行排序。

```Excel
QUERY('Example Data'!$A$2:$H$7, "select B, MAX(D) group by B order by MAX(D)")
```

5. 指定输入范围中的标题行数目，这让函数能够将带多个标题行的输入转换为单个标题行的输入。

```Excel
# Male
QUERY(A1:E4, "select B where (D>175 and E>53)", 1)

# Male height
QUERY(A1:E4, "select B where (D>175 and E>53)", 2)
```

其实以上都不是重点，只是我想Mark一下而已。



## 利用 Query 、Where、Contains 自从筛选出符合条件的 Event

由于当时设计timeline只考虑到好看（easy to read）的问题，所以基本上将 Google Sheet 当成了画板使用，这也导致了 timeline 的设计从数据分析的逻辑上来看是十分辣鸡的；但木得办法，老板要“好看”，所以只能基于原有timeline的基础上去做到老板想要的需求。

以下为“好看”（easy to read) 的timeline示例：

![image.png](https://i.loli.net/2019/08/23/OvHFniK8xLrat4f.png)

考虑到 Google Sheet 只能默认某一列的日期是一致的，所以没办法一口气将所有区域选上并且进行筛选，所以只能用一个笨方法：将range设置为一周。

举个例子：假设12月23日 - 12月29日的 range 为 C174:I180，Monday 代表C列，我需要在总表上代表12月23日的一格输入以下代码：

```Excel
query(xxxx!$C$174:$I$180,"select C where C contains '线下'")

# 在$C$174:$I$180的范围内，自从标记出在C列里包含着线下这两个关键字的内容
# 同理线下可以切换为线上或执行
```

到了这一步，问题得到了解决。

但新的问题又出现了



### 用 isna 、if function 将在没有关键性 Event 的格子里会出现 #N/A 提示去掉

这个提示是由 query function 直接产生的，所以无法使用 conditional format 进行标白处理，因此在朋友的提示下，我选择使用 **isna** function.

**isna** function 是用来检查某个值是否为错误值 #N/A 的。

- **ISNA（值）**
  - **值** - 要与错误值 #N/A 进行比较的值
  - 如果**值**为**#N/A**或指向包含**#N/A**的单元格的引用，则**ISNA**将返回TRUE，否则返回FALSE

```Excel
isna(query(UTSG!$M$4:$S$10,"select O where O contains '线下'"))

# 检测 query(UTSG!$M$4:$S$10,"select O where O contains '线下'") 的返回值是否为 #N/A， 如果是返回TRUE， 否则返回 FALSE
```

当 query function 指向 #N/A 时，会自动返回 TRUE， 此时已达到将 #N/A 去掉的目的。之后可配合 **if function** 将TURE值转换为空格。

```Excel
if(isna(query(UTSG!$M$4:$S$10,"select O where O contains '线下'"))," ", query(UTSG!$M$4:$S$10,"select O where O contains '线下'"))

# 如果 ISNA 返回的值为TURE，则将TRUE 变为空格，否则显示qury function 返回值。
```

至此，老板的需求已经完成。让我大吼一句 Google Sheet 真好玩。



### 其他

在Google Sheet 上可使用 CTRL + ` 来显示出 formula，然后使用 formula replace 一键替换公式里的关键词。 

Ref:

> https://support.google.com/docs/answer/3093343?hl=zh-Hans
>
> https://support.google.com/docs/answer/3093293?hl=zh-Hans
>
> https://infoinspired.com/google-docs/spreadsheet/contains-substring-match-in-google-sheets-query-for-partial-match/