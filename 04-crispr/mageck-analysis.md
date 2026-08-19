# CRISPR MAGeCK 分析

tags: [crispr, mageck, screen-analysis]

> 来源：[待补充] · 日期

## 背景 / 原理

MAGeCK (Model-based Analysis of Genome-wide CRISPR-Cas9 Knockout) 是分析 CRISPR 数据的标准工具，能够从 sgRNA 计数中识显著富集或耗竭的基因。

## 主要功能

- **MAGeCK test**：比较处理组 vs 对照组，识别 hits
- **MAGeCK rank**：基于冗余siRNA原理排名基因
- **MAGeCK MLE**：建模 sgRNA 效率和拷贝数变异
- **MAGeCK-VISPR**：可视化和报告

## 典型流程

```bash
# 1. 计数矩阵（已有：每个 sgRNA 在每个样本的 reads）
# 格式：sgrna_id sample1 sample2 ...

# 2. MAGeCK test
mageck test \
  -k sample_counts.txt \
  -t treated_rep1,treated_rep2 \
  -c control_rep1,control_rep2 \
  -n experiment_name \
  --norm-method total \
  --control-sgrna non_targeting_sgrna.txt

# 3. 查看结果
# experiment_name.gene_summary.pdf
# experiment_name.sgrna_summary.pdf
```

## 参考文献

- Li et al., 2014. *MAGeCK enables robust identification of essential genes from genome-scale CRISPR/Cas9 knockout screens*. Genome Biology.
- Bai et al., 2020. *MAGeCK-VISPR, an interactive roadmap for CRISPR-Cas9 screen analysis*. Nature Biotechnology.

## 相关链接

- [MAGeCK GitHub](https://github.com/li-lab/MAGeCK)
- [MAGeCK-VISPR](https://vispr.li-lab.org/)