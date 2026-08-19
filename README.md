# 单细胞分析知识库

> 收集整理单细胞多组学分析方法、工具与研究报告，覆盖 scRNA-seq、scATAC-seq、Multiome、CRISPR screens、VDJ 等主流技术。

---

## 知识地图

### 一、scRNA-seq 分析 (`01-rnaseq/`)
- **预处理**：质控指标、双细胞去除、归一化、批次校正
- **降维与聚类**：PCA/Harmony/ScVI、Leiden/Graph-based、分辨率选择
- **细胞注释**：Marker genes、参考图谱映射、自动注释工具
- **拟时序与 RNA Velocity**：Monocle3/Slingshot/Palantir、scVelo/CellRank
- **多数据集整合**：Harmony/Seurat v5/LIGER/ScVI

### 二、scATAC-seq 分析 (`02-atac/`)
- **Peak Calling**：MACS2/Genrich、细胞级 peak 矩阵构建
- **Motif 分析**：chromVAR/TFBS 预测、调控网络推断
- **与 RNA 整合**：Signac/ArchR/MAESTRO、基因活性评分

### 三、Multiome (RNA + ATAC) 联合分析 (`03-multiome/`)
- **联合分析流程**：WNN/Weighted Nearest Neighbors、多模态降维
- **工具对比**：Signac、ArchR、MAESTRO、SnapATAC2、Cobolt

### 四、CRISPR Perturbation Screens (`04-crispr/`)
- **文库设计**：sgRNA 设计原则、文库复杂度、对照设置
- **MAGeCK 分析**：MAGeCK-VISPR、MAGeCK-Flute、必需基因鉴定
- **Perturb-seq**：CROP-seq/ENCODE、单细胞表型读出、因果推断

### 五、VDJ (TCR/BCR) 受体库分析 (`05-vdj/`)
- **VDJ Tools**：VDJtools/ImmuneDB/Change-O、克隆型定义
- **克隆型分析**：克隆扩增、V/J 基因使用、SHM 分析、抗原特异性推断

### 六、通用工具横评 (`06-tools/`)
- **Seurat (R)**：v5 新特性、WNN、空间转录组支持
- **Scanpy (Python)**：AnnData 生态、scVI 集成、大规模数据
- **Loupe Browser**：可视化交互、无代码探索
- **云平台**：Terra、DNAnexus、Seven Bridges、Partek Flow

### 七、重要论文与综述 (`07-papers/`)
- **综述合集**：技术综述、方法比较、最佳实践
- **新方法论文**：新算法、新工具、benchmark 研究

### 八、资源与教程 (`08-resources/`)
- **公开数据集**：PBMC、Tabula Sapiens、Human Cell Atlas、10x 官方数据
- **教程合集**：官方教程、社区教程、工作坊材料
- **课程与工作坊**：Satija Lab、Theis Lab、Linnarsson Lab 等

---

## 已收录笔记索引

| 分类 | 笔记 | 日期 | 标签 |
|------|------|------|------|
| 01-rnaseq | [差异表达分析](01-rnaseq/differential-expression.md) | 2026-08-19 | rnaseq, differential-expression, seurat, pseudobulk, deseq2, mast |
| 01-rnaseq | [质控与预处理](01-rnaseq/preprocessing.md) | 2026-08-19 | rnaseq, preprocessing, qc, seurat, harmony |
| 01-rnaseq | [降维、聚类与细胞注释](01-rnaseq/clustering.md) | 2026-08-19 | rnaseq, clustering, annotation, monocle3, celltypist |
| 01-rnaseq | [拟时序与 RNA Velocity](01-rnaseq/trajectory.md) | 2026-08-19 | rnaseq, trajectory, pseudotime, rna-velocity, scvelo, cellrank |
| 01-rnaseq | [多数据集整合](01-rnaseq/integration.md) | 2026-08-19 | rnaseq, integration, batch-correction, harmony, scvi |
| 02-atac | [Peak Calling](02-atac/peak-calling.md) | 2026-08-19 | atac, peak-calling, macs2, genrich |
| 02-atac | [Motif 分析](02-atac/motif-analysis.md) | 2026-08-19 | atac, motif, chromvar, transcription-factor |
| 02-atac | [与 RNA 整合](02-atac/integration-with-rna.md) | 2026-08-19 | atac, rna, integration, signac, archr |
| 03-multiome | [分析流程](03-multiome/workflows.md) | 2026-08-19 | multiome, rna, atac, wnn, signac |
| 03-multiome | [常用工具](03-multiome/tools.md) | 2026-08-19 | multiome, tools, signac, archr, maestro |
| 04-crispr | [Pool-seq 设计](04-crispr/pool-seq.md) | 2026-08-19 | crispr, pool-seq, library-design |
| 04-crispr | [MAGeCK 分析](04-crispr/mageck-analysis.md) | 2026-08-19 | crispr, mageck, screen-analysis |
| 04-crispr | [Perturb-seq](04-crispr/perturbseq.md) | 2026-08-19 | crispr, perturbseq, single-cell |
| 05-vdj | [VDJ Tools](05-vdj/vdj-tools.md) | 2026-08-19 | vdj, tools, immunoseq |
| 05-vdj | [Clonotype 分析](05-vdj/clonotype-analysis.md) | 2026-08-19 | vdj, clonotype, immune-repertoire |
| 06-tools | [Seurat](06-tools/seurat.md) | 2026-08-19 | tools, r, seurat, single-cell |
| 06-tools | [Scanpy](06-tools/scanpy.md) | 2026-08-19 | tools, python, scanpy, single-cell |
| 06-tools | [Loupe Browser](06-tools/loupe.md) | 2026-08-19 | tools, visualization, loupe, 10x |
| 06-tools | [云平台](06-tools/cloud-platforms.md) | 2026-08-19 | tools, cloud, terra, dnanexus, sevenbridges |
| 07-papers | *(暂无)* | — | — |
| 08-resources | *(暂无)* | — | — |

> 新增笔记时，请同步更新此表。

---

## 文件命名约定

- 笔记文件：小写英文连字符（如 `preprocessing.md`、`scvi-integration.md`）
- 剪藏/待整理文件：放 `inbox/`，命名 `YYYY-MM-DD-关键词.md`
- 每篇笔记顶部建议保留：
  ```
  # 标题
  tags: [rnaseq, preprocessing, seurat]
  > 来源：[文章/论文/文档名](链接) · 日期
  ```

---

## 收录流程（参考 ai-news-kb）

新内容收录时，**一次性完成**：

1. **创建笔记文件** → 对应分类目录
2. **更新 `_sidebar.md`** → 对应分类下添加链接
3. **更新 `README.md`** → 同步知识地图简介 + 索引表
4. **验证** → 读取确认无误
5. **Git push** → `cd ~/.openclaw/workspace/single-cell-kb && git add -A && git commit -m "add: 笔记标题" && git push`

---

## 使用方式

- **日常收集**：发链接/文件/想法给我，我抓取整理归档
- **主题深挖**：说"帮我整理一下 RNA velocity 的最新方法"，我搜索收集
- **工具对比**：说"对比一下 Seurat v5 和 Scanpy 在大数据集上的表现"，我生成对比笔记
- **文献跟踪**：重要期刊新刊、bioRxiv 预印本、会议墙报