# Multiome 常用工具

tags: [multiome, tools, signac, archr, maestro]

> 来源：[待补充] · 日期

## 工具概览

| 工具 | 语言 | 特点 |
|------|------|------|
| Signac | R | Seurat 生态，易于整合 RNA 和 ATAC |
| ArchR | R/Python | 功能全面，支持峰值、motif、足印、轨迹等 |
| MAESTRO | R | 统一框架，支持 multiome 数据 |
| Cobolt | Python | 基于矩阵分解的多模态整合 |
| LIGER | R | 也支持 multiome 整合 |
| scMultiome | Python | 专门针对 10x multiome 的工作流 |

## 安装与使用

以 Signac 为例：

```r
# 安装
if (!requireNamespace("Signac", quietly = TRUE)) {
    install.packages("Signac")
}
# 或从 GitHub 安装最新版
# devtools::install_github("stuartlab/signac")

library(Signac)
```

## 参考文献

- Wang et al., 2021. *Signac: a flexible framework for analyzing single-cell chromatin accessibility data*. Genome Biology.
- Granja et al., 2021. *ArchR is a scalable software package for integrative single-cell chromatin accessibility analysis*. Nature Genetics.
- Lopez et al., 2020. *MAESTRO: a scalable and accurate method for allele-specific expression quantification*. Nature Communications.

## 相关链接

- [Signac 官方网站](https://stuartlab.org/signac/)
- [ArchR 文档](https://www.archrproject.com/)
- [MAESTRO GitHub](https://github.com/GreenleafLab/MAESTRO)