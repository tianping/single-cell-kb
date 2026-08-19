# scRNA-seq 质控与预处理

tags: [rnaseq, preprocessing, qc, normalization, batch-correction]

> 来源：[待补充] · 日期

## 背景 / 原理

单细胞 RNA-seq 数据预处理是下游分析的基础，主要包括：
- **质控 (QC)**：识别低质量细胞、双细胞、空滴
- **归一化**：消除文库大小差异
- **批次校正**：消除技术批次效应

## 关键质控指标

| 指标 | 说明 | 典型阈值 |
|------|------|----------|
| nCount_RNA | 每细胞总 UMI 数 | > 500, < 50000 |
| nFeature_RNA | 每细胞检测到的基因数 | > 200, < 6000 |
| percent.mt | 线粒体基因比例 | < 10-20% |
| percent.rb | 核糖体基因比例 | 可选 |
| doublet score | 双细胞概率 | < 0.25 |

## 常用工具

| 步骤 | 工具 | 语言 | 备注 |
|------|------|------|------|
| QC 可视化 | Seurat `VlnPlot`/`FeatureScatter` | R | 交互式探索 |
| 双细胞检测 | **DoubletFinder**, **Scrublet**, **scDblFinder** | R/Python | 推荐跑多个取交集 |
| 归一化 | **SCTransform** (Seurat), **scran** `computeSumFactors`, **scVI** | R/Python | SCTransform 推荐 |
| 批次校正 | **Harmony**, **Seurat v5 CCA**, **LIGER**, **scVI**, **BBKNN** | R/Python | 看数据规模选 |

## 推荐流程 (Seurat v5)

```r
library(Seurat)
library(DoubletFinder)

# 1. 创建对象
obj <- CreateSeuratObject(counts = counts, min.features = 200)

# 2. QC
obj[["percent.mt"]] <- PercentageFeatureSet(obj, pattern = "^MT-")
obj <- subset(obj, subset = nFeature_RNA > 200 & nFeature_RNA < 6000 & percent.mt < 20)

# 3. 双细胞去除
obj <- DoubletFinder_v3(obj, pN = 0.25, pK = 0.09, nExp = ncol(obj) * 0.076)
obj <- subset(obj, subset = DF.classifications_0.25_0.09_XXX == "Singlet")

# 4. SCTransform 归一化
obj <- SCTransform(obj, vars.to.regress = "percent.mt", verbose = FALSE)

# 5. 批次校正 (如有多样本)
obj <- RunHarmony(obj, group.by.vars = "orig.ident")
```

## 参考文献

- Hafemeister & Satija, 2019. *Normalization and variance stabilization of single-cell RNA-seq data using regularized negative binomial regression*. Genome Biology.
- Korsunsky et al., 2019. *Fast, sensitive and accurate integration of single-cell data with Harmony*. Nature Methods.
- Wolock et al., 2019. *DoubletFinder: Doublet Detection in Single-Cell RNA Sequencing Data*. Cell Reports.

## 相关链接

- [Seurat v5 官方教程](https://satijalab.org/seurat/articles/pbmc3k_tutorial.html)
- [SCTransform 论文](https://genomebiology.biomedcentral.com/articles/10.1186/s13059-019-1874-1)
- [Harmony GitHub](https://github.com/immunogenomics/harmony)