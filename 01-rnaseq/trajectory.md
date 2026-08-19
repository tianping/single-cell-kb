# scRNA-seq 拟时序与 RNA Velocity

tags: [rnaseq, trajectory, pseudotime, rna-velocity, monocle3, slingshot, scvelo]

> 来源：[待补充] · 日期

## 背景 / 原理

- **拟时序**：从静态快照推断细胞分化动态过程
- **RNA Velocity**：利用剪接/未剪接 mRNA 比例预测细胞未来状态

## 主流方法对比

| 方法 | 类型 | 输入 | 优点 | 缺点 |
|------|------|------|------|------|
| Monocle 3 | 拟时序 | 表达矩阵 | 学习主图、分支点检测 | 对起始点敏感 |
| Slingshot | 拟时序 | 聚类 + 降维 | 简单、可解释 | 需预先聚类 |
| Palantir | 拟时序 | 扩散映射 | 熵/分化势量化 | 计算量大 |
| RNA Velocity (scVelo) | 动力学 | spliced/unspliced | 方向性、短时预测 | 需 intron reads |
| CellRank | 统一框架 | Velocity + 拟时序 | 结合两者、终态概率 | 依赖 velocity 质量 |

## 典型流程 (Monocle 3)

```r
library(monocle3)
cds <- new_cell_data_set(expression_data = obj@assays$RNA@data,
                          cell_metadata = obj@meta.data,
                          gene_metadata = data.frame(gene_short_name = rownames(obj)))
cds <- preprocess_cds(cds, num_dim = 50)
cds <- reduce_dimension(cds, reduction_method = "UMAP")
cds <- cluster_cells(cds)
cds <- learn_graph(cds)
cds <- order_cells(cds, root_cells = root_cell_ids)
pseudotime <- pseudotime(cds)
```

## RNA Velocity (scVelo) 要点

```python
import scvelo as scv
adata = scv.read('adata.h5ad')
scv.pp.filter_and_normalize(adata)
scv.pp.moments(adata, n_pcs=30, n_neighbors=30)
scv.tl.recover_dynamics(adata)  # 动力学模型
scv.tl.velocity(adata, mode='dynamical')
scv.tl.velocity_graph(adata)
scv.pl.velocity_embedding_stream(adata, basis='umap')
```

| 模式 | 适用场景 |
|------|----------|
| steady-state | 快、数据少 |
| dynamical | 精准、需足够 unspliced reads |

## 关键质控

- Velocity 需 **intron reads**（10x v3 chemistry 或 STARsolo `--soloType CB_UMI_Complex`）
- 细胞周期效应会干扰，建议回归
- 稀疏性高时 dynamical 模式不稳定

## 参考文献

- Qiu et al., 2017. *Reversed graph embedding resolves complex single-cell trajectories*. Nature Methods (Monocle 2).
- Cao et al., 2019. *The single-cell transcriptional landscape of mammalian organogenesis*. Nature (Monocle 3).
- Bergen et al., 2020. *RNA velocity — current challenges and future perspectives*. Molecular Systems Biology (scVelo).
- Lange et al., 2022. *CellRank for directed single-cell fate mapping*. Nature Methods.

## 相关链接

- [Monocle 3 教程](https://cole-trapnell-lab.github.io/monocle3/docs/)
- [scVelo 官方教程](https://scvelo.readthedocs.io/)
- [CellRank 文档](https://cellrank.readthedocs.io/)