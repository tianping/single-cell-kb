# scATAC-seq Motif 分析

tags: [atac, motif, chromvar, transcription-factor]

> 来源：[待补充] · 日期

## 背景 / 原理

motif 分析推断哪些转录因子在特定染色质开放区域富集，从而调控基因表达。

## 主流方法

| 方法 | 原理 | 软件 |
|------|------|------|
| chromVAR | 计算每个细胞在每个 motif 上的偏差分数 | chromVAR (R) |
| Footprinting | 检测 TF 结合保护的切割片段 | TOBIAS, HINT-ATAC |
| Motif enrichment | 测试峰值中 motif 频率是否高于背景 | HOMER, MEME-ChIP, findMotifsGenome.pl |
| PWM scoring | 使用 position weight matrix 评分 | FIMO (MEME suite) |

## 推荐流程 (chromVAR + Signac)

```r
library(Signac)
library(Seurat)
library(chromVAR)
library(motifmatchr)
library(JASPAR2020)

# 1. 添加 motif information
obj <- AddMotifs(obj, genome = BSgenome.Hsapiens.UCSC.hg38)

# 2. 计算 motif activity
obj <- RunChromVAR(obj)
```

## 参考文献

- Schep et al., 2017. *chromVAR: inferring point-to-point transcription factor accessibility from single-cell epigenomic data*. Nature Methods.
- Quinn et al., 2020. *ATAC-seq footprinting and chromatin dynamics analysis with TOBIAS*. Nature Protocols.

## 相关链接

- [chromVAR 文档](https://greenleaflab.github.io/chromVAR/)
- [Signac Motif Analysis](https://stuartlab.org/signac/articles/motif_vignette.html)