---
title: "Bioinfo | 003. 文献通读参考-2"
description: 
date: 2024-06-28T18:28:41-07:00
image: 
math:
license: 
categories:
- bioinfomatics
tags:
hidden: false
comments: true
draft: false
summary:
---

这篇blog主要是针对[Significance of Immunogenic Cell Death-Related Prognostic Gene Signature in Cervical Cancer Prognosis and Anti-Tumor Immunity](https://doi.org/10.2147/JIR.S410140)中的分析部分详细阅读，并记录其过程思路，为后续实操打基础。

总结一下，本文献通过一些列分析，确认了PDIA3 高表达可以作为宫颈癌预测的一个重要因素。假设患者预后PDIA3依然存在高表达，那么需要重点关注这个患者，因为代表预后较差。（那么怎么样才代表高呢？）

## 数据来源
1. TCGA中的宫颈癌数据库（309条宫颈癌患者的基因表达数据，323条宫颈癌患者的临床病理数据）
2. GSE44001 （300条样本）
3. GSE16852
4. GSE6791

## 数据处理
1. 移除不完整的样本，只保留具有完整数据的样本用于后续分析
2. 将转录本每千碱基每百万片段（FPKM，Fragments Per Kilobase of transcript per Million fragments mapped）值转换为每百万转录本（TPM，Transcripts Per Million）值进行分析。**这种转换可以减少跨样本定量中的不准确性，使得不同样本间的数据更加可比。**
    - tpm = (fpkm / sum(fpkm)) * 1e6
3. 从之前的文献和研究中获得**49个与ICD相关的基因**

## Gene Set Variation Analysis（GSVA）- 基因集变异分析
pkg:
- RCircos
- maftools

1. 利用"RCiros"分析23对人类染色体上49个相关基因的拷贝数变异（CNV）获得或缺失的频率，用以分析分析宫颈癌中ICD相关基因的突变。（GSVA可以用来分析ICD相关基因在宫颈癌中的拷贝数（CNV）和突变情况。）
2. 利用"maftools"检测TCGA队列中ICD相关基因的突变频率、突变类型和碱基变化。

**拷贝数变异（Copy Number Variation, CNV）**是指基因组中某些片段的拷贝数与参考基因组不一致的现象。这些变异包括基因或基因组片段的增益（copy number gain）或缺失（copy number loss）。CNV可以影响基因的剂量，从而影响基因表达和功能，是许多疾病（包括癌症）的重要遗传因素。

## 基于IRG表达的聚类分析
pkg:
- ConsencesclusterPlus
- clusterProfiler

1. 利用"ConsencesclusterPlus"针对肿瘤细胞进行聚类分析**（TCGA数据库+3个GEO数据库？）**
2. 用Kaplan-Meier分别分析共识聚类后的肿瘤亚型的预后
3. 进行功能富集分析（GO和KEGG）

## ICD相关风险评分模型的构建
pkg:
- limma
- glmnet
- maxstat

1. 利用"limma"确认与ICD相关的基因表达
2. 用lasso regression进一步缩小ICD相关的基因表达
3. 利用cox回归分析计算风险指数（IPGs model）**IPGs model coef让他们决定深入分析PDIA3）**
4. 绘制ROC、AUC
5. 结合临床数据跟IPGs分数进行单因素Cox分析以及多因素Cox分析，以确定风险评分是否可以作为宫颈癌患者预后的独立预测因子。
6. 利用"maxstat"确定高风险组跟低风险组

## IRG与免疫浸润细胞的相关性分析及免疫治疗展望
pkg:
- ESTIMATE
- pRRophetic

1. 计算ImmuneScore、StromalScore和ESTIMATEScore，分别表示免疫浸润评分、基质细胞浸润评分和估计评分(基质细胞和免疫细胞评分的组合)。
2. 使用单样本基因集富集分析（ssGESA）
3. 利用"pRRophetic"计算药物敏感性差异
4. 用Tumor Immune Dysfunction and Exclusion (TIDE) (http://tide.dfci.harvard.edu/)提供的接口预测免疫治疗反应

**The Mann–Whitney test was used to compare immune cell ssGSEA scores and immune-related pathways between groups. Two-tailed p-values < 0.05 were considered statistically significant.**

**ssGSEA（single-sample Gene Set Enrichment Analysis）和GSEA（Gene Set Enrichment Analysis）**都是基于基因表达数据的富集分析方法，但它们有一些重要的区别和适用情况：

1. GSEA（Gene Set Enrichment Analysis）
- 定义：GSEA是一种用于发现基因集在两个不同生物条件下的富集情况的方法，通常是比较疾病组与对照组之间的差异。
- 原理：GSEA基于基因表达的秩次，将基因集中的基因排列在基因表达数据的排序列表中，然后计算富集分数来评估基因集在整体上的富集程度。
- 适用情况：适用于比较两个或多个不同条件（如疾病组 vs 对照组）中的基因表达差异，探索与疾病相关的生物学过程和通路。
2. ssGSEA（single-sample Gene Set Enrichment Analysis）
- 定义：ssGSEA是一种基于基因表达数据评估单个样本中基因集富集情况的方法，不需要先定义样本间的分组。
- 原理：ssGSEA使用核心基因集中的基因在每个样本中的表达水平，计算出每个样本中基因集的富集得分。
- 适用情况：适用于单个样本的基因表达数据，评估在单个样本中特定生物过程或通路的活跃程度，例如评估免疫细胞类型的浸润情况或代谢通路的活性。
区别总结
- 目标：GSEA用于组间比较，寻找差异性表达的生物通路；ssGSEA用于单样本内部，评估个体样本中基因集的富集程度。
- 输入：GSEA需要分组信息，将基因表达数据分为至少两组；ssGSEA直接作用于单个样本，不需要先定义组别。
- 应用：GSEA适合比较疾病状态、药物治疗前后等条件下的基因表达差异；ssGSEA适合评估个体样本的基因集富集，如免疫细胞类型的浸润程度或代谢通路的活性。

综上所述，选择GSEA还是ssGSEA取决于研究问题和数据类型：如果需要比较不同条件下的基因集富集情况，选择GSEA；如果要在单个样本中评估基因集的富集情况，选择ssGSEA。

## 实验材料？
> The human CC-related cell lines HeLa and C33a were purchased from Meisen (Zhejiang, China), and SiHa and ME180 were purchased from Pricella (Wuhan, China). All cultures were supplemented with RPMI 1640 medium, 0.25% trypsin ethylenediamine tetraacetic acid, and fetal bovine serum (Gibco, NY, USA) and maintained below 5% CO2 at 37 °C. PDIA3 antibody (Proteintech, Wuhan, China), GAPDH antibody (CST, Shanghai, China), goat anti-rabbit IgG antibody secondary antibody (Zsbio, Beijing, China), and goat anti-mouse IgG H&L/Cy3 (Beyotime, Shanghai, China).

忽略。

## Immunofluorescence 
> For PDIA3 co-localization analysis, the CC cells were treated with a PDIA3 antibody (1:2000 dilution) overnight at 4 °C. The cells were then restained with goat anti-rabbit IgG H&L/FITC (1:2000 dilution).

处理方法（这里应该是属于湿实验了，不确定为什么要做湿实验？）

## Real-Time Quantitative PCR
> 收集8例正常卵巢组织和24例CESC组织进行实时荧光定量PCR (qPCR)检测。使用Promega公司的总RNA提取试剂盒，从组织样本(中国上海)中提取总RNA。在提取RNA后，我们用中国上海的转录子第一链cDNA合成试剂盒逆转录全RNA产生cDNA。我们使用Tiangen Biotech公司的SuperReal PreMix Plus进行qPCR以确定标签(中国北京)中IPGs的表达水平。我们从上海生工生物科技公司购买的IPGs引物序列见补充表1。 

实验相关不是特别懂，所以是想通过实验来验证模型效果吗？