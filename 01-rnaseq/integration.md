# scRNA-seq 多数据集整合

tags: [rnaseq, integration, batch-correction, harmony, seurat, scvi]

> 来源：[待补充] · 日期

## 背景 / 原理

整合多个批次或条件的数据以消除技术噪声，保留生物学差异。

## 主流方法

| 方法 | 原理 | 优点 | 缺点 |
|------|------|------|------|
| Harmony | 软聚类 + 迭代校正 | 快、灵活、保留细粒度 | 需指定批次变量 |
| Seurat v5 (CCA/ RPCA) | 规范相关分析 | 生态成熟、可视化好 | 对批数敏感 |
| LIGER | 非负矩阵分解 | 适合极端批次效应 | 少见、文档少 |
| scVI / scANVI | 深度变分自编码器 | 处理大数据、不确定性 | 训练慢、需 GPU |
| BBKNN | 基于最近邻的批次校正 | 简单、快 | 仅用于邻接图构建 |
| Liger | 非负矩阵分解 | 适合稀疏数据 | — |

## 推荐流程 (Seurat v5)

```r
# 假设有 list.of.seurat.objects
features <- SelectIntegrationFeatures(object.list = list.of.seurat.objects, nfeatures = 3000)
list.of.seurat.objects <- PrepSCTIntegration(object.list = list.of.seurat.objects, anchor.features = features)
anchors <- FindIntegrationAnchors(object.list = list.of.seurat.objects, normalization.method = "SCT", anchor.features = features)
integrated <- IntegrateData(anchorset = anchors, normalization.method = "SCT")
```

## 参考文献

- Korsunsky et al., 2019. *Harmony: fast, sensitive, accurate integration of single-cell data*. Nature Methods.
- Stuart et al., 2019. *Comprehensive Integration of Single-Cell Data*. Cell (Seurat v3/4).
- Hie et al., 2019. *LIGER: lightweight, memory-efficient integration*. Nature Biotechnology.
- Lopez et al., 2018. *Deep generative modeling for single-cell genomics*. Nature Methods (scVI).

## 相关链接

- [Seurat Integration Tutorial](https://satijalab.org/seurat/articles/integration_intro.html)
- [Harmony GitHub](https://github.com/immunogenomics/harmony)
- [scVI Tutorial](https://scvi-tools.org/)