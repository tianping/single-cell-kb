# Scanpy (Python)

tags: [tools, python, scanpy, single-cell]

> 来源：[待补充] · 日期

## 概述

Scanpy 是 Python 生态中流行的单细胞 RNA-seq 分析工具包，基于 AnnData 数据结构，提供与 Seurat 类似的全流程功能。

## 安装

```bash
# 基础安装
pip install scanpy

# 推荐完整安装（包含可视化等依赖）
pip install scanpy[leiden,louvain,umap]
```

或使用 conda：

```bash
conda install -c conda-forge scanpy
```

## 核心数据结构: AnnData

Scanpy 的核心是 `AnnData` 对象，类似于 Seurat 的对象，但更轻量且易于与其他工具整合。

```python
import scanpy as sc
import pandas as pd
import numpy as np

# 从矩阵创建 AnnData
adata = sc.AnnData(X=count_matrix.T)  # 基因 x 细胞
adata.obs = pd.DataFrame(index=cell_names)  # 细胞注释
adata.var = pd.DataFrame(index=gene_names)  # 基因注释
```

## 核心功能

| 功能 | 主要函数 | 说明 |
|------|----------|------|
| 读取数据 | `sc.read_10x_mtx()`, `sc.read_h5ad()` | 读取各种格式 |
| 质控 | `sc.pp.calculate_qc_metrics()`, `sc.pl.violin()` | 计算和可视化 QC 指标 |
| 过滤 | `sc.pp.filter_cells()`, `sc.pp.filter_genes()` | 过滤低质量细胞和基因 |
| 归一化 | `sc.pp.normalize_total()`, `sc.pp.log1p()` | 总和归一化 + log1p |
| 特征选择 | `sc.pp.highly_variable_genes()` | 识别高度变异基因 |
| 归一化（回归） | `sc.pp.regress_out()`, `sc.pp.scale()` | 回复 confounders 并标准化 |
| 降维 | `sc.tl.pca()`, `sc.tl.umap()`, `sc.tl.tsne()` | PCA、UMAP、t-SNE |
| 聚类 | `sc.tl.leiden()`, `sc.tl.louvain()` | Leiden/Louvain 聚类 |
| 差异表达 | `sc.tl.rank_genes_groups()` | Wilcoxon、t-test、logreg 等 |
| 可视化 | `sc.pl.umap()`, `sc.pl.violin()`, `sc.pl.dotplot()` | 各种绘图函数 |
| 整合 | `sc.external.pp.harmony_integrate()` | Harmony 整合（需安装 harmonypy） |
| 轨迹分析 | `sc.tl.draw_graph()`, `sc.tl.paga()` | PAGA 和 force-directed 图 |

## 重要特性

- **AnnData 生态**：易于与 CellBender、scVelo、CellRank 等工具整合
- **大数据支持**：支持稀疏矩阵和增量计算
- **可定制性高**：所有步骤都可以参数调整
- **良好文档**：丰富的教程和 API 文档

## 常用工作流

```python
import scanpy as sc

# 读取 10x 数据
adata = sc.read_10x_mtx(
    'filtered_feature_bc_matrix/',
    var_names='gene_symbols',
    cache=True
)

# 质控
adata.var['mt'] = adata.var_names.str.startswith('MT-')  # 人类线粒体基因
sc.pp.calculate_qc_metrics(adata, qc_vars=['mt'], percent_top=None, log1p=False, inplace=True)

# 过滤
sc.pp.filter_cells(adata, min_genes=200)
sc.pp.filter_genes(adata, min_cells=3)

# 归一化和特征选择
sc.pp.normalize_total(adata, target_sum=1e4)
sc.pp.log1p(adata)
sc.pp.highly_variable_genes(adata, min_mean=0.0125, max_mean=3, min_disp=0.5)
adata = adata[:, adata.var.highly_variable]

# 归一化（回复并标准化）
sc.pp.regress_out(adata, ['total_counts', 'pct_counts_mt'])
sc.pp.scale(adata, max_value=10)

# 降维和聚类
sc.tl.pca(adata, svd_solver='arpack')
sc.pp.neighbors(adata, n_neighbors=10, n_pcs=40)
sc.tl.umap(adata)
sc.tl.leiden(adata)

# 差异表达
sc.tl.rank_genes_groups(adata, 'leiden', method='wilcoxon')
sc.pl.rank_genes_groups(adata, n_genes=25, sharey=False)
```

## 参考文献

- Wolf et al., 2018. *Scanpy: large-scale single-cell gene expression data analysis*. Genome Biology.
- Zhang et al., 2019. *PORE: a Python toolkit for analyzing single-cell and bulk RNA sequencing data*. Bioinformatics.

## 相关链接

- [Scanpy 官方文档](https://scanpy.readthedocs.io/)
- [Scanpy 教程](https://scanpy-tutorials.readthedocs.io/)
- [Scanpy GitHub](https://github.com/theislab/scanpy)