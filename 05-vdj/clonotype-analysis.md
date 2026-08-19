# Clonotype 分析

tags: [vdj, clonotype, immune-repertoire]

> 来源：[待补充] · 日期

## 背景 / 原理

克隆型（clonotype）是指具有相同V基因、D基因、J基因和CDR3氨基酸序列的T或B细胞群体，代表同一来源的克隆扩增。

## 关键概念

- **V基因**：可变区基因
- **D基因**：多样性基因（仅在重链和TCR β/δ链中存在）
- **J基因**：连接区基因
- **CDR3**：第三补决定区，决定抗原特异性
- **克隆大小**：具有相同克隆型的细胞数量

## 分析步骤

1. **序列获取**：从测序数据中获得完整的VDJ重排序列
2. **基因分配**：将序列比对到VDJ基因库，确定V、D、J基因使用
3. **CDR3提取**：提取CDR3区域的核苷酸和氨基酸序列
4. **克隆型定义**：根据V基因、J基因和CDR3氨基酸序列分组
5. **克隆扩增分析**：计算每个克隆型的频率和绝对数量
6. **多样性指数**：香农熵、辛普森指数等
7. **样本间比较**：追踪克隆型在不同样本或时间点的变化

## 常用分析维度

| 维度 | 说明 |
|------|------|
| 克隆大小分布 | 大多数克隆型小，少数扩增克隆型大 |
| V/J 基因使用偏好 | 某些基因家族使用更频繁 |
| CDR3 长度分布 | 反映结构约束 |
| 克隆型重叠度 | 不同样本间共享的克隆型比例 |
| 突变负荷 | 尤其在IgG中，体细胞超突变（SHM） |

## 推荐工具

| 工具 | 特点 |
|------|------|
| **scRepertoire** | R 包，专门用于单细胞 VDJ 数据 |
| **Change-O** | 免疫学分析套件，强大的克隆分析功能 |
| **Immunarch** | R 包，免疫受体数据分析和可视化 |
| **VDJtools** | 库多样性分析和样本比较 |
| **AlleleTracker** | 追踪等位基因使用和突变 |

## R 示例 (scRepertoire)

```r
library(scRepertoire)
library(Seurat)

# 假设 seurat 对象中已经有 VDJ 信息（通常在 meta.data 中）
# 或者从外部文件读取
contig_list <- readCsvFiles("path/to/filtered_contig_annotations.csv")

# 合并到 Seurat 对象
seurat_obj <- combineExpression(contig_list, seurat_obj, cloneCall="gene+nt")

# 克隆型大小分布
clonalProportion(seurat_obj, cloneCall = "gene+nt")
cloneSizeDistribution(seurat_obj, cloneCall = "gene+nt")

# 样本间克隆型重叠
clonalOverlap(seurat_obj, cloneCall = "gene+nt", method = "overlap")

# 可视化
clonalHomeostasis(seurat_obj, cloneCall = "gene+nt")
```

## 参考文献

- Vander Heiden et al., 2014. *pRESTO: a toolkit for processing high-throughput sequencing reads of immune repertoires*. Frontiers in Immunology.
- Gupta et al., 2015. *Change-O: a toolkit for analyzing large-scale B-cell and T-cell receptor repertoire data*. Bioinformatics.
- Borcherds et al., 2020. *scRepertoire: a toolkit for single-cell immune receptor analysis*. Frontiers in Immunology.

## 相关链接

- [scRepertoire 文档](https://cran.r-project.org/package=scRepertoire)
- [Change-O 文档](https://bitbucket.org/kleinstein/change-o/src/master/)
- [Immunarch](https://immiunarch.github.io/immunarch/)