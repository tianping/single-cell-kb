# Seurat (R)

tags: [tools, r, seurat, single-cell]

> 来源：[待补充] · 日期

## 概述

Seurat 是 R 语言中最流行的单细胞 RNA-seq 分析工具包，提供质控、归一化、聚类、注释、差异表达等全流程功能。

## 安装

```r
# 安装稳定版
if (!requireNamespace("Seurat", quietly = TRUE)) {
    install.packages("Seurat")
}

# 或安装开发版（GitHub）
# devtools::install_github("satijalab/seurat")

library(Seurat)
```

## 核心功能

| 功能 | 主要函数 | 说明 |
|------|----------|------|
| 创建对象 | `CreateSeuratObject()` | 从 count 矩阵创建 Seurat 对象 |
| 质控 | `PercentageFeatureSet()`, `VlnPlot()` | 计算线粒体百分比、可视化质控指标 |
| 归一化 | `NormalizeData()`, `SCTransform()` | LogNormalize 或 SCTransform 归一化 |
| 特征选择 | `FindVariableFeatures()` | 识别高度变异基因 |
| 降维 | `RunPCA()`, `RunUMAP()`, `RunTSNE()` | PCA、UMAP、t-SNE 降维 |
| 聚类 | `FindNeighbors()`, `FindClusters()` | 构建 KNN 图并进行 Leiden/Louvain 聚类 |
| 注释 | `FindAllMarkers()` | 寻找每个簇的标记基因 |
| 整合 | `FindIntegrationAnchors()`, `IntegrateData()` | 多批次数据整合 |
| 差异表达 | `FindMarkers()`、`FindAllMarkers()` | Wilcoxon、MAST、DESeq2 等检验方法 |
| 可视化 | `DimPlot()`, `FeaturePlot()`, `DoHeatmap()` | 降维可视化、特征表达热图 |

## 重要更新 (v5)

- **SCTransform v2**：改进的归一化方法
- **双模态支持**：通过 `Weighted Nearest Neighbors (WNN)` 整合 RNA 和 ATAC
- **空间转录组**：原生支持 Visium、Slide-seq 等空间数据
- **集成生态**：与 Signac、Disk.Data 框架无缝整合

## 常用工作流

```r
# 标准 scRNA-seq 流程
pbmc <- CreateSeuratObject(counts = pbmc_data)
pbmc[["percent.mt"]] <- PercentageFeatureSet(pbmc, pattern = "^MT-")
pbmc <- subset(pbmc, subset = nFeature_RNA > 200 & nFeature_RNA < 2500 & percent.mt < 5)
pbmc <- NormalizeData(pbmc)
pbmc <- FindVariableFeatures(pbmc, selection.method = "vst", nfeatures = 2000)
pbmc <- ScaleData(pbmc)
pbmc <- RunPCA(pbmc, features = VariableFeatures(object = pbmc))
pbmc <- FindNeighbors(pbmc, dims = 1:10)
pbmc <- FindClusters(pbmc, resolution = 0.5)
pbmc <- RunUMAP(pbmc, dims = 1:10)
```

## 参考文献

- Satija et al., 2015. *Spatial reconstruction of single-cell gene expression data*. Nature Biotechnology.
- Butler et al., 2018. *Integrating single-cell transcriptomic data across different conditions, technologies, and species*. Nature Biotechnology.
- Stuart et al., 2019. *Comprehensive Integration of Single-Cell Data*. Cell.
- Hafemeister & Satija, 2019. *Normalization and variance stabilization of single-cell RNA-seq data using regularized negative binomial regression*. Genome Biology.

## 相关链接

- [Seurat 官方网站](https://satijalab.org/seurat/)
- [Seurat 教程](https://satijalab.org/seurat/articles/pbmc3k_tutorial.html)
- [Seurat GitHub](https://github.com/satijalab/seurat)