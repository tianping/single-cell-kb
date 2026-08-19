# Loupe Browser

tags: [tools, visualization, loupe, 10x]

> 来源：[待补充] · 日期

## 概述

Loupe Browser 是 10x Genomics 提供的免费可交互式单细胞数据可视化和探索工具，无需编程即可探索 scRNA-seq、scATAC-seq、multiome 等数据。

## 核心功能

| 功能 | 说明 |
|------|------|
| **交互式可视化** | UMAP/t-SNE 聚类图、特征表达小提琴图/箱线图 |
| **基因探索** | 搜索基因、查看表达水平、共表达分析 |
| **簇注释** | 手动编辑簇标记、添加自定义注释 |
| **比较分析** | 同时加载多个样本进行条件比较 |
| **特征绘图** | 双参数散点图、热图、小提琴图组合 |
| **数据导出** | 导出子集、簇信息、表达矩阵 |
| **多模态支持** | 同时展示 RNA 和 ATAC 数据（对于 multiome） |

## 安装与使用

1. **下载**：从 [10x Genomics 官网](https://www.10xgenomics.com/products/loupe-browser) 下载桌面版（支持 Windows、macOS、Linux）
2. **打开数据**：支持以下格式：
   - 10x 生成的 `cloupe` 文件（推荐）
   - Loom 文件（`.loom`）
   - CSV 矩阵（需要手动指定基因和细胞注释）
3. **探索数据**：
   - 左侧：样本列表和簇列表
   - 中央：主可视化画板（UMAP/t-SNE）
   - 右侧：详细信息面板（表达值、注释等）

## 工作流建议

1. **质控初检**：用 Loupe 快速检查数据质量（线粒体比例、双细胞等）
2. **簇探索**：识别有趣的簇并查看标记基因
3. **条件比较**：加载多个样本（如处理 vs 对照）进行直观比较
4. **多模态关联**：对于 multiome 数据，同时查看 RNA 表达和 ATAC 峰值
5. **导出进一步分析**：导出感兴趣的子集或簇信息用于 downstream 分析（如轨迹、差异表达）

## 参考文献

- 10x Genomics. *Loupe Browser User Guide*. 10x Genomics Documentation.
- Zheng et al., 2017. *Massively parallel digital transcriptional profiling of single cells*. Nature Communications (描述了 Loupe 的前身)。

## 相关链接

- [Loupe Browser 下载页](https://www.10xgenomics.com/products/loupe-browser)
- [Loupe Browser 教程视频](https://www.10xgenomics.com/resources/videos/?product=loupe-browser)
- [Loupe Browser 用户指南 PDF](https://support.10xgenomics.com/single-cell-gene-expression/software/pdfs/latest/loupe_browser_user_guide.pdf)