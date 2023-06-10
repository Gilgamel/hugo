---
title: "Google Sheets | 通过 Query、Importrange、Select、Where 将多张 Google Sheets 合并到一起"
description: 
date: 2021-04-04T04:20:46-04:00
image: 
math:
license: 
categories:
tags:
- google sheets
- query
- PBI
hidden: false
comments: true
draft: false
summary:
---

## 背景
公司每个学期所做的 Budget 都放在 Google Sheet，大概每2-4周会出现频繁修改的情况，因此需要实时保持更新并连接到 PBI。

在之前曾经试过 Custom Goolge Sheet Connector，但由于 Connector 只能在 Desktop 使用，会影响将报表 push 到 PBI Service 后的刷新；也曾经试过将 Power Query 写到 PBI Service 上，但由于 credential 问题，会导致后续无法刷新。

由于每个分公司的 Budget 都会在一张 spreadsheet 上，基于保密因此需要先将分公司的 Budget 有选择性地抓到同一张表上，再连接到 PBI Service 上。

</br>

## 总结
基于以上背景，可简单总结为以下两个目的：
1. Merge Multiple Sheets into One Sheet
2. Connect Goolge Sheet to PBI Service and Configure Scheduled Refresh

</br>

## STEP 1. Merge Multiple Sheets
### STEP 1.1 Filter + Importrange，失败的尝试
</br>

**Filter Synatax**
`FILTER(range, condition1, [condition2, ...])`
- **range** - The data to be filtered
- **condition1** - A column or row containing true or false values corresponding to the first column or row of range, or an array formula evaluating to true or false.

</br>

```
= {
    FILTER(IMPORTRANGE("{Spreadsheet_link1}","Sheet_name!A2:A"),
    LEN(IMPORTRANGE("{Spreadsheet_link},"Sheet_name!H2:H))>0;
    FILTER(IMPORTRANGE("{Spreadsheet_link2}","Sheet_name!A3:A"),
    LEN(IMPORTRANGE("{Spreadsheet_link2}","Sheet_name!H3:H"))>0)
}
```

</br>

- **{Spreadsheet_link1}** 分公司A的 Google Sheet Link
- **{Spreadsheet_link2}** 分公司B的 Google Sheet Link
- **Sheet_name1!A2:A**由于是第一次抓表，因此需要将标题也一同抓取。在这次的 Budget 表里第二行为标题，因此公司A的表格从A2开始抓起
- **Sheet_name2!A3:A**接着是 merge 公司B的表格，由于此时已不需要标题了，因此可以由 A3 开始抓起
- 通过 FILTER以及LEN 将已经没有填写完成的内容暂时放弃抓取，LEN里的 **filter** 判定行数需要跟 **importrange** 保持一致

</br>

到此为止，其实已经可以成功将各分公司的表格合并到一起。但出于信息安全保密问题，因此需要跳列只抓取最为重要的几列，以确保及时资料外泄对方也不知道具体产品的对应 Budget 是多少。

由于 filter 跟 importrange 均无法跳列，因此开始尝试使用 query + select + where + importrange 的组合进行数据抓取。

</br>

### STEP 1.2 Query + Importrange + Select + Where

**Query Syntax**
` Query(data, query, [headers])`
- **data** - The range of cells to perform the query on
- **query** - The query to perform, written in the Google Visualization API Query Language
- **headers** - [OPTIONAL] - The number of header rows at the top of data. If omitted or set to -1, the value is guessed based on the content of data

**Importrange Syntax**
`IMPORTRANGE(spreadsheet_url, range_string)`
- **spreadsheet_url** - The URL of the spreadsheet from where data will be imported
- **range_string** - A string, of the format "[sheet_name!]range" (e.g. "Sheet1!A2:B6" or "A2:B6") specifying the range to import

</br>


```
= {
    =QUERY({IMPORTRANGE("{Spreadsheet_ID1}",
    "Sheet_name1!A:V");
    IMPORTRANGE("{Spreadsheet_ID2}","Sheet_name2!A:V");
    IMPORTRANGE("{Spreadsheet_ID3}","Sheet_name3!A:V")},
    "SELECT Col1,Col8,Col10,Col16,Col17,Col18,Col20,Col22 WHERE Col10 > 0",2)
}
```

- **Spreadsheet_ID1** https://docs.google.com/spreadsheets/d/{Spreadsheet_ID}/edit#gid=0
    - 在写 Query 的过程中发现只填写 Spreadsheet_ID 也能成功读取
- SELECT ... WHERE 典型的 SQL 用法
- 在 Google Sheet 里 Col 代表着 Column，一般情况下输入 A, B, C 或 Column1, Column2, Column3 均会被认为找不到该 Column

</br>

#### Note
使用 Query + Select + Where + Importrange 时，第一次会出现错误，原因是没有获取读取表格的 access，因此需要先在新的表上读取一次 importrange，再将 importrange function 放进 query里。

第一次用 importrange 读取新表会出现以前情况，点击**Allow access**即可。
![](https://gilgamel.github.io/post-images/1617726487957.png)

自此，合并各分公司表格工作完成。
开始第二步：将数据放到 PBI Service 上，让数据能够定时刷新。

</br>

## STEP 2. Connect Goolge Sheet to PBI Service and Configure Scheduled Refresh
在将合并好的 Google Sheet 设置成 “Anyone can view with link”以后，原本想直接在 PBI Service 上直接通过 Web/Web API 的形式进行连接，但一直显示需要需要安装并选择网关才能设置成功。由于安装网关对电脑负荷过大，因此一样采取老办法：尝试利用 Power Query 抓取数据。

### STEP 2.1 修改 shareable link
根据 Stackoverflow 上的Solution，需要将 shareable link 作出以下修改：

</br>

 **From**
`https://docs.google.com/spreadsheets/d/{spreadsheet_ID}/edit#gid=0`
 
> </br>

 **To**
`https://docs.google.com/spreadsheets/d/{spreadsheet_ID}/export?format=xlsx&id={spreadsheet_ID}`

</br>

- **export?format=xlsx&id={spreadsheet_ID}** - 将已经分享出来的 Google Sheet 输出为 xlsx 格式；且再次 identify spreadsheet via spreadsheet_ID

### STEP 2.2 攥写 Power Query
1. 打开 app.powerbi.com
2. 打开相对应的 Workspace。根据个人习惯，我建立了一个叫 DATAFLOW 的 Workspace，专门用于刷新数据
3. 选择**新建数据流**，见下图

![](https://gilgamel.github.io/post-images/1617730510237.png)
4. 由于这是我第一次尝试从 Google Sheet 抓取数据，我选择**定义新表**

![](https://gilgamel.github.io/post-images/1617730529275.png)
5. 选择最下方的**空白查询**

![](https://gilgamel.github.io/post-images/1617730575163.png)


输入以下 Query Code：
```
let 
    Source = Excel.Workbook(Web.Contents("the-link-in-step-2.1"), null, true）
in 
    Source
```

6. 重命名并保存退出
7. 返回到 DATAFLOW 界面，选择刚才创建的数据流，并选择**设置**

![](https://gilgamel.github.io/post-images/1617730834159.png)
8. 数据源凭证 — 编辑凭据 — 勾选“**跳过测试连接**“

![](https://gilgamel.github.io/post-images/1617730938911.png)

自此，通过 Power Query 在 PBI Service 上抓取 Google Sheet 的目的已完成。
最后一步便是设置定时刷新。


### STEP 2.3 Configure Scheduled Refresh

PBI Pro Lincense 每天可以设置8次定时刷新，因此我一般选择每三个小时刷新一次。

操作如下：
- 数据流设置 — 计划的更新 — 选择相对应的时区及刷新频率 — 选择需要刷新的时间段

</br>

## Other
在查询如何通过Power Query 过程中发现还有一种方法，即通过 Google BigQuery 将数据存储到上面，再利用 Power Query 抓取 BigQuery 上的数据。

BigQuery 被誉为 **secure method**，而目前使用的方法则相对的 **insecure**，后续迭代可以尝试一下。

另外一个发现，移动 Google Sheet 的位置并不会影响它的spreadsheet_ID，因此在 Personal Drive 上测试完以后基于共享，后续将 Google Sheet 移动到 Shared Drive 也毫无障碍，这点必须好评。

</br>

## Ref
> https://stackoverflow.com/questions/45945815/how-to-connect-google-sheet-with-power-bi
> 
> https://webapps.stackexchange.com/questions/85101/queryimportrange-function-to-skip-columns
> 
> https://webapps.stackexchange.com/questions/93002/unable-to-parse-query-string-for-function-query-when-using-importrange
> 
> https://blog.coupler.io/combine-sheets-into-one/
> 
> https://blog.coupler.io/query-importrange/#What_you_can_do_with_IMPORTRANGEQUERY_functions_real-life_formula_examples
> 
> https://support.google.com/docs/answer/3093343?hl=en
> 
> https://support.google.com/docs/answer/3093197?hl=en
> 
> https://gist.github.com/Spencer-Easton/78f9867a691e549c9c70
> 
> https://www.excelinppc.com/using-google-sheets-as-data-source-in-power-query
