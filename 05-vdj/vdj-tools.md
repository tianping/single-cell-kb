# VDJ Tools

tags: [vdj, tools, immunoseq]

> 来源：[待补充] · 日期

## 常用 VDJ 分析工具

| 工具 | 特点 |
|------|------|
| **MiXCR** | 快速、准确的 VDJ 序列获取和分析 |
| **IgBLAST** | NCBI 官方工具，用于抗体序列注释 |
| **VDJtools** | 多功能 VDJ 库分析和可视化 |
| **Change-O** | 联盟免疫学工具套件，用于克隆分析 |
| **ImmuneDB** | 数据库驱动的 VDJ 库分析平台 |
| **Partis** | 推断种系和体系突变 |
| **scRepertoire** | R 包，用于单细胞 VDJ 数据分析 |

## 推荐流程 (MiXCR + Change-O)

```bash
# 1. 使用 MiXCR 从测序数据中提取 VDJ 序列
mixcr align input_R1.fastq input_R2.fastq alignments.vdjca
mixcr assemble alignments.vdjca clones.clns
mixcr exportClones --chains IGH clones.clns clones.txt

# 2. 使用 Change-O 进行克隆分析
# 格式转换
ParseIgNCBI --db human --in clones.clns --out changeo_db --format changeo

# 3. 克隆分析
DefineClones --db changeo_db --act --dist 0.15
```

## 参考文献

- Bolotin et al., 2015. *MiXCR: universal software for fast and accurate processing of TCR and BGP repertoire sequencing data*. Nature Protocols.
- Yaari et al., 2012. *Models of somatic hypermutation targeting and substitution by explicitly simulating 5-mer motifs*. PLoS Computational Biology.
- Lindner et al., 2018. *Change-O: a toolkit for analyzing large-scale B-cell and T-cell receptor repertoire data*. Bioinformatics.

## 相关链接

- [MiXCR 文档](https://mixcr.ru/)
- [Change-O GitHub](https://bitbucket.org/kleinstein/change-o/src/master/)
- [scRepertoire](https://cran.r-project.org/package=scRepertoire)