# scRNA-seq 降维、聚类与细胞注释

tags: [rnaseq, clustering, dimensionality-reduction, annotation]

> 来源：[待补充] · 日期

## 背景 / 原理

核心步骤：
1. **降维**：PCA → Harmony/ScVI → UMAP/t-SNE
2. **聚类**：Graph-based (Leiden/Louvain) → 分辨率调节
3. **注释**：Marker genes、参考图谱、自动注释

## 降维方法对比

| 方法 | 适用场景 | 优点 | 缺点 |
|------|----------|------|------|
| PCA | 标准流程起点 | 快、可解释 | 线性、受批次影响 |
| Harmony | 批次校正后 | 保留生物变异、快 | 需指定批次变量 |
| scVI | 深度生成模型 | 非线性、大数据、不确定性量化 | 训练慢、需 GPU |
| Scanorama | 多批次整合 | 无需共同细胞类型 | 内存大 |

## 聚类参数指南

```r
# Seurat 典型流程
obj <- RunPCA(obj, npcs = 50)
obj <- RunHarmony(obj, group.by.vars = "orig.ident")  # 如需
obj <- RunUMAP(obj, reduction = "harmony", dims = 1:30)
obj <- FindNeighbors(obj, reduction = "harmony", dims = 1:30)
obj <- FindClusters(obj, resolution = 0.5)  # 0.3-1.0 常用范围
# 分辨率选择：clustree 可视化
```

| 数据规模 | 建议 PCs | 分辨率范围 |
|----------|----------|------------|
| < 5k cells | 20-30 | 0.3-0.6 |
| 5k-50k | 30-50 | 0.4-0.8 |
| > 50k | 50 | 0.5-1.0 |

## 细胞注释策略

| 策略 | 工具 | 适用场景 |
|------|------|----------|
| 手动 Marker | Seurat `FindAllMarkers` | 细胞类型已知、样本少 |
| 参考映射 | **SingleR**, **scANVI**, **CellTypist**, **scArches** | 有高质量参考图谱 |
| 自动注释 | **CellTypist**, **scCATCH**, **SCINA** | 快速初筛 |
| 迁移学习 | **scArches**, **scVI** 跨数据集 | 同组织不同研究 |

## 常用 Marker 基因库

- **PanglaoDB** / **CellMarker 2.0** / **Human Protein Atlas**
- 组织特异性：Tabula Sapiens、Human Cell Atlas、Mouse Cell Atlas

## 参考文献

- Traag et al., 2019. *Louvain/Leiden algorithm*. Scientific Reports.
- Aran et al., 2019. *SingleR: Reference-based annotation*. Nature Biotechnology.
- Lotfollahi et al., 2022. *Mapping single-cell data to reference atlases by transfer learning*. Nature Biotechnology.

## 相关链接

- [clustree: 聚类分辨率可视化](https://github.com/lazappi/clustree)
- [CellTypist 在线工具](https://www.celltypist.org/)
- [scArches 教程](https://www.scarches.org/)