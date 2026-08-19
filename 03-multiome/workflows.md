# Multiome (RNA+ATAC) 联合分析流程

tags: [multiome, rna, atac, wnn, signac, archr]

> 来源：[待补充] · 日期

## 背景 / 原理

Multiome 同时测量同一细胞的 transcriptome 和 epigenome，需要特殊的分析方法来联合两种模态。

## 主流方法

| 方法 | 特点 |
|------|------|
| **Weighted Nearest Neighbors (WNN)** | Seurat v4+/Signac 的核心，计算 RNA 和 ATAC 的加权邻接图 |
| **MAESTRO** | 统一框架，支持 peaks、gene activity、motif 等 |
| **Cobolt** | 基于矩阵分解的多模态整合 |
| **LIGER** | 也支持 multiome 数据整合 |
| **scMultiome** | 专门针对 10x multiome 的工作流 |

## 推荐流程 (Seurat/Signac WNN)

```r
library(Signac)
library(Seurat)
library(EnsDb.Hsapiens.v86)

# 假设已有 multiome.seurat 对象，包含 RNA 和 ATAC 两个 assay
# 1. 标准化 RNA
multiome <- NormalizeData(multiome, assay = "RNA")
multiome <- FindVariableFeatures(multiome, assay = "RNA")

# 2. 标准化 ATAC (峰值矩阵)
multiome <- RunTFIDF(multiome)
multiome <- FindTopFeatures(multiome, min.cutoff = 'q0')
multiome <- RunSVD(multiome)

# 3. 计算 gene activity
multiome <- GeneActivity(multiome)

# 4. 标准化 gene activity
DefaultAssay(multiome) <- 'ACTIVITY'
multiome <- NormalizeData(multiome)
multiome <- FindVariableFeatures(multiome, assay = 'ACTIVITY', nfeatures = 3000)

# 5. 寻找多模态锚点（使用 WNN）
multiome <- FindMultiModalNeighbors(
  multiome,
  reduction.list = list("pca", "lsi"),  # RNA PCA, ATAC LSI
  dims.list = list(1:50, 2:40),
  modality.weight.name = c("RNA", "ACTIVITY"),
  modality.weight = c(0.5, 0.5)
)

# 6. 聚类与 UMAP
multiome <- RunUMAP(multiome, nn.name = "weighted.nn", reduction.name = "wnn.umap")
multiome <- FindClusters(multiome, graph.name = "weighted.nn", resolution = 0.5)

# 7. 可视化
DimPlot(multiome, reduction = "wnn.umap", label = TRUE)
```

## 参考文献

- Stuart et al., 2019. *Comprehensive Integration of Single-Cell Data*. Cell.
- Wang et al., 2021. *Signac: a flexible framework for analyzing single-cell chromatin accessibility data*. Genome Biology.
- Hao et al., 2021. *Integrated analysis of multimodal single-cell data*. Cell (Seurat v4 WNN).

## 相关链接

- [Seurat WNN 教程](https://satijalab.org/seurat/articles/weighted_nearest_neighbors.html)
- [Signac Multiome 教程](https://stuartlab.org/signac/articles/multiome_vignette.html)