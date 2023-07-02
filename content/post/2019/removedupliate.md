---
title: "Excel | Remove Duplicate"
description: 
date: 2019-08-20T11:25:00-04:00
image: 
math:
license: 
categories:
- 新建就是成功
tags:
- Excel
- VBA
hidden: false
comments: true
draft: false
summary:
---

最近一直在做活动，报名方式为麦客单表，但由于经常性会出现重复性报名情况，所以需要在统计之前对数据进行清洗。 

出现重复报名的情况一般如下：

1. 报名了 **A活动&B活动**后，又再报名了 **B活动&A活动**
2. 报名了 **A活动&B活动**后，又再报名了 **C活动**
3. 报名了 **A活动&B活动**后，又再报名了 **A活动&B活动&C活动**
4. 报名了 **A活动&B活动**后，又再报名了 **A活动&C活动&D活动**


## 思路

### 合并/去掉重复行

根据报名表的设置，一般都会有 **姓名、手机、微信、邮箱、参与活动** 这几项，而其中姓名跟手机是大概率是唯一，which means不会重复的，所以将使用姓名/手机作为检测项

![Snipaste_2019-08-21_23-54-49.png](https://i.loli.net/2019/08/22/vNIyXZYmdtxBoWS.png)

在以姓名作为检测项对齐进行排列后，可以看出这个叫 Ada 的少年一口气重复报名了三次，并且第二、三次报得是完全一样的，现在要做的就是 **将同一个姓名的人选择参加的活动全部合并在一起**

在活动后面一列起个小标题 **1** 写上以下code：

```Excel
=if(A2=A1, F1&";"&E2, E2)

# 如果 A2=A1，就将F1跟E2内容合并到一块，中间用；来表示，否则就显示E2
```

完事儿将公式拉完这一列。 在 **列1** 后面再弄一个 **列2**，写上以下code：

```Excel
=if(A2<>A3, "Merged Value", " ")

# 如果 A2 不等于 A3，那么就显示为 Merged Value，否则显示空白
```

同理，将公式拉满 **列2**；接着将 **列1&列2** 都选上，然后 **copy & paste value only**，将这两列的数值都变为plain text

接下来将 **列2** 选中，并且进行 **自定义排序** 

![image.png](https://i.loli.net/2019/08/22/rQgEpokvaZxhbYt.png)

选择 **扩展选定区域**，然后将主要关键字选为 **2**，次序为 **降序**

![image.png](https://i.loli.net/2019/08/22/NkeDbcPUOSME1xY.png)

最后将 **列2** 最下方显示为 **空白**的几行删掉即可。



### 去掉重复活动（Remove Duplicate）

在VBA上新建Module，输入以下code：

```Excel
Function RemoveDupes2(txt As String, Optional delim As String = " ") As String
    Dim x
    'Updateby20140924
    With CreateObject("Scripting.Dictionary")
        .CompareMode = vbTextCompare
        For Each x In Split(txt, delim)
            If Trim(x) <> "" And Not .exists(Trim(x)) Then .Add Trim(x), Nothing
        Next
        If .Count > 0 Then RemoveDupes2 = Join(.keys, delim)
    End With
End Function
```

Module创建完毕后，在新的空白列上输入以下code：

```Excel
=removedupes2(E2,";")

# remove deplicate from E2, ; is seperator
```

最后将新列上的数据 **copy &* paste value only**以后，即可删掉之前合并的重复活动列。