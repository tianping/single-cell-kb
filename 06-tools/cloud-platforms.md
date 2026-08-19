# 云平台单细胞分析

tags: [tools, cloud, terra, dnanexus, sevenbridges]

> 来源：[待补充] · 日期

## 主流云平台

| 平台 | 特点 | 支持的数据类型 |
|------|------|----------------|
| **Terra** | Broad Institute，工作流管理（WDL/Cromwell），费用透明 | scRNA-seq, scATAC-seq, multiome, 空间转录组 |
| **DNAnexus** | 商业平台，强大的计算和协作功能 | 各种测序数据，支持自定义工作流 |
| **Seven Bridges (SBG)** | 基于 CWL，丰富的公开数据集和工作流 | 癌症 genomics 为主，也支持单细胞 |
| **BaseSpace (Illumina)** | Illumina 生态，易于与测序仪对接 | 10x Genomics 数据原生支持 |
| **Google Cloud Life Sciences** | 强大的计算资源，Flexible 定价 | 需要自行搭建工作流 |
| **AWS HealthOmics** | 专门针对 genomics 的托管服务 | 支持主流单细胞工作流 |

## 常用工作流在云上的实现

### Terra 上的常见工作流

1. **scRNA-seq 基础流程**
   - 输入：原始 FASTQ 或 BAM
   - 步骤：Cell Ranger → Seurat/Scanpy 质控 → 聚类 → 注释
   - 输出：分析结果（loom、h5ad、RDS）、可视化报告

2. **scATAC-seq 流程**
   - 输入：FASTQ
   - 步骤：Cell Ranger ATAC → Signac 质控 → 峰值叫峰 → 聚类 → motif 分析
   - 输出：片段文件、峰值矩阵、可视化

3. **Multiome 联合分析**
   - 输入：Multiome FASTQ
   - 步骤：Cell Ranger ARC → Signac WNN 整合 → 聚类 → 多模态注释
   - 输出：整合后的嵌入、可视化、标记基因

## 优势与注意事项

| 优势 | 说明 |
|------|------|
| **可重复性** | 工作流版本化，相同输入得到相同输出 |
| **可扩展性** | 轻松处理大规模数据（万甚至百万细胞） |
| **协作性** | 团队共享工作流、数据和结果 |
| **成本透明** | 按使用量付费，可预估成本 |
| **无需本地维护** | 不需要管理集群或软件依赖 |

| 注意事项 | 说明 |
|----------|------|
| **数据传输** | 大文件上传/下载可能耗时且产生费用 |
| **延迟** | 依赖网络，交互式探索可能不如本地流畅 |
| **学习曲线** | 需要掌握 WDL/CWL 或平台特定语法 |
| **费用控制** | 需要监控使用量以避免意外费用 |

## 示例：Terra 工作流 (WDL 片段)

```wdl
task CellRangerCount {
    File fastq_tar
    String sample_name
    String reference_dir
    Int nthreads
    Int memory_mb

    command {
        cellranger count \
            --id=${sample_name} \
            --fastqs=${fastq_tar} \
            --transcriptome=${reference_dir} \
            --localcores=${nthreads} \
            --localmem=${memory_mb} \
            --no-bam
    }
    runtime {
        docker: "quay.io/biocontainers/cellranger:7.1.0--h5d0b8e3_0"
        memory: "${memory_mb}MB"
        cpu: "${nthreads}"
    }
    output {
        File matrix = "${sample_name}/outs/filtered_feature_bc_matrix.h5"
    }
}

workflow scRNAseq {
    Input {
        File fastq_tar
        String sample_name
        String reference_dir
        Int nthreads
        Int memory_mb
    }
    call CellRangerCount {
        input: 
            fastq_tar=fastq_tar,
            sample_name=sample_name,
            reference_dir=reference_dir,
            nthreads=nthreads,
            memory_mb=memory_mb
    }
    Output {
        File matrix = CellRangerCount.matrix
    }
}
```

## 参考文献

- Levine et al., 2015. *Data-driven phenotypic dissection of AML reveals progenitor-like cells that correlate with prognosis*. Cell (早期使用云平台分析单细胞数据的例子)。
- Regev et al., 2017. *The Human Cell Atlas*. Nature (描述了大规模协作和云计算的需求)。

## 相关链接

- [Terra 官方网站](https://terra.bio/)
- [Terra 工作流库](https://github.com/DataBiosphere/tdp-workflows)
- [DNAnexus 单细胞解决方案](https://www.dnanexus.com/solutions/single-cell)
- [Seven Bridges Cancer Genomics 公开数据](https://www.sbgenomics.com/public-data/)
- [Google Cloud Life Sciences 文档](https://cloud.google.com/life-sciences)
- [AWS HealthOmics](https://aws.amazon.com/healthomics/)