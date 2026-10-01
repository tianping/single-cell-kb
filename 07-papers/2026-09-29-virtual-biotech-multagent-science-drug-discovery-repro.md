# 斯坦福团队在 Science 发表用于自动化药物研发的多智能体框架（Virtual Biotech），8G 显卡本地复现 + 补充病理组学实验

> 收藏自「小罗碎碎念」公众号 · 2026-09-29 · https://mp.weixin.qq.com/s/jY3nCzbPcIDgqjq1IDgGiA
> 涉及文献：
> - Harrison G. Zhang, Peter Eckmann, Jiacheng Miao, Andrew B. Mahon, James Zou. **The Virtual Biotech: A multi-agent AI framework for therapeutic discovery and development**. Science. 2026-09-17（Stanford / PHD Biosciences；通讯 H.G. Zhang & James Zou）
> - 作者另提及同通讯作者 James Zou 的 Nature 论文（2026-09-16，也是智能体系统）

## 一、Virtual Biotech（VBT）系统架构

"虚拟生物技术公司"：AI 智能体扮演 CSO 和各科科学家，自主查数据库、写代码、做统计，模拟真实研发团队。

| 层级 | 内容 |
|------|------|
| CSO 办公室 | CSO Orchestrator（只拆解任务/路由/汇总结论，不碰数据）+ Chief of Staff（情报简报）+ Scientific Reviewer（挑刺质控） |
| 四个研发部门 | Target ID / Target Safety / Modality Selection / Clinical Officers，各辖专职科学家智能体（统计遗传、功能基因组、单细胞图谱、FDA 安全官、靶点生物学家、药理学家、临床试验等） |
| MCP 工具层 | 12 个 MCP 服务器统一封装十余个数据库：GWAS/QTL/gnomAD/ClinVar/PharmGKB（遗传学）、ChEMBL/OpenFDA/DailyMed（药物）、IntAct/Reactome/STRING/GO（互作通路）、ClinicalTrials.gov/PubMed（临床）、OMIM/Orphanet（疾病）、GTEx（组织表达）、CELLxGENE Census/Tabula Sapiens v2（单细胞）、DepMap/CRISPR/Tahoe-100M（功能基因组）、Mouse/Human Protein Atlas + 可成药性评分（分子靶点） |
| 数据层 | Open Targets（~80,000 靶点、1,450 万蛋白互作）、CELLxGENE Census（1.4 亿单细胞）、Tabula Sapiens、ClinicalTrials.gov、cBioPortal、ChEMBL |

- 原版跑在 **H100 + Claude Sonnet 4.5**，每案例 API 费 $50–59；40GB 全量参考数据
- 可审计性设计：每个结论可追溯到具体工具调用和数据文件；缺数据集时工具显式报错"失败也要留痕"

## 二、8G 显卡（RTX 4060）零 API 复现方案

- 全量复现不现实 → 选 3 个可本地运行的案例 + 冒烟测试
- **冒烟测试**：自写 `download_ot_subset.py` 只下载 13 个小数据集（613 MB vs 全量 40 GB），目录结构与原版一致；验证数据布局、Parquet 加载层、MCP 工具错误语义
- **案例 1（临床试验标注复现）**：复用作者开源的 37,075 条智能体标注表（clinical_trial_labels_reconciled.csv，覆盖 5.6 万试验），只补算靶点 tau 细胞特异指数，统计模型验证结论
- **案例 2（肺癌 B7-H3/CD276）**：
  - TCGA 肺腺癌（luad_tcga_pan_can_atlas_2018，477 名患者，cBioPortal REST API）：OS HR=1.62 (1.05–2.48), p=0.0281 (n=243) —— 与论文图 4F **逐位吻合**（论文 1.62, 1.05–2.49, p=0.028）；DFS HR=1.82 (0.99–3.36), p=0.0548 vs 论文 2.06，微小偏移
  - 单细胞：CELLxGENE HTAN MSK 组合图谱（14.7 万细胞）QC → 供体×细胞类型伪批量 → PyDESeq2；发现单一数据集正常臂无成纤维细胞（4 个供体、0 纤维细胞）→ 借 Tabula Sapiens 健康图谱 8 个供体当"正常臂"；结果印证 B7-H3 上调集中于成纤维细胞（SCLC log2FC=2.13，LUAD 1.79，与论文一致）
  - VBT 据此提名 **ADC（抗体药物偶联物）** 策略：靶向 B7-H3+ 成纤维细胞改变免疫抑制微环境 + 旁观者效应杀伤邻近阴性癌细胞
- **案例 3（gp130 轴）**：10 基因评分（OSMR、IL6ST、LIFR…）在 4 个 GEO 队列算 AUC，对应论文图 5C
- **tau 靶点优先级**：Tabula Sapiens（27 组织、110 万细胞），只下载 1,520 个药物靶点基因列（40GB+ → 697 MB），WSL 中合并为 1,136,218 细胞 × 1,513 基因"瘦身"图谱；tau 分布在 0.85 附近有明显峰；K-means 二分阈值论文 0.69（复算 0.650）
  - 复现 vs 论文：Phase I→II OR=1.245 (1.19–1.30), p=8.3e-25, n=11,359（论文 1.27）；主要终点 OR=1.121（论文 1.12）；次要终点 OR=1.120（论文 1.12）—— 一致到小数点后三位；不良事件率降低 42%（5.40% vs 9.25%，n=12,861）vs 论文 -32%（方向一致但更强，因复现时把各器官系统 AE 率先取均值再分析，论文按器官分别 beta 回归）
  - 技巧：多靶点药物取 tau 最小值（"最广谱靶点决定安全面"）；z-score 后逻辑回归
- 偷懒说明：原版 API 调 Claude，复现时改用本地 AI 编程助手做"拆解/方法/解释"流程

## 三、补充番外：VBT + GHIST 病理组学结合

GHIST（Nature Methods 2025）：深度学习从普通 H&E 切片预测每细胞 280 基因的空间表达。输入均为两个已完成复现的现成产物（GHIST 验证集预测 + VBT tau 表与试验标注）。

### 3.1 预测表达是否有效
- 全部基因 Spearman ρ=0.55（中等正相关）；Top-50 空间可变基因（SVG）ρ=0.651
- **几乎所有点在对角线下方：GHIST 预测 tau 系统性偏低**（把细胞特异性"平滑"掉）；作者猜测主因是复现 GHIST 时只训了 3 个 epoch
- 二分判定（tau 最高 1/4 归"细胞特异"）一致性 **71.6%** → 可用于给靶点"分堆"（细胞特异 vs 广泛表达），但不可过度解读 tau 绝对数值（定性风向标，非精确刻度尺）

### 3.2 虚拟图谱（7 类细胞 × 280 基因表达程序）
- 矩阵扁平化相关 r=0.633, p≈1e-219
- 按细胞类型差异明显：巨噬 0.77 / T 细胞 0.75 / 成纤维 0.70 / 髓系 0.69 / 恶性 0.68 均可信；**内皮 0.52、上皮 0.46 偏弱**（验证集中各仅 47/74 个细胞，可能方法边界+数据稀疏双重因素）
- 逐类 top-3 标记基因：T 细胞 PTPRC/TRAC、巨噬 LYZ 方向与相对高低保住；恶性 ERBB2/KRT7、上皮 KRT7 幅度缩水（预测信号被"压扁"）；内皮 AQP1/CD93 与上皮 TACSTD2 预测值甚至变负（细胞类型排序排错）
- 稀疏标记 tau 崩塌量化：FOXA1 实测 0.81 → 预测 0.46（PCC 0.69，较好）；CD19 0.73 → 0.62（PCC≈0 却保留良好）；**VWF/PECAM1/CD14/CD68 实测 0.65–0.91 → 预测 0.05–0.28（PCC≈0.04，几乎完全崩塌）**
- 规律：表达稀疏的标记基因（尤其内皮、髓系经典 marker）是"从形态猜表达"方法的固有软肋 → 喂给 VBT 时必须带 PCC 权重按基因预测质量加权

### 3.3 空间可视化（H&E 核分割 + 表达映射）
- GHIST 验证集按空间切分：1,072 个有实测真值的细胞集中在切片顶部条带，预测覆盖全图 9,517 个细胞
- 三基因对照：ERBB2（PCC=0.70，肿瘤区高表达与实测条带几乎重合）、PTPRC（PCC=0.55，免疫浸润分布对得上）、**CD274/PD-L1（预测失败，PCC 无法计算，均匀模糊）** —— 保留该图说明检查点分子在单细胞里表达稀疏且依赖转录状态而非形态，"病理形态能猜到细胞是谁，却猜不到每个细胞此刻表达了多少检查点"

## 四、资源

- 复现包 `VirtualBiotech-Repro`（原仓库 + 博主脚本 + 结果）：丢进 Codex / ZCode / WorkBuddy 等 AI 编程工具，"请阅读并执行 REPRODUCE.md" 即可自动复现
- 前置条件：本机有已完成的 GHIST 复现
- 博主打包上传至 MedX（需注册）

## 关联

- 与 ai-news-kb 中 James Zou / Stanford 智能体研究相关；可检索 "Zou James 智能体 Nature 2026"
- GHIST：Nature Methods 2025，空间转录组预测；H&E → 单细胞 280 基因
