# Seurat 差异表达分析教程

tags: [rnaseq, differential-expression, seurat, pseudobulk, deseq2, mast, wilcoxon]

> 来源：[Seurat DE Vignette](https://satijalab.org/seurat/articles/de_vignette) · 2026-08-19

## 背景 / 原理

Seurat 提供完整的差异表达（DE）分析功能，核心函数是 `FindMarkers()`。默认使用 Wilcoxon rank sum test，也支持 MAST、DESeq2、negbinom 等多种检验方法。本教程基于 interferon-beta 刺激的 PBMC 数据集（ifnb）演示三种常见场景：

1. **簇间比较**：比较两种细胞类型（如 CD16 Mono vs CD14 Mono）
2. **同类型细胞跨条件比较**：比较同一细胞类型在刺激 vs 对照中的差异
3. **Pseudobulk 分析**：按样本聚合后做 DE，避免假阳性

## 分析流程

### 1. 默认 DE 分析（Wilcoxon）

```r
library(Seurat)
library(SeuratData)
InstallData("ifnb")
ifnb <- LoadData("ifnb")

# 归一化
ifnb <- NormalizeData(ifnb)

# 比较两个细胞类型
Idents(ifnb) <- "seurat_annotations"
monocyte.de.markers <- FindMarkers(ifnb, ident.1 = "CD16 Mono", ident.2 = "CD14 Mono")
head(monocyte.de.markers)
```

**结果列说明：**

| 列 | 说明 |
|------|------|
| p_val | 未校正 p 值 |
| avg_log2FC | 两组平均表达的 log2 fold-change（正值 = 在 ident.1 中更高） |
| pct.1 | 在第一组中检测到该基因的细胞比例 |
| pct.2 | 在第二组中检测到该基因的细胞比例 |
| p_val_adj | Bonferroni 校正后的 p 值 |

### 2. 同一细胞类型跨条件比较

```r
# 创建 celltype_stim 复合标签
ifnb$celltype.stim <- paste(ifnb$seurat_annotations, ifnb$stim, sep = "_")
Idents(ifnb) <- "celltype.stim"
mono.de <- FindMarkers(ifnb, ident.1 = "CD14 Mono_STIM", ident.2 = "CD14 Mono_CTRL", verbose = FALSE)
```

⚠️ **注意**：直接在单细胞水平做跨条件 DE 会把每个细胞当作独立样本，忽略样本间相关性，导致大量假阳性（Squair et al., 2021; Zimmerman et al., 2021）。推荐使用 pseudobulk。

### 3. Pseudobulk DE 分析

按样本（donor）和条件聚合细胞，然后用 DESeq2 做样本水平的 DE。

```r
# 添加样本信息（从原论文 GitHub 获取 donor ID）
ctrl <- read.table(url("https://raw.githubusercontent.com/yelabucsf/demuxlet_paper_code/master/fig3/ye1.ctrl.8.10.sm.best"), head = T, stringsAsFactors = F)
stim <- read.table(url("https://raw.githubusercontent.com/yelabucsf/demuxlet_paper_code/master/fig3/ye2.stim.8.10.sm.best"), head = T, stringsAsFactors = F)
info <- rbind(ctrl, stim)
info$BARCODE <- gsub(pattern = "\\-", replacement = "\\.", info$BARCODE)
info <- info[grep(pattern = "SNG", x = info$BEST), ]
info <- info[!duplicated(info$BARCODE) & !duplicated(info$BARCODE, fromLast = T), ]
rownames(info) <- info$BARCODE
info <- info[, c("BEST"), drop = F]
names(info) <- c("donor_id")
ifnb <- AddMetaData(ifnb, metadata = info)
ifnb$donor_id[is.na(ifnb$donor_id)] <- "unknown"
ifnb <- subset(ifnb, subset = donor_id != "unknown")

# Pseudobulk：按 stim + donor_id + cell type 聚合
pseudo_ifnb <- AggregateExpression(ifnb, assays = "RNA", return.seurat = T,
                                    group.by = c("stim", "donor_id", "seurat_annotations"))
pseudo_ifnb$celltype.stim <- paste(pseudo_ifnb$seurat_annotations, pseudo_ifnb$stim, sep = "_")

# 用 DESeq2 做 pseudobulk DE
Idents(pseudo_ifnb) <- "celltype.stim"
bulk.mono.de <- FindMarkers(object = pseudo_ifnb,
                             ident.1 = "CD14 Mono_STIM",
                             ident.2 = "CD14 Mono_CTRL",
                             test.use = "DESeq2")
```

### 4. 单细胞 vs Pseudobulk 结果对比

```r
# 对比两种方法的显著性基因数
# Common (both): 3519
# Only in single-cell: 1649  ← 多为假阳性
# Only in bulk: 204
```

**关键发现**：
- 单细胞水平的 p 值普遍更小（更"显著"），但很多是假阳性
- Pseudobulk 更保守，减少了因忽略样本相关性导致的假阳性
- SRGN 和 HLA-DRA 是典型例子：单细胞水平极显著（p ≈ 10⁻²¹），但 pseudobulk 后 p ≈ 0.18（不显著），因为跨样本变异大

### 5. 可视化验证

```r
# 按条件分组看表达
VlnPlot(ifnb, features = common[1:2],
        idents = c("CD14 Mono_CTRL", "CD14 Mono_STIM"),
        group.by = "stim")

# 按样本分组看表达（检查跨样本一致性）
ifnb$donor_id.stim <- paste0(ifnb$stim, "-", ifnb$donor_id)
VlnPlot(ifnb, features = c('SRGN','HLA-DRA'),
        idents = c("CD14 Mono_CTRL", "CD14 Mono_STIM"),
        group.by = "donor_id.stim", ncol = 1)
```

## 支持的 DE 检验方法

| 方法 | test.use | 说明 |
|------|----------|------|
| Wilcoxon rank sum | `"wilcox"` (默认) | 基于 presto 包，速度快 |
| Wilcoxon (limma) | `"wilcox_limma"` | 基于 limma 包 |
| Likelihood-ratio | `"bimod"` | McDavid et al., 2013 |
| AUC classifier | `"roc"` | 标准 AUC |
| Student's t-test | `"t"` | 简单 t 检验 |
| Poisson LRT | `"poisson"` | 仅适用于 UMI 数据 |
| Negative Binomial LRT | `"negbinom"` | 仅适用于 UMI 数据 |
| Logistic Regression | `"LR"` | 似然比检验 |
| MAST | `"MAST"` | GLM 框架，将检测率作为协变量（Finak et al., 2015） |
| DESeq2 | `"DESeq2"` | 负二项分布模型（Love et al., 2014） |

```r
# 使用 MAST
head(FindMarkers(ifnb, ident.1 = "CD14 Mono", ident.2 = "CD16 Mono", test.use = "MAST"))
```

## 推荐工具

| 场景 | 推荐方法 |
|------|----------|
| 快速初筛 | Wilcoxon（默认，presto 加速） |
| 跨条件比较（有重复样本） | Pseudobulk + DESeq2 |
| 考虑检测率偏差 | MAST |
| UMI 数据精确建模 | negbinom / DESeq2 |

## 参考文献

- Squair et al., 2021. *Confronting false discoveries in single-cell differential expression*. Nature Communications.
- Zimmerman et al., 2021. *A practical guide to single-cell RNA-sequencing for biomedical research*. Nature Communications.
- Finak et al., 2015. *MAST: a flexible statistical framework for differential expression analysis of single-cell RNA-seq data*. Genome Biology.
- Love et al., 2014. *Moderated estimation of fold change and dispersion for RNA-seq data with DESeq2*. Genome Biology.

## 相关链接

- [Seurat DE Vignette 原文](https://satijalab.org/seurat/articles/de_vignette)
- [FindMarkers() 文档](https://satijalab.org/seurat/reference/findmarkers)
- [AggregateExpression() 文档](https://satijalab.org/seurat/reference/aggregateexpression)
- [SeuratData 包](https://github.com/satijalab/seurat-data)