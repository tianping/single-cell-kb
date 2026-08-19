# CRISPR Pool-seq 设计

tags: [crispr, pool-seq, library-design]

> 来源：[待补充] · 日期

## 背景 / 原理

CRISPR筛选文库设计是进行pool-seq实验的第一步，需要考虑覆盖度、特异性和对照设置。

## 设计原则

- **覆盖度**：每个目标基因多个gRNA（通常3-6个）
- **特异性**：避免脱靶效应，使用较高的特异性得分阈值
- **对照**：包括非靶向对照和必需基因对照
- **长度**：标准20nt引导序列+PAM

## 常用工具

| 工具 | 特点 |
|------|------|
| **CRISPOR** | 在线设计，提供脱靶评分 |
| **Benchling** | 商业平台，完整工作流 |
| **Zhang Lab CRISPR Design** | 广泛使用的学术工具 |
| **GeCKO v2** | 人类和小鼠基因组文库 |

## 参考文献

- Doench et al., 2016. *Optimized sgRNA design to maximize activity and minimize off-target effects of CRISPR-Cas9*. Nature Biotechnology.
- Shalem et al., 2014. *Genome-scale CRISPR-Cas9 knockout screening in human cells*. Science.

## 相关链接

- [CRISPOR](http://crispor.tefor.net/)
- [Addgene CRISPR Guide](https://www.addgene.org/crispr/guide/)