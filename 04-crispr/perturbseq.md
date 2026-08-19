# CRISPR Perturb-seq

tags: [crispr, perturbseq, single-cell]

> 来源：[待补充] · 日期

## 背景 / 原理

Perturb-seq 将 CRISPR 基因敲除或激活与单细胞转录组读出相结合， umożliwia jednoczesne pomiar efektu genetycznego i transkriptomicznego w pojedynczej komórce.

## 实验流程

1. **文库构建**：包含引导RNA和巴码的慢病毒或腺相关病毒文库
2. **病毒包装与转导**：在目标细胞系中进行低MOI转导，以确保单一体每细胞只有一个引导
3. **培养与选择**：药物选择或富集转导阳性细胞
4. **单细胞捕获**：使用 10x、Drop-seq 或 Smart-seq2 等平台
5. **文库准备**：同时扩增引导巴码和转录组
6. **测序与分析**：分离引导和转录组读取，进行关联分析

## 分析要点

- **引导分配**：确定每个细胞携带哪个引导（需要考虑巴码碰撞）
- **质控**：引导捕获效率、转录组质控
- **差异表达**：比较携带特定引导的细胞 vs 对照或非靶向引导
- **基因集富集**：GSEA、GO 分析等
- **表型评估**：若有细胞表型读出（如 powierzchniowy marker），可做关联

## 常用工具

| 工具 | 特点 |
|------|------|
| **Seurat** | 通过 `CreateAssayObject` 整合引导信息，做差异表达 |
| **Scanpy** | 支持引导注释，`scvelo`、`scVelo` 可用 |
| **Cell Ranger CRISPR** | 10x 官方 pipeline，输出引导分配矩阵 |
| **CROP-seq** | 早期方法，利用 polyA 尾巴码 |
| **Perturbator** | R 包，专门用于 perturb-seq 分析 |

## 参考文献

- Dixit et al., 2016. *Perturb-Seq: Dissecting Molecular Circuits with Scalable Single-Cell RNA Profiling of Pooled Genetic Screens*. Cell.
- Datlinger et al., 2017. *Pooled CRISPR screening with single-cell transcriptome readout*. Nature Methods.
- Adamson et al., 2016. *A multiplexed single-cell scoring platform enables functional genomic screens for therapeutic targets and pathogenic viruses*. Science.

## 相关链接

- [10x Chromium CRISPR Screening Guide](https://www.10xgenomics.com/resources/assays/crispr-screening)
- [Seurat Vignette: CRISPR Guide RNA Capture](https://satijalab.org/seurat/articles/crispr_guide_rna_capture.html)