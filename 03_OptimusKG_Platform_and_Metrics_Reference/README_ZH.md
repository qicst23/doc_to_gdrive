# OptimusKG-TxGNN（`clinical_v4`、`clinical_v5`、安进 `v5`、`AMG 757 v4.3.2` 与 `AMG 691 v2.0` 转化发现套件 — 中文总览）

> **对应英文源文档**：[README.md](file:///home/nchen10/antigravity_prjs/kg/README.md) | **中文权威指标总账**：[METRICS_ZH.md](file:///home/nchen10/antigravity_prjs/kg/docs/zh/METRICS_ZH.md) ([EN](file:///home/nchen10/antigravity_prjs/kg/METRICS.md)) | **跨设备项目交接手册**：[ANTIGRAVITY_PROJECT_HANDOFF_ZH.md](file:///home/nchen10/antigravity_prjs/kg/docs/zh/ANTIGRAVITY_PROJECT_HANDOFF_ZH.md) ([EN](file:///home/nchen10/antigravity_prjs/kg/ANTIGRAVITY_PROJECT_HANDOFF.md))

基于 **OptimusKG v3.2（`clinical_v4` / `clinical_v5` / `amgen_v5`）**（`139,439` 个连通临床节点，`9,385,536` 条双向边，涵盖 `R = 50` 种无符号或 `R = 59` 种有符号/分型规范关系）构建的可扩展、严格防数据泄露生物医学知识图谱基础模型（单模型 GNN 参数量 `1.05 亿` 至 `9.442 亿`；`24` 检查点跨架构集成；全项目 `42 / 42` 个 `.pt` 权重包 `100%` 通过 `torch.load(..., weights_only=True)` 安全反序列化验证），全面赋能六大转化医学与生物统计核心任务：

1. **目标一（Goal 1）——安进全管线适应症重定位（`47` 款商业化、生物类似药、1–3 期临床及临床前资产 $\times$ `18,025` 种临床疾病）**：逐疾病去枢纽偏置标准化、同种子无泄露 `AmgenRepurposeRanker`、MONDO 本体有向无环图（`47,892` 条边）+ `OnSIDES v3.1.1`（`472 万`对）本体/毒性双重屏蔽、分层 Mondrian 保形 `FDR` 校准，以及有符号 `d = 3072` `GraphMask` 机制路径解释。
2. **目标二（Goal 2）——泛疾病联合给药方案与专利/BD 商务合作引擎（`OptimusCombo-Net`）**：严格置换对称的三头神经网络，在 `1,059` 种疾病的 `53,952` 组临床三元组上采用 MONDO 根节点分组交叉验证训练，优先筛选 **Tier 1（`安进 × 安进`）** 与 **Tier 2（`安进 × FDA 专利到期仿制药/生物类似药`）** 方案。
3. **交互式业务交付与 `< 1 秒`（`0.96s`）机密靶点纯 CPU 评估**：实时 **15 视图安进转化发现工作台与自解释知识图谱浏览器（`:8004`）**、中英文双语可编辑幻灯片（`.pptx`）与免服务器单文件演示文稿（`.html`），以及无需 GPU 即可在 **`0.96 秒`内完成任意机密 `AMG-XXXX` 靶点签名全疾病评估**的跨 Python 版本 `post_ultra_inference_pack/`。
4. **【权威封版】IMDELLTRA®（`AMG 757` / 塔拉妥单抗）`v4.3.2` 审校证据驱动适应症拓展与双轨试验模型（`docs/zh/16` & `results_v4/`）**：构建在 `260` 条经核验溯源证据记录与 `120` 项 `DLL3` 试验离线富集快照之上，将 **表 A 已承诺 SCLC 基线**（`12` 项在研承诺拓展/生命周期研究 + `7` 项历史完成基线研究，`EXP-S1 + S2 + S3 = $2,610.8M` [`2036` 法定下限] / `$3,306.6M` [`2039` 专利情景]）与 **表 B 增量拓展及双轨罕见非 SCLC 期权**（**路线 `V1` 外部 IIT 信号联盟** 统筹 `11` 项开放 IIT [`N = 517`；`0.90` 基准 `+$1.8M` vs `0.75` 供药折损敏感性 `-$4.3M`；贝叶斯样本信息期望值 `EVSI = +$12.94M`（精确 `2036` 独占期截断为 `$12.35M`）] vs **路线 `V2` 新建安进申办注册主篮子** [`+$10.6M`] vs `V0` 独立建组 [`-$34.6M`]）严格物理隔离；统一整合 `Q1` 四链 NUTS 多项模型（`LOSO` `100%` 覆盖）、`Q2` `15` 队列纯 `TCE` `EXNEX`（`a0 = 0.50` 跨药幂先验；肺 `LCNEC` 在零塔拉妥单抗直接数据下主后验 `44.5%` vs 去 Obrixtamig `29.9%`）、`32` 抗原严格截断前新适应症回测 `PE1`（封存 `(16, 8)` `tanh` `lbfgs` `alpha=8.0` `max_iter = 300` 下 `P5 = 0.378` vs 静态 `M5 = 0.501`，量化 `+0.123` 截断后度数虚高，`wp25_passed = False` -> 维持 `Q5` 退役）、无偏 `Q4` 核验（`recall@50 = 0.000`）、附录 B `Q6` 离子通道安全护栏（`Ion Guard`）、三档制度（`A1`–`A3`）`WP9` 篮式权衡、四级低成本维护模式 SOP，以及全部 `3/3` 份 `.ots` 均经比特币区块 `#975,929`、`#975,963`、`#976,004` 与 `#976,009` 工作量证明（PoW）离线核验的 `41` 条追加式预测账本（严格物理分离 `5` 项 `q2_empirical_model` 与 `4` 项 `analyst_prior` 预测）。
5. **IMDELLTRA®（`AMG 757`）历史基准 `Option-1 v2.0/v3.2` 原发患者活检/单细胞图谱与 `Option-2 OptimusBiTE-Head`（`v1.3 g4_max`，`docs/zh/11–13`）**：完整保留 `2,215` 例原发肿瘤活检转录组（含 `MCC GSE235092`、`MTC`、`TARGET-NBL`）、`60,026` 个临床 SCLC 单细胞（`GSE281740 + GSE324849`）与 `SVD-LoRA + Modality-FiLM + ESM-2 3B` 表面组神经头（`2,418` 个人类膜表面蛋白 `<= 14 nm` 突触间隙及 `BACE1` 脱落酶图谱；在 `v4.3.2` 附录 B `Ion Guard` 护栏下，`KCNB2`/`SCN3A` 因中枢/心肌电压门控通道风险降级，**`DLL3 + IGSF9`** [`0.4745`] 晋升为赛道 3 第 `#1` 洁净候选构型）。
6. **【权威封版】AMG 691 `v2.0`（抗 `CD30` [`TNFRSF8`] / `CD30L` [`TNFSF8`]，1 期临床 `NCT06637371` & `NCT07745907`）联合用药与下一代双抗发现（`docs/zh/14–15`）**：整合 `24` 个检查点、原代人类过敏性哮喘气道单细胞（`GSE193816`，`52,152` 个细胞，证实 `0.29%` 上皮重叠、`15.69×` `CD30L/OX40L` 扩增与 `94.99%` `FOXP3+ Treg` 豁免窗口）及 `10` 个难治性人类活检队列（`n = 1,642`，含 `Dupilumab` 治疗 3 个月后 `CD30L` `105.6%` 残留实证），在非循环 `14` 锚点 2025–2026 人类临床基准上实现盲测 **`AUROC = 1.0000`**。

---

## 文档体系全景导航

| 文档 | 核心内容 |
| :--- | :--- |
| **[METRICS_ZH.md](file:///home/nchen10/antigravity_prjs/kg/docs/zh/METRICS_ZH.md)** / **[METRICS.md](file:///home/nchen10/antigravity_prjs/kg/METRICS.md)** | **机器权威验证指标总账（中/英，第 1–7 节）**：由 [`generate_metrics_ledger.py`](file:///home/nchen10/antigravity_prjs/kg/generate_metrics_ledger.py) 直接从 JSON/Parquet 结果生成，涵盖 PrimeKG 三层对比、外部 TxGNN `94.5%` 重叠证明、冷启动靶点盲测、无规则 NCT 联用盲测、`AMG 757 v4.3.2` 与 `AMG 691 v2.0` 全部实测指标 |
| **[ANTIGRAVITY_PROJECT_HANDOFF_ZH.md](file:///home/nchen10/antigravity_prjs/kg/docs/zh/ANTIGRAVITY_PROJECT_HANDOFF_ZH.md)** / **[ANTIGRAVITY_PROJECT_HANDOFF.md](file:///home/nchen10/antigravity_prjs/kg/ANTIGRAVITY_PROJECT_HANDOFF.md)** | **项目全局历史与跨设备交接手册（中/英）**：完整架构背景、`2026-11-15` `v4.3.2` / `v2.0` 封版状态、Google Drive 云盘文件 ID、`doc_to_gdrive/` 纯净外发包目录结构及离线验证/Colab 工作流 |
| **[forecasts/README_ZH.md](file:///home/nchen10/antigravity_prjs/kg/forecasts/README_ZH.md)** / **[forecasts/README.md](file:///home/nchen10/antigravity_prjs/kg/forecasts/README.md)** | **比特币 PoW 锚定追加式前瞻临床预测账本说明（中/英，`v4.3.2`）**：跨 `2026_11`、`2026_11b`、`2026_11c` 共 `41` 条密封记录、`3/3` 落盘比特币区块 `#975,929`、`#975,963`、`#976,004` & `#976,009` 离线核验、`11` 条作废修订、`FREEZE-P5`、`TRIG-01..09` 及严格分离的 `5` 项 `Q2` vs `4` 项分析员先验记分卡 |
| **[NEXT_STEPS_TODO_AND_TRAVEL_GUIDE.md](file:///home/nchen10/antigravity_prjs/kg/NEXT_STEPS_TODO_AND_TRAVEL_GUIDE.md)** | **差旅远程操作与后续路线图（英）**：汇报前核查（`A1`–`A7`）、笔记本一键运行 Colab `Cell 4D` 指南（`B1`–`B3`）、返程消融实验（`C1`–`C6`）与全管线拓展规划（`D1`–`D5`） |
| **[01](file:///home/nchen10/antigravity_prjs/kg/docs/01_DESIGN_AND_IMPLEMENTATION.md)–[10_CRITICAL_AUDIT_AND_AMGEN_NEXT_STEPS_PLAN.md](file:///home/nchen10/antigravity_prjs/kg/docs/10_CRITICAL_AUDIT_AND_AMGEN_NEXT_STEPS_PLAN.md)** | **核心工程与生物统计审计系列专著（第 01–10 卷，英）**：涵盖双后端 `HeteroRGCN`、训练与 Colab 手册、历史下游基线、方法学综述、Goal 1/2 总体蓝图、15 视图工作台设计、`OptimusKG v3.2` 数据工程、`30` 模型注册表、排序校准及生物统计根因审计（`RC-1`..`RC-5`） |
| **[11_AMG757_OPTION1_COMPLETE_GUIDE_AND_COLLEAGUE_PLAYBOOK_ZH.md](file:///home/nchen10/antigravity_prjs/kg/docs/zh/11_AMG757_OPTION1_COMPLETE_GUIDE_AND_COLLEAGUE_PLAYBOOK_ZH.md)** / **[docs/11](file:///home/nchen10/antigravity_prjs/kg/docs/11_AMG757_OPTION1_COMPLETE_GUIDE_AND_COLLEAGUE_PLAYBOOK.md)** | **历史基准 AMG 757 Option-1（`v2.0` / `v3.2` 原发活检图谱）临床手册（中/英，综合评分已被 `docs/zh/16` 取代）**：`DLL3 × CD3` BiTE 机理拆解、未加权 Mondrian 保形校准、分子门控注册表、`2,215` 例原发肿瘤 RNA-seq 与 Simon 两阶段计算器 |
| **[12_AMG757_OPTION2_MODALITY_CONDITIONED_BITE_HEAD_ZH.md](file:///home/nchen10/antigravity_prjs/kg/docs/zh/12_AMG757_OPTION2_MODALITY_CONDITIONED_BITE_HEAD_ZH.md)** / **[docs/12](file:///home/nchen10/antigravity_prjs/kg/docs/12_AMG757_OPTION2_MODALITY_CONDITIONED_BITE_HEAD.md)** | **历史基准 Option-2 `OptimusBiTE-Head`（`v1.3 g4_max`）架构与 Colab `G4 96 GB` 手册（中/英，受 `docs/zh/16` `PE1` 回测与附录 B `Ion Guard` 约束）**：`SVD-LoRA + Modality-FiLM`、`ESM-2 3B` ECD、`<= 14 nm` 突触生物物理与双靶点或门挽救 |
| **[13_AMG757_COMPREHENSIVE_INDICATION_EXPANSION_AND_RECOMMENDATIONS_ZH.md](file:///home/nchen10/antigravity_prjs/kg/docs/zh/13_AMG757_COMPREHENSIVE_INDICATION_EXPANSION_AND_RECOMMENDATIONS_ZH.md)** / **[docs/13](file:///home/nchen10/antigravity_prjs/kg/docs/13_AMG757_COMPREHENSIVE_INDICATION_EXPANSION_AND_RECOMMENDATIONS.md)** | **已被 `v4.3.2` 取代的第三阶段（`v3.2.1`）AMG 757 历史基准报告（中/英）**：保留供与 `v4.3.2` 对照（`67.8%` 结构性 `IHC_pos` Shapley 重复计分审校记录于 `results_v4/amg757_pillar_attribution.json`） |
| **[14_AMG691_COMBINATION_THERAPY_AND_BISPECIFIC_DISCOVERY_ZH.md](file:///home/nchen10/antigravity_prjs/kg/docs/zh/14_AMG691_COMBINATION_THERAPY_AND_BISPECIFIC_DISCOVERY_ZH.md)** / **[docs/14](file:///home/nchen10/antigravity_prjs/kg/docs/14_AMG691_COMBINATION_THERAPY_AND_BISPECIFIC_DISCOVERY.md)** | **【权威真值】AMG 691 `v2.0`（`NCT06637371` & `NCT07745907`）联合用药、组织驻留记忆 T 细胞生物学与下一代双抗报告（中/英）**：`GSE193816` `52,152` 气道单细胞、`1,642` 例难治性人类活检、`14` 锚点 `AUROC = 1.0000` 及哮喘/鼻息肉、AD、UC/CD、HS 四大核心领域联用路线图 |
| **[15_AMG691_APPROACH_MODELING_SCORING_AND_WORKFLOW_REPORT_ZH.md](file:///home/nchen10/antigravity_prjs/kg/docs/zh/15_AMG691_APPROACH_MODELING_SCORING_AND_WORKFLOW_REPORT_ZH.md)** / **[docs/15](file:///home/nchen10/antigravity_prjs/kg/docs/15_AMG691_APPROACH_MODELING_SCORING_AND_WORKFLOW_REPORT_EN.md)** | **【权威真值】AMG 691 `v2.0` 数学建模、评分方程与端到端工作流技术手册（中/英）**：完整评分公式、药理安全硬门控、2025–2026 非循环临床锚点校准与复现管线规范 |
| **[16_AMG757_V4_EVIDENCE_MODEL_ZH.md](file:///home/nchen10/antigravity_prjs/kg/docs/zh/16_AMG757_V4_EVIDENCE_MODEL_ZH.md)** / **[docs/16](file:///home/nchen10/antigravity_prjs/kg/docs/16_AMG757_V4_EVIDENCE_MODEL.md)** | **【权威真值】IMDELLTRA®（AMG 757）`v4.3.2` 审校封版证据驱动适应症拓展与双轨试验报告（中/英）**（配套双语离线工作台 [AMG757_Tarlatamab_V4_Evidence_Studio.html](file:///home/nchen10/antigravity_prjs/kg/results_v4/AMG757_Tarlatamab_V4_Evidence_Studio.html) 与 18 页双语 PPTX [EN](file:///home/nchen10/antigravity_prjs/kg/results_v4/AMG757_Biostatistics_Evidence_Model_v4_3_EN.pptx) / [ZH](file:///home/nchen10/antigravity_prjs/kg/results_v4/AMG757_Biostatistics_Evidence_Model_v4_3_ZH.pptx)）：`260` 条溯源 + `120` 项 `DLL3` 试验盘点、表 A（`12` 在研 + `7` 历史，`$2,610.8M`）/ 表 B（`V0/V1/V2` + `EVSI = +$12.94M`［精确 `2036` 独占期截断为 `$12.35M`］）物理隔离、`a0 = 0.50` 跨药 `Q2`、`PE1` 防泄露回测（`Q5` 退役）、无偏 `Q4`（`recall@50 = 0.000`）、附录 B `Ion Guard`、`A1–A3` 篮式权衡、四级维护 SOP 及 `3/3` 落锤结算比特币 PoW 封存预测账本 |

早期设计日志与已退役草稿归档于 [docs/archive/](file:///home/nchen10/antigravity_prjs/kg/docs/archive/README.md)。

---

## 核心实测基准总览（`2026 年 11 月 15 日` — 权威源：[`METRICS_ZH.md`](file:///home/nchen10/antigravity_prjs/kg/docs/zh/METRICS_ZH.md) / [`METRICS.md`](file:///home/nchen10/antigravity_prjs/kg/METRICS.md)）

* **与 PrimeKG 的诚实三层知识图谱对比**：
  * **Tier A（原始 OptimusKG v3.2）**：`192,813` 个节点（`1.49×`），`21,834,669` 条边（`5.39×`），整合 `65` 个数据源。
  * **Tier B（精炼 GNN 训练图 `clinical_v4`）**：`139,439` 个节点（`1.08×`），`9,385,536` 条双向边（`1.16×`），`30` 种关系名称（`50` 种有向规范三元组），`18,640 + 23` 种药物（`2.35×`；其中 `3,353` 种［`18.0%`］含 `>=1` 条获批适应症），`9,895` 条适应症边（覆盖 `1,268` 种疾病；`18,025` 种疾病中 `93.0%` 尚无获批药物），`11,673` 条禁忌症边（`0.38×`）。
  * **Tier C（第二阶段药理、安全与专利侧边层）**：`36,641` 条有符号作用机制边、`1,345,373` 条四分类药物相互作用（DDI）、`890 万`对 Open Targets `L2G`、`34,718` 条 MONDO DAG 边、`OnSIDES v3.1.1`（`44,607` 对药物–不良反应）、FDA 橙皮书/紫皮书（`2,494` 款专利到期仿制药/生物类似药）、`768` 维 `MedCPT` 特征库及切分锁定的 `59` 关系有符号图 `clinical_v5`。
* **生产级 `24` 检查点集成模型（`fp32` 权重 + `d = 3072` 第 3 阶段 `GraphMask`）与自有测试集分母统一口径**：
  * 最大单模型规模 = **`9.442 亿` GNN 参数（`d = 3072`，官方 `26.3M` TxGNN 的 `35.9×`）**；单模型自有测试集 Indication AUPRC 峰值达 **`0.3504`**（`seed = 1`，范围 `0.2843–0.3504`）。
  * **统一分母口径**：在 **`3,353` 种活跃临床药物**（`evaluator.py` 分母）上，24 模型同种子集成取得 **`0.3379` 三种子平均 AUPRC（`seed = 1` 达 `0.3603`，超越最佳单模型 `0.3504`）**；在 **全部 `18,640` 个药物节点**（扩大 `5.56×` 的分母）上达 **`0.2460`**（全面超越各单一 6 模型家族 `0.2003–0.2343`）。
* **与外部 Drive TxGNN 的唯一疾病聚类配对自助法比较（`207` 共享疾病 × `5,297` 共享药物，`10,000` 次重采样）**：
  * **全部 `207` 种共享疾病（`398 / 421 = 94.5%` 的测试对位于 Drive TxGNN 的 PrimeKG 训练集中！）**：**`AUPRC = 0.4380` vs. `0.2156`**（**`+0.2224 [+0.1545, +0.2880]`，`2.03×`**，`P = 1.0000`），`Recall@50 = 0.6468` vs. `0.7745`，`AUROC = 0.9597` vs. `0.9903`。
  * **严格双重留出层（`0%` 外部训练重叠，`n = 22` 种独立疾病）**：本项目在 **`AUPRC`（`0.4542` vs. `0.0509`，`+0.4033 [+0.2232, +0.5874]`，`8.93×`）与 `Recall@50`（`0.6818` vs. `0.4545`，`1.50×`）上双双大幅胜出**！
* **目标一（Goal 1）——全管线疾病重定位与分层蒙眼冷启动靶点快筛**：
  * **蒙眼冷启动靶点留出（`n = 124` 次零训练适应症评估，覆盖 `16,992` 种可靶向疾病，强制 `deg = 0`）**：总体 **`Hits@20 = 77.42%`**（中位排名 `5.0`），包含 `n = 95` 同靶点机制先例层 **`90.53%` `Hits@20`（中位排名 `3.0`）** 与 `n = 29` 严格全新靶点–适应症层 **`34.48%` `Hits@20`（随机基线的 `293.0×`，`Hits@50 = 48.28%`，中位排名 `51.0`）**（纯 CPU 单机密靶点签名耗时仅 `0.96 秒`）。
  * **同种子无泄露已见药物 OOF 排序（`392` 药/种子 × `18,025` 疾病）**：**`Hits@10 = 73.45%`**，**`Hits@30 = 85.11%`**，**最佳中位排名 `3.0`**。
  * **去重并经临床试验审计的管线输出（`2,350` 行 = `47 × 50`）**：`FDR <= 10%` 下产出 **`12` 条洁净去重推荐（覆盖 `5 / 47` 款资产）**（`FDR <= 5%` 下 `2` 条，`median_best_q = 0.5396`；`47` 条前 5 推荐标注 `Tested in Phase 2/3 — Not Approved`；`0` 例本体子代/伞状词或 `OnSIDES` 毒性泄露）。
* **目标二（Goal 2）——`OptimusCombo-Net` 联合给药可行性与专利/BD 优选清单（`53,952` 组临床三元组，`1,059` 种疾病）**：
  * **严格无规则 NCT 临床试验 5 折 MONDO 分组 OOF（`15,712` 组 NCT 联用 vs 头对头/拮抗试验；全部 `8` 个规则重叠特征归零；`0` 置换误差）**：**`0.9714` AUROC / `0.9944` AUPRC** *（基线 `0.8762`）*
  * **全部 `53,952` 组三元组无规则 5 折 MONDO 分组盲测**：**`0.9327` AUROC / `0.8849` AUPRC** *（全特征 MONDO 分组 `0.9499`）*
  * **安进药物冷启动 5 折（`3,550` 组严格留出安进三元组）**：**`0.9343` AUROC / `0.8949` AUPRC** | 精确置换对称误差 `5.96e-08` | 导出 **`5,760` 条 Tier 1**（`安进 × 安进`）、**`5,760` 条 Tier 2**（`安进 × 专利到期仿制药/生物类似药`）与 **`5,760` 条 Tier 3**（外部临床基准）方案。
* **【权威封版】AMG 757 / 塔拉妥单抗（`IMDELLTRA®`）`v4.3.2` 审校证据驱动模型（`results_v4/` & `docs/zh/16`）**：
  * **表 A 已承诺 SCLC 临床基线（`12` 项在研承诺拓展/生命周期研究 + `7` 项历史完成基线研究 = `19` 项注册表研究）**：`EXP-S1`（`DeLLphi-305` OS HR `0.42 [0.32–0.54]` + `DeLLphi-312`）+ `EXP-S2`（`NCT07531095` `+ZL-1310`、`NCT06898957` `+YL201`、`DeLLphi-310 SC` 在研管线）+ `EXP-S3`（`DeLLphi-306` + `PrECOG NCT07637513`）= **`$2,610.8M`（`2036` 法定下限）/ `$3,306.6M`（`2039` 专利情景）**。
  * **表 B 增量拓展与双轨罕见非 SCLC 期权（财务审校前情景测算，共享 `2036-05-16` 母体独占期下限、扣除 `-$469.3M` `2L` 蚕食、`FD&C §529 PRV = $0`）**：`#1 EXP-S4` 活动性脑转移（`NCT07402343`，`N = 35`，`33.0%†` 全身参考 ORR，`+$59.7M`；`TRIG-06` 三区间临床门控 `>=12/35` 拓展、`8..11/35` 亚组复核 `P = 36.0%`［`-$21.71M` vs 二元放弃 `-$34.01M`］、`<8/35` 终止）、`#2 EXP-L1` 肺 `LCNEC`（`IFCT NCT06937905`，`0` 项塔拉妥单抗直接数据，`a0 = 0.50` 主后验 `44.5%` vs 去 Obrixtamig `29.9%`，`V0 +$29.6M` / `V1 +$27.1M` / `V2 +$34.3M`；胸部 NE `EXP-L1 + EXP-L2` 合计 `V0 +$33.5M` / `V1 +$38.0M` / `V2 +$47.5M`）、`#3 EXP-L2` `t-SCLC`（`V2 +$13.2M`）、`#4 EXP-A` `MCC`（`V2 +$12.6M`）；罕见非 SCLC 独立建组总和为 `-$34.6M`（`V0`），在 **路线 `V1`（统筹 `11` 项开放外部 IIT 联盟，`N = 517`）** 下基准（`0.90`）为 **`+$1.8M`**（`0.75` 供药折损敏感性为 **`-$4.3M`**，非 SCLC 信息期权价值 **`EVSI = +$12.94M`**［精确 `2036` 独占期截断为 `$12.35M`］），在 **路线 `V2`（新建安进申办注册主篮子；独立 Simon `$20.96M` `<= 4.99%` 一类错误 vs `EXNEX<=10%` `$17.24M` `<= 8.25%` 一类错误）** 下升至 **`+$10.6M`**。
  * **严格防泄露验证与密码学治理**：`Q1` `LOSO` `100%` 覆盖（`Winkler95 = 0.615`）；`Q2` `LOCO` `100%` 覆盖（`Winkler95 = 0.510`）；封存 `PE1` `32` 抗原回测 `P5 = 0.378`（`MLPClassifier((16, 8), activation='tanh', solver='lbfgs', alpha=8.0, tol=0.001, max_iter=300)`）vs 静态 `M5 = 0.501`（`+0.123` 截断后度数虚高，`wp25_passed = False` -> `Q5` 维持退役）；无偏 `Q4` `recall@50 = 0.000`；`41` 条追加式封存记录（`5` 项 `q2_empirical_model` + `4` 项 `analyst_prior` 严格分离计分）全部 `3/3` 份 `.ots` 经比特币区块 `#975,929`、`#975,963`、`#976,004` 与 `#976,009` PoW 核验封存于 `forecasts/`。
* **【权威封版】AMG 691 `v2.0`（抗 `CD30/CD30L`，1 期 `NCT06637371` & `NCT07745907`，`docs/zh/14–15`）**：
  * 原代人类过敏性哮喘气道单细胞（`GSE193816`，`52,152` 细胞）证实 **`0.29%` 上皮重叠**、**`15.69×` `CD30L/OX40L` 富集**与 **`94.99%` `FOXP3+ Treg` 豁免**；`GSE157194` 证实 `Dupilumab` 治疗 3 个月后皮损 `CD30L`（`105.6%`）持续存在；非循环 `14` 锚点 2025–2026 临床基准盲测 **`AUROC = 1.0000`**；领跑全资 2a 期联用：**哮喘/鼻息肉 `+ Tezepelumab`（`0.7650 / 0.7885`）**、**UC/CD `+ Wezlana`（`0.8329 / 0.7852`）**、**AD `+ Rocatinlimab`（`0.7432`）**。
* **历史基准 AMG 757 `Option-1 v2.0/v3.2` 与 `Option-2 OptimusBiTE-Head`（`v1.3 g4_max`，管线投资决策已被 `v4.3.2` 取代）**：
  * **`Option-2 g4_max`（`24` 检查点 + `ESM-2 3B` ECD，`10 × 5` 同源家族分组靶点冷启动 CV）**：方向 A `MRR = 0.1187 -> 0.3595`（`AUROC = 0.9851`），方向 B（`2,381` 表面组）`MRR = 0.0120 -> 0.2454`，留出 `TCE` `MRR = 0.2474 -> 0.6536`（`AUROC = 0.9938`），`DLL3 × TCE` 在 SCLC 排名 `#1,767（冻结）-> #16（DLL3 冷启动 OOF）-> #1（部署）`。在 `v4.3.2` 附录 B `Ion Guard` 安全护栏下，中枢/心肌电压门控离子通道（`KCNB2`、`SCN3A`）降级（`F_ion_guard = 0.15`），**`DLL3 + IGSF9`（`0.4745`，`73.3%` Bulk / `14.9%` scRNA 挽救率，正常脑最高 `0.49 TPM`，`1.18%` TIL）** 成为赛道 3 第 `#1` 洁净下一代双抗候选（领先 `DLL3 + PTK7` [`0.2202`] 与 `DLL3 + MUC4-beta` [`0.2192`]）。

### 受控架构与参数规模扩展阶梯表（`clinical_v4`，`full_graph` 种子 `[42, 1, 2]`，`207` 种共享疾病）

| 模型家族（每个切分内部 `8` 或 `2` 个同种子检查点） | 单模型 GNN 参数量 | 共享药物 AUPRC（`207` 疾病） | 对比 Drive TxGNN 配对差值 $\Delta$（`95%` CI） | 自有测试集 `3,353` 活跃药 / `18,640` 全药物 AUPRC |
| :--- | :---: | :---: | :---: | :---: |
| **外部基准：Google Drive TxGNN（`PrimeKG`，`d = 512`，`94.5%` 位于训练集）** | `26.3M` | `0.2156`（`0%` 重叠层 `0.0509`） | — | — |
| **OptimusKG-TxGNN（`d = 1024`，`u = 0.2`，`s42` 对照）** | `105.0M`（`4.0×`） | `0.3010` | `+0.0415`（`s42`） | `0.2597` / — |
| **`v4 wmatch` 等宽家族（`d = 2048`，`6` 检查点同种子均值）** | `419.7M`（`16.0×`） | `0.4022` | `+0.1866 [+0.1205, +0.2512]` | `0.3122` / `0.2143` |
| **`v5bi` 双向去偏家族（`d = 2048`，`6` 检查点同种子均值）** | `419.7M`（`16.0×`） | `0.3919` | `+0.1763 [+0.1095, +0.2414]` | `0.2990` / `0.2003` |
| **`v5bi` 超宽旗舰家族（`d = 3072`，`6` 检查点同种子均值）** | `944.2M`（`35.9×`） | `0.3948` | `+0.1792 [+0.1112, +0.2454]` | `0.3266` / `0.2286` |
| **`v5fm` MedCPT + 化学多模态家族（`d = 2048`，`6` 检查点同种子均值）** | `441.8M`（`16.8×`） | `0.4275` | `+0.2119 [+0.1445, +0.2773]` | `0.3145` / `0.2343` |
| **生产级 `24` 模型跨家族集成（每切分 `8` 个同种子检查点）** | `419.7M–944.2M` | **`0.4380`**（`0%` 重叠层 **`0.4542`**） | **`+0.2224 [+0.1545, +0.2880]`（`2.03×`；`0%` 重叠层 `8.93×`）** | **`0.3379`（`s1: 0.3603`）/ `0.2460`** |

零样本未见疾病领域留出基准（`complex_disease`，`5` 种子 `[42, 1, 2, 3, 4]`，`d = 1024`）：Indication AUPRC 达 **`0.1403`（`5` 种子 `u=0.5+deg` 均值）至 `0.1678`（`s42 fix_dst`）**；一键运行的 **`d = 2048` 零样本 + 有符号 `clinical_v5`（`59` 关系）配对消融队列**已封装于 [`run_travel_priority2b_and_4_ablations.py`](file:///home/nchen10/antigravity_prjs/kg/run_travel_priority2b_and_4_ablations.py)（**Colab Cell `4D`**）。

---

## 代码仓库目录结构

```text
/home/nchen10/antigravity_prjs/kg/
├── Makefile                                  # 100% 离线 v4.3.2 审计、测试、季度试验监控、预测计分、OTS 区块升级与全新克隆复现入口
├── optimus_txgnn/                            # 核心算法库（双后端 dgl/torch 引擎、HeteroRGCN、贝叶斯 Q1–Q6、AMG 691 v2）
│   ├── dgl_compat.py                         # 原生 DGL + 纯 PyTorch TorchHeteroGraph（~256 MB 分块聚合引擎）
│   ├── data.py                               # OptimusTxData 数据加载器、三向切分防漏守卫与 SHA-256 指纹校验
│   ├── clinical_v5_builder.py                # ~4 秒构建切分锁定的 59 关系有符号图 clinical_v5
│   ├── model.py                              # O(N d^2) HeteroRGCN、门控 MedCPT 适配器与稀疏 CSR 疾病原型
│   ├── trainer.py                            # 三阶段 OptimusTxGNN 训练器、Full_Graph_NegSampler 与混合精度/断点续训
│   ├── evaluator.py                          # 向量化疾病中心评估器与配对自助法比较器
│   ├── graphmask.py                          # 两层 SoftConcrete + 拉格朗日稀疏约束 GraphMask 解释器
│   ├── explain.py                            # 精确两层积分梯度（IG）与多跳因果路径排序器
│   ├── ip_registry.py                        # FDA 橙皮书/紫皮书专利分类器与安进管线 YAML 加载器
│   ├── ontology_and_safety_filters.py        # MONDO 47,892 边 DAG 闭包 + OnSIDES v3.1.1 / MedDRA 靶点毒性屏蔽
│   ├── calibration.py                        # 逐疾病 z-score、分层 BH FDR、冷启动投影与 AmgenRepurposeRanker
│   ├── combo_model.py                        # 三头置换对称 OptimusCombo-Net、MONDO 分组 CV 与强负样本压力测试
│   ├── models/                               # 权威 AMG 757 v4.3.2 Q1–Q4/Q6 模型、表 A/B 映射器、决策总表与 EVSI 触发器
│   ├── backtest/                             # 严格截断前 DLL3 时序回测与 32 抗原 PE1 新适应症发现回测引擎
│   ├── basket_design.py                      # 精确 Simon 两阶段、EXNEX<=10% 与外部 IIT 联盟篮式试验操作特征引擎
│   ├── regulatory_rules.py                   # 法定 PHS §351(k)(7)、FD&C §529 PRV ($0) 与 BPCA §351(m) 儿科独占期规则
│   ├── amg691_v2_combination_engine.py       # 权威 AMG 691 v2.0 CD30/CD30L 单细胞/活检校准、联用与双抗引擎
│   ├── bite_biomarker_and_expansion.py       # 实体瘤 BiTE CD3 去混杂、胚系失活突变屏蔽与未加权保形校准钩子
│   ├── option1_protocol_and_omics.py         # 历史 Option-1 分子门控、五支柱 MCDA、Dirichlet 敏感性与精确 Simon 计算器
│   ├── option2_bite_head.py                  # Option-2 OptimusBiTE-Head（SVD-LoRA + Modality-FiLM，protocol=2 weights_only=True）
│   ├── portable_pack.py                      # 跨 Python 版本（3.8 <-> 3.13）纯张量推理包序列化器（protocol=2）
│   └── studio_ui_*.py                        # 模块化 15 视图转化发现工作台 HTML/CSS/D3/Canvas 前端模板
├── scripts/                                  # 确定性 v4.3.2 构建脚本、双语报告/工作台生成器、预测记分器与外发包审计器
│   ├── build_evidence_store_v1.py            # 构建并校验 260 条主源核验溯源库 + 120 项 DLL3 试验快照
│   ├── verify_evidence_sources.py            # 离线 SHA-256 与 PubMed/CT.gov 原文数值逐字核验器
│   ├── build_amg757_v4_reports_and_studio.py # 单一真值源生成 docs/16*、docs/zh/16* 与双语 V4 Evidence Studio HTML
│   ├── score_prospective_forecasts.py        # 离线比特币 PoW (.ots) 核验器、季度 CT.gov 监控与 Q2/分析员分离记分卡引擎
│   └── audit_share_bundle.py                 # 边界安全扫描器，拦截外发包中任何 v3/v4.2 历史残留词条
├── configs/                                  # 管线资产、法规/rNPV、分子门控与生物标志物 YAML 配置表
├── forecasts/                                # 追加式 SHA-256 + 3/3 比特币区块 (#975,929/#975,963/#976,004/#976,009) 41 条密封预测账本
├── results_v4/                               # Git 跟踪的 AMG 757 v4.3.2 CSV/JSON/Parquet 产物、溯源快照、工作台与 PPTX
├── build_amg757_biostat_pptx.py              # 零硬编码数值的 18 页中英双语生物统计 PPTX 生成器
├── build_optimuskg_amg757_amg691_presentation.py # 单一真值源生成 16 页中英双语 OptimusKG + AMG 757 v4.3.2 + AMG 691 v2.0 演示文稿
├── generate_metrics_ledger.py                # 机器校验生成 METRICS.md（第 1–7 节）
├── docs/                                     # 16 卷英文专著/报告 + docs/zh/（10 份简体中文配套文档）+ 配图
└── tests/                                    # 23 套 pytest 测试套件（v4.3.2 100% 全新克隆离线通过 + 双后端兼容）
```

---

## 快速启动与验证命令

### A. AMG 757 `v4.3.2` 审校证据模型、比特币 PoW 预测账本、外发包审计与维护模式（`Makefile`）

由于全部 `v4.3.2` 分析产物、离线 PubMed / ClinicalTrials.gov（`120` 项 `DLL3` 试验）/ 泛抗原溯源快照、中英双语 18 页 `.pptx` 幻灯片、`EVSI` 决策触发器以及比特币 PoW OpenTimestamps 追加式前瞻预测账本（跨 `2026_11`、`2026_11b`、`2026_11c` 共 `41` 条密封记录，**`3/3` 全部落锤结算于比特币区块 `#975,929`、`#975,963`、`#976,004` 与 `#976,009`**）均已纳入 Git 版本控制（`results_v4/` 与 `forecasts/`），完整的 `v4.3.2` 验证与维护套件可 **`100%` 离线运行**：

```bash
# 1. 快速 (<30 秒) 离线核验 260 条溯源记录、41 条比特币 PoW 账本记录、外发包洁净度及全部 v4.3.2 测试套件
make verify-fast

# 2. 扫描外发共享包 (doc_to_gdrive/、visualization_studio/、PPTX、DOCX、HTML、MD) 确保零历史 v3/v4.2 残留漂移
make bundle-audit

# 3. 全仓库完整单元测试验证 (对全部 42 个 .pt 检查点强制执行 weights_only=True)
make verify

# 4. 将 HEAD 克隆至隔离 /tmp 目录 (不含 gitignore 的 data_optimuskg/ 与 amgen_discovery_studio/) 验证全新克隆零误差复现
make verify-fresh-clone

# 5. 四级维护模式 SOP (季度 CT.gov 监控 [含 UCLA NCT06788938 隔离追踪]、读出事件驱动计分、OTS 比特币区块升级、仅当新 TCE 数据披露时年度重拟合)
make watch-trials
make score-forecasts
make ots-upgrade
```

#### 全新克隆（Clean-Clone）自动跳过的历史测试说明（`13` 项依赖本地 Gitignore 大体积 `v3` / `AMG 691` / 原始图数据包的历史测试）

所有 AMG 757 `v4.3.2` 核心测试在全新克隆环境下均无条件运行且 **`0` 跳过**（涵盖 `test_evidence_store.py`、`test_registry_and_anchors.py`、`test_q1_q2_bayesian_models.py`、`test_q3_q4_q6_models.py`、`test_basket_design.py`、`test_backtest_no_future_leak.py`、`test_prospective_forecasts.py`、`test_doc16_zero_drift.py`、`test_torch_weights_only_hygiene.py`、`test_regulatory_rules.py`、`test_pillar_attribution.py` 与 `test_option2_debiased_v2.py`）。在未挂载本地大体积 `data_optimuskg/` 与 `amgen_discovery_studio/` 目录的纯净克隆中，恰有 **`13` 项历史 `v3` / `AMG 691` / 原始图测试**按设计优雅跳过：

| # | 测试文件与函数名 | 所需本地 Gitignore 大体积数据包 |
| :-: | :--- | :--- |
| 1 | `tests/test_amg691_v2_combination_discovery.py::test_amg691_phase1_metadata_and_studio_hygiene` | `data_optimuskg/amg691_v2/`, `amgen_discovery_studio/results/amg691_v2/` |
| 2 | `tests/test_amg691_v2_combination_discovery.py::test_amg691_24ckpt_subbank_and_empirical_bundle_invariants` | `data_optimuskg/amg691_v2/`, `amgen_discovery_studio/results/amg691_v2/` |
| 3 | `tests/test_amg691_v2_combination_discovery.py::test_amg691_clinical_pharmacology_guardrails_and_top_rankings` | `data_optimuskg/amg691_v2/`, `amgen_discovery_studio/results/amg691_v2/` |
| 4 | `tests/test_amg691_v2_combination_discovery.py::test_amg691_non_circular_2026_clinical_calibration_and_offline_cache` | `data_optimuskg/amg691_v2/`, `amgen_discovery_studio/results/amg691_v2/` |
| 5 | `tests/test_amg691_v2_combination_discovery.py::test_amg691_bilingual_reports_figures_and_html_studio` | `data_optimuskg/amg691_v2/`, `amgen_discovery_studio/results/amg691_v2/` |
| 6 | `tests/test_amg757_integrated_v3_consistency.py::test_markdown_reports_have_zero_broken_file_or_figure_links` | `amgen_discovery_studio/results/amg757_v3_master_indication_ranking.parquet` |
| 7 | `tests/test_amg757_integrated_v3_consistency.py::test_v32_master_ranking_deduplication_neuroblastoma_and_combo_hygiene` | `amgen_discovery_studio/results/amg757_v3_master_indication_ranking.parquet` |
| 8 | `tests/test_amg757_integrated_v3_consistency.py::test_v32_five_upgrades_end_to_end_regression` | `amgen_discovery_studio/results/amg757_v3_master_indication_ranking.parquet` |
| 9 | `tests/test_amg757_integrated_v3_consistency.py::test_option2_full_2944_disease_exports_and_clinical_protocol_guardrails` | `amgen_discovery_studio/option2_bite_head/g4_max/` |
| 10 | `tests/test_bite_and_amg757_expansion.py::test_within_platform_omics_and_faers_ror_rigor` | `amgen_discovery_studio/results/amg757_empirical_omics_and_safety_v2.json` |
| 11 | `tests/test_bite_and_amg757_expansion.py::test_tarlatamab_and_xaluritamig_pipeline_hygiene_and_deduplication` | `amgen_discovery_studio/results/portfolio_repurposing_50_assets_top50.parquet` |
| 12 | `tests/test_bite_and_amg757_expansion.py::test_inductive_bite_scorer_and_disease_to_drug_engine` | `data_optimuskg/amgen_v5/pack_tensors_fp16.pt` |
| 13 | `tests/test_data_pipeline.py::test_clinical_v4_acceptance` | `data_optimuskg/clinical_v4/` |

### B. 安进 Goal 1 + Goal 2 全管线、16 页全平台双语演示文稿与 `:8004` 实时转化工作台

```bash
# 1. 运行同种子无泄露 47 款资产 Goal 1 + Goal 2 管线 + 蒙眼靶点留出 + 4 家族 CPU 推理包导出
.venv/bin/python run_amgen_goal1_and_goal2_pipeline.py
.venv/bin/python evaluate_24model_ensemble_vs_drive.py

# 2. 构建图谱浏览器数据包、15 视图工作台、16 页双语平台幻灯片并启动 :8004 服务
.venv/bin/python build_amgen_explorer_bundle.py
.venv/bin/python build_amgen_studio_and_decks.py
.venv-v4/bin/python build_optimuskg_amg757_amg691_presentation.py
.venv/bin/python serve_amgen_studio.py --port 8004

# 3. 无需 GPU，0.96 秒内对任意机密 AMG-XXXX 靶点签名完成纯 CPU 全疾病快筛
.venv/bin/python score_new_amgen_asset.py \
    --name "AMG-NEXT-DUAL" \
    --targets "TSLP:inhibits,IL13:inhibits" \
    --out_json ./amgen_discovery_studio/results/sample_post_ultra_query.json
```

### C. Colab GPU 差旅自动队列（Priority 2b 零样本疾病 + Priority 4 有符号 `clinical_v5` 配对消融）

```bash
# 本地打包并冒烟测试 colab_upload/ (含 OptimusKG_TxGNN_G4_96GB_Colab.ipynb 中自包含的 Cell 4D)
.venv/bin/python package_for_colab.py

# 或直接在任意 GPU 节点通过命令行运行 (~4 秒自动构建切分锁定的 clinical_v5 并同步报告)：
python run_travel_priority2b_and_4_ablations.py \
    --enable_tf32 \
    --local_runs_root /content/work/optimuskg_txgnn/runs \
    --drive_runs_root /content/drive/MyDrive/optimuskg_txgnn/runs \
    --drive_folder /content/drive/MyDrive/optimuskg_txgnn
```

### D. 历史基准 AMG 757 Option-1 离线复现与 Option-2 `OptimusBiTE-Head`（`protocol=2 weights_only=True`）

```bash
# 1. 确定性离线重建 Option-1 多组学数据包与 MCDA 历史基线
.venv/bin/python build_amg757_option1_empirical_data.py --offline

# 2. Option-2 OptimusBiTE-Head：提取/增强 24 检查点特征库、本地验证并打包 Google Colab G4 归档
.venv/bin/python build_option2_bite_head_bank.py --augment_features_only
.venv/bin/python train_option2_bite_head.py --profile quick --out_dir ./amgen_discovery_studio/option2_bite_head
.venv/bin/python package_option2_colab.py
```

### E. 核心命令行工具与双后端测试套件

```bash
# 在 Python 3.12 (.venv-v4) 下跨原生 DGL 与纯 PyTorch 后端验证全部 pytest 测试套件
.venv-v4/bin/pytest -q
OPTIMUS_GRAPH_BACKEND=torch OPTIMUS_DISABLE_NATIVE_DGL=1 .venv-v4/bin/pytest -q
```
