---
title: "Bioinfo | 002. 文献通读参考-1"
description: 
date: 2024-06-27T17:07:08-07:00
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

这篇blog主要是针对[An Immunogenic Cell Death-Related Genes Signature: Predicting Prognosis in Patients with Cervical Cancer](https://doi.org/10.21203/rs.3.rs-2620356/v1)中的分析部分详细阅读，并记录其过程思路，为后续实操打基础。

## 数据来源
1. TCGA中的宫颈癌数据库（309条宫颈癌患者的基因表达数据，323条宫颈癌患者的临床病理数据）
2. GSE44001 （300条样本）

## 数据预处理
1. 利用Perl software 对患者的基因表达数据、临床病例数据、突变符合数据进行整合和处理

## 提取ICD 相关基因细胞
1. 利用R提取 TCGA 数据集中的与ICD相关的基因表达。在这一步将目前已知与ICD相关的 34个基因都包括进来。
2. 用 [GENEMANIA](https://genemania.org/) database 构建与ICD相关基因的蛋白质互相作用网络

## 共识聚类分析
pkg:
- ConsensusClusterPlus
- heatmap
- surminer
- survivor

1. 利用R的 “ConsensusClusterPlus"pkg进行共识聚类分析。
    -  通过多次（1000次）重复聚类来评估不同聚类数量 （k = 1-9）下的结果一致性
    -  通过计算共识矩阵和共识的份评估每个k值的聚类稳定性，选择最优的聚类数量。
2. 利用R的"heatmap"pkg 将与ICD相关的基因分组进行可视化（分子亚型 vsICD相关基因）
    - 生成热图展示不同基因在各个亚型中的表达模式。
3. 利用R的"survminer"和"survivor"pkg 进行 Kaplan-Meier 分析（也就是预后生存率），评估不同亚型的生存情况，特别是高ICD表达和低R表达亚型之间的差异。
    - 整理每个亚型患者的生存数据，包括生存时间和生存状态
    - 利用"survival"中的Surv() 构建生存对象，后使用 “survminer“ 进行 KM 分析
    - 生成 KM 生存曲线，比较高ICD表达和低ICD表达
    - 利用Log-rank（mantel-cox）评估不同亚型之间生存曲线的显著性差异

## 差异表达基因鉴定
pkg:
- limma
- ggplot2
- pheatmap

1. 差异化分析：利用"limma"进行线性模型拟合，计算每个基因的P值和logFC。
2. 多重验证校正：使用Benjamini-Hochberg方法校正P值，得到FDR（adjusted P value）。
3. 筛选DEGs：选择FDR < 0.05且logFC > 1的基因，确保这些基因在统计学上显著且具有生物学意义的表达差异。

### Note

在差异表达基因（DEG）分析中，corrected P value (FDR) < 0.05 和 LogFC > 1 是常见的筛选标准。

**FDR**
FDR的定义
- FDR是错误发现率，是指在所有被认为是显著的基因中，实际是错误发现的比例。
它是多重检验校正方法之一，用于控制在多次独立检验中出现的假阳性结果的比例。

为什么使用FDR < 0.05
- 当进行大量独立检验（如在基因表达数据中分析数千个基因）时，单纯使用P值会导致大量的假阳性结果（即误认为某些基因是显著差异的）。
- 使用FDR校正可以控制这些假阳性结果的比例，确保结果的可靠性。FDR < 0.05 意味着在所有被认为显著的基因中，有不超过5%的可能是错误的。

如何计算FDR
- FDR通常通过Benjamini-Hochberg校正方法计算。这个方法根据P值的排序和数量，对P值进行调整，使得FDR可以控制在预设的阈值内。

**LogFC（Log Fold Change）**
LogFC的定义
- LogFC是对数倍数变化，表示基因在两个条件（如高表达和低表达）之间的表达变化程度。

为什么使用LogFC > 1：
- LogFC > 1 表示基因在高表达条件下的表达水平是低表达条件下的至少2倍（即2倍以上的变化）。
- 使用LogFC > 1作为筛选标准，可以确保所选的基因在两个条件之间具有显著的表达差异，从而使结果更加显著和生物学上有意义。

## 功能富集分析和基因富集分析（Gsea）
pkg:
- ClusterPro
- org.Hs.eg.db
- enrichplot
- ggplot2
- circlize
- RColorBrewer
- dplyr
- ggpubr
- ComplexHeatmap
- R.utils

目的：研究这些ICD相关基因的生物学功能特性和潜在通路。

1. 功能富集分析（GO和KEGG）
2. 用clusterProfiler进行GO富集分析，以及进行KEGG路径分析
3. 利用"ggplot2"和"enrichplot"进行可视化
4. 进行基因集富集分析（GSEA），基因集富集分析（GSEA）用于评估预定义的基因集在两个生物条件之间的富集程度。
    1. 使用“MSigDB”中的基因集文件，如c2.cp.kegg.symbols.gmt和c2.cp.go.symbols.gmt进行排序（如，按照logFC顺序排序）
    2. 运行GSEA，利用"clusterProfiler"进行GSEA分析
    3. 利用"enrichplot"进行可视化

## 肿瘤微环境的差异分析
pkg:
- limma
- optimize
- ggpubr

进行肿瘤微环境的差异分析可以帮助了解不同基因表达亚型在免疫微环境中的差异。

1. 分别对ICD高表达亚型和低表达亚型相关基因的免疫微环境进行评分。（？如何评分？在生物学上是否有一套标准的打分流程？）
2. 测定两种亚型的肿瘤纯度。
3. 绘制相关的基质评分、免疫评分、估算评分和肿瘤纯度的小提琴图。

## 两个ICD亚型之间的免疫特征
pkg:
- CIBERSORTRscriptv1.03 函数
- corrplot

1. 计算每个样本中22种免疫细胞类型的相对百分比，以分析309种免疫细胞的免疫学特征（应该就是TCGA的数据）。
2. 利用"pheatmap"创建免疫细胞之间的相关热图。

## 两个Icd亚群免疫细胞、Hla基因和免疫检查点的差异分析
pkg:
- vioplot
- limma
- plyr
- reshape2
- ggplot2
- ggpubr

1. 检测ICD高、低表达亚型之间的差异（亚型跟亚型之间的差异）。
2. 检查免疫细胞、HLA基因和免疫检查点的差异表达。
3. 创建免疫细胞的小提琴图及HLA基因和免疫检查点的箱型图。

## ICD相关基因风险构建？
1. 采用单变量Cox回归分析，找出与预后相关的ICD相关基因，并将其纳入Lasso Cox回归分析，缩小预测预后的基因范围。
2. 计算每个确定的相关基因的coef值，进一步得到模型公式，从而创建相关模型。

## Construction of the normogram development?

不知道该如何理解这个，暂时放这里。

## 免疫组织化学

使用Human Protein Atlas (https://www.proteinatlas.org/)下载宫颈癌组织和正常组织中ATG5、FOXP3、IFNG、PDIA3、TNF表达的免疫组化图片。

## 存活分析
pkg:
- survminer
- survivor

1. 利用Kaplan-Meier分析高风险ICD组及低风险ICD组的存活差异。
2. 采用单因素Cox分析（筛选了10 genes）、多因素Cox分析（最后筛选了6 genes）
3. 创建risk scoree (coe cient*expression of each gene in the sample)
4. ROC曲线和AUC帮助评估ICD作为宫颈癌患者预后指标的独立性和有效性。

## 免疫细胞相关性

也不是很懂，就放这吧。



