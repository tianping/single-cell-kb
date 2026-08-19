# scATAC-seq 与 RNA 数据整合

tags: [atac, rna, integration, signac, archr]

> 来源：[待补充] · 日期

## 背景 / 原理

联合分析 scATAC-seq 和 scRNA-seq 可以将染色质可及性与基因表达关联起来，推断调控网络。

## 主流方法

| 工具 | 特点 |
|------|------|
| **Signac** | Seurat 生态，易于与 RNA 整合 |
| **ArchR** | 功能全面，支持峰值矩阵、motif、足印等 |
| **MAESTRO** | 统一框架，支持 multiome 数据 |
| **Cobolt** | 基于矩阵分解的多模态整合 |
| **LIGER** | 也支持 ATAC+RNA 整合 |

## 推荐流程 (Signac)

```r
library(Signac)
library(Seurat)
library(EnsDb.Hsapiens.v86)

# 假设已有 atac.seurat 和 rna.seurat 对象
# 1. ATAC 对象：添加基因注释
Annotation(atac) <- EnsDb.Hsapiens.v86

# 2. 寻找锚点（使用互相关或标准化值）
transfer.anchors <- FindTransferAnchors(
    reference = rna,
    query = atac,
    features = VariableFeatures(object = rna),
    reference.assay = "RNA",
    query.assay = "PEAK",
    reduction = "cca"
)

# 3. 将 RNA 表达预测到 ATAC 细胞
predicted RNA <- TransferData(
    anchorset = transfer.anchors,
    refdata = rna[["RNA"]]$data,
    weight.reduction = atac[["lsi"]],
    dims = 2:30
)

# 4. 可视化：散点图、热图等
```

## 参考文献

- Stuart et al., 2019. *Comprehensive Integration of Single-Cell Data*. Cell.
- Stuart & Satija, 2019. *Integrative single-cell analysis*. Nature Reviews Genetics.
- Wang et al., 2021. *Signac: a flexible framework for analyzing single-cell chromatin accessibility data*. Genome Biology.

## 相关链接

- [Signac ATAC+RNA 教程](https://stuartlab.org/signac/articles/rna_vignette.html)
- [ArchR 文档](https://www.archrproject.com/)