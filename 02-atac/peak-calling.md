# scATAC-seq Peak Calling

tags: [atac, peak-calling, macs2, genrich]

> 来源：[待补充] · 日期

## 背景 / 原理

scATAC-seq 数据的 Peak Calling 旨在识别开放染色质区域。

## 主流工具

| 工具 | 特点 |
|------|------|
| **MACS2** | 经典、广泛使用，需调整参数 |
| **Genrich** | 针对 ATAC-seq 设计、快 |
| **PEAKachu** | 基于隐马尔可夫模型 |
| **ATACseqQC** | QC + peak calling 结合 |

## 典型流程 (MACS2)

```bash
# 1. 过滤线粒体和黑名单地区
samtools view -b -F 0x04 input.bam \| samtools view -b -q 30 - \| samtools sort -o filtered.bam
samtools index filtered.bam

# 2. 调用 peaks（建议使用 --nomodel --extsize 147 --shift -73）
macs2 callpeak -t filtered.bam -f BAMPE -g hs -n sample --nomodel --extsize 147 --shift -73 --keep-dup all --call-summits

# 3. 获取 narrowPeak 文件（peak 位置）
# 4. 使用 bedtools 生成峰值矩阵（每个峰值 × 每个细胞的 counts）
```

## 质控指标

- **FRiP (Fraction of Reads in Peaks)**：> 0.1 通常可接受
- **TSS Enrichment Score**：> 6 表明片段定位良好
- **黑名单比例**：应尽量低
- **碱基转换率**（若有）：检测未完全转换的情况

## 参考文献

- Zhang et al., 2008. *Model-based analysis of ChIP-Seq (MACS)*. Genome Biology.
- Yip et al., 2019. *Genrich: a fast peak caller for ATAC-seq and ChIP-seq data*. Bioinformatics.
- Corces et al., 2017. *An improved ATAC-seq protocol reduces background and enables interrogation of frozen tissues*. Nature Methods.

## 相关链接

- [MACS2 文档](https://maccs2.github.io/)
- [Genrich GitHub](https://github.com/jsh58/Genrich)
- [Signac ATAC-seq 教程](https://stuartlab.org/signac/articles/atac_vignette.html)