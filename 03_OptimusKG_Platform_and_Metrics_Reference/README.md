# OptimusKG-TxGNN (`clinical_v4`, `clinical_v5`, Amgen `v5`, `AMG 757 v4.3.2` & `AMG 691 v2.0` Discovery Suite)

> **Simplified Chinese Overview**: [docs/zh/README_ZH.md](file:///home/nchen10/antigravity_prjs/kg/docs/zh/README_ZH.md) | **Canonical Metrics Ledger**: [METRICS.md](file:///home/nchen10/antigravity_prjs/kg/METRICS.md) ([中文](file:///home/nchen10/antigravity_prjs/kg/docs/zh/METRICS_ZH.md)) | **Cross-Laptop Project Handoff**: [ANTIGRAVITY_PROJECT_HANDOFF.md](file:///home/nchen10/antigravity_prjs/kg/ANTIGRAVITY_PROJECT_HANDOFF.md) ([中文](file:///home/nchen10/antigravity_prjs/kg/docs/zh/ANTIGRAVITY_PROJECT_HANDOFF_ZH.md))

Scalable, leakage-guarded biomedical Knowledge Graph Foundation Model (`105.0M` to `944.2M` GNN weights per model; `24`-checkpoint multi-family ensemble; `42 / 42` `.pt` checkpoints verified under strict `torch.load(..., weights_only=True)`) built on **OptimusKG v3.2 (`clinical_v4` / `clinical_v5` / `amgen_v5`)** (`139,439` connected clinical nodes, `9,385,536` bidirectional edges across `R = 50` unsigned or `R = 59` signed/typed canonical relations) powering six translational and biostatistical workstreams:

1. **Goal 1 — Amgen Portfolio Disease Repurposing (`47` Commercial, Biosimilar, Phase 1–3 & Preclinical Assets $\times$ `18,025` Clinical Diseases)**: Per-disease hub-bias removal, leak-free within-seed `AmgenRepurposeRanker`, MONDO DAG (`47,892` edges) + `OnSIDES v3.1.1` (`4.72M` pairs) ontology/toxicity masking, within-stratum Mondrian conformal `FDR` calibration, and signed `d = 3072` `GraphMask` mechanism chains.
2. **Goal 2 — Pan-Disease Co-Administration Regimen & IP/BD Partnering (`OptimusCombo-Net`)**: Exact permutation-invariant 3-head combination feasibility engine trained on `53,952` clinical triples across `1,059` diseases under MONDO-root-blocked CV, prioritizing **Tier 1 (`Amgen × Amgen`)** and **Tier 2 (`Amgen × FDA Off-Patent Generic / Biosimilar`)** regimens.
3. **Interactive Client Delivery & `< 1s` (`0.96s`) Confidential CPU Target Triage**: Live **15-View Amgen Translational Discovery Studio & Self-Explaining KG Explorer (`:8004`)**, bilingual editable `.pptx` + zero-server `.html` presentation suites, offline Franchise Client Decks, and a cross-Python `post_ultra_inference_pack/` for scoring confidential `AMG-XXXX` targets in **`0.96s` on CPU** with zero GPU required.
4. **Canonical IMDELLTRA® (`AMG 757` / Tarlatamab) `v4.3.2` Audited Evidence-Backed Indication Expansion & Dual-Route Trial Model (`docs/16` & `results_v4/`)**: Built on `260` source-verified provenance records and an enriched `120`-trial `DLL3` `ClinicalTrials.gov` snapshot, physically segregating **Table A Committed SCLC Baseline** (`12` active/committed SCLC expansion/lifecycle studies + `7` historical/completed baseline studies; `EXP-S1 + S2 + S3 = $2,610.8M` [`2036` floor] / `$3,306.6M` [`2039` patent]) from **Table B Incremental Expansion & Dual-Route Rare Non-SCLC Options** (**Route `V1` External IIT Signal Federation** across `11` open non-SCLC IITs [`N = 517`; `+$1.8M` at `0.90` base vs `-$4.3M` at `0.75` drug-supply attrition sensitivity; Bayesian `EVSI = +$12.94M` (`$12.35M` exact `2036` LOE)] vs **Route `V2` De Novo Amgen Registrational Master Basket** [`+$10.6M`] vs `V0` standalone [`-$34.6M`]); unifies `Q1` 4-chain NUTS multinomial IHC (`100%` LOSO coverage), `Q2` `15`-cohort TCE `EXNEX` (`a0 = 0.50` cross-agent power prior; `44.5%` Pulmonary `LCNEC` primary posterior with `0` direct tarlatamab trials vs `29.9%` drop-Obrixtamig), strict pre-`T` `PE1` 32-antigen discovery back-test (`P5 = 0.378` vs `M5 = 0.501`, `wp25_passed = False` -> `Q5` retired), unbiased `Q4` check (`recall@50 = 0.000`), Appendix B `Q6` `Ion Guard`, `WP9` multi-regime (`A1`–`A3`) basket trade-offs, 4-cadence Maintenance Mode SOP, and a `41`-row `3/3` settled Bitcoin-PoW OpenTimestamps append-only forecast ledger (`#975,929`, `#975,963`, `#976,004` & `#976,009`) strictly separating `5` `q2_empirical_model` forecasts from `4` `analyst_prior` forecasts.
5. **IMDELLTRA® (`AMG 757`) Historical `Option-1 v2.0/v3.2` Primary-Biopsy/scRNA Atlas & `Option-2 OptimusBiTE-Head` (`v1.3 g4_max`, `docs/11–13`)**: Preserves the `2,215`-profile primary human tumor biopsy atlas (`MCC GSE235092`, `MTC`, `TARGET-NBL`), `60,026` clinical human SCLC biopsy single cells (`GSE281740 + GSE324849`), and `SVD-LoRA` + `Modality-FiLM` + `ESM-2 3B` surfaceome head (`2,418` human surface proteins with `<= 14 nm` synapse cleft & `BACE1` sheddase biophysics; under `v4.3.2` Appendix B `Ion Guard`, `KCNB2`/`SCN3A` are demoted for neuronal/cardiac risk, elevating **`DLL3 + IGSF9`** [`0.4745`] to `#1` clean Track 3 candidate).
6. **Canonical AMG 691 `v2.0` (`CD30` [`TNFRSF8`] / `CD30L` [`TNFSF8`], Phase 1 `NCT06637371` & `NCT07745907`) Combination Therapy & Bispecific Discovery (`docs/14–15`)**: Evaluates `2,790` `(Indication, Partner)` triples across `24` checkpoints, primary human allergic-asthma airway single cells (`GSE193816`, `52,152` cells proving `0.29%` epithelial overlap, `15.69×` `CD30L/OX40L`, and `94.99%` `FOXP3+ Treg` sparing), and `10` treatment-refractory human biopsy cohorts (`n = 1,642`, including `105.6%` residual `CD30L` on `Dupilumab`), achieving **`AUROC = 1.0000`** on a non-circular `14`-anchor 2025–2026 human clinical benchmark.

---

## Documentation Suite

| Document | Focus |
| :--- | :--- |
| **[METRICS.md](file:///home/nchen10/antigravity_prjs/kg/METRICS.md)** ([中文](file:///home/nchen10/antigravity_prjs/kg/docs/zh/METRICS_ZH.md)) | **Canonical Machine-Verified Metrics Ledger**: Auto-generated by [`generate_metrics_ledger.py`](file:///home/nchen10/antigravity_prjs/kg/generate_metrics_ledger.py) directly from live JSON/Parquet outputs (`Sections 1–7`: 3-Tier PrimeKG table, Drive TxGNN benchmark + `94.5%` overlap proof, cold-target holdout, rule-free NCT combo OOF, `AMG 757 v4.3.2`, and `AMG 691 v2.0`) |
| **[ANTIGRAVITY_PROJECT_HANDOFF.md](file:///home/nchen10/antigravity_prjs/kg/ANTIGRAVITY_PROJECT_HANDOFF.md)** ([中文](file:///home/nchen10/antigravity_prjs/kg/docs/zh/ANTIGRAVITY_PROJECT_HANDOFF_ZH.md)) | **Full Project & Cross-Laptop Handoff Guide**: Architecture summary, `Nov 15, 2026` `v4.3.2` / `v2.0` status, Google Drive file IDs, `doc_to_gdrive/` share bundle layout, and offline `Makefile` / Colab workflows |
| **[forecasts/README.md](file:///home/nchen10/antigravity_prjs/kg/forecasts/README.md)** ([中文](file:///home/nchen10/antigravity_prjs/kg/forecasts/README_ZH.md)) | **Append-Only Bitcoin-PoW Forecast Ledger (`v4.3.2`)**: `41` sealed JSONL rows across `2026_11`, `2026_11b`, and `2026_11c`, `3/3` settled Bitcoin block headers `#975,929`, `#975,963`, `#976,004` & `#976,009`, `11` voided amendments, `FREEZE-P5`, `TRIG-01..09`, and separated `5` `Q2` vs `4` `analyst_prior` scorecards |
| **[NEXT_STEPS_TODO_AND_TRAVEL_GUIDE.md](file:///home/nchen10/antigravity_prjs/kg/NEXT_STEPS_TODO_AND_TRAVEL_GUIDE.md)** | **Step-by-Step Travel Guide & Prioritized Next-Steps Plan**: Pre-presentation review actions (`A1`–`A7`), 1-click laptop Colab `Cell 4D` guide (`B1`–`B3`), post-return ablations (`C1`–`C6`), and Amgen-wide partnerships (`D1`–`D5`) |
| **[01_DESIGN_AND_IMPLEMENTATION.md](file:///home/nchen10/antigravity_prjs/kg/docs/01_DESIGN_AND_IMPLEMENTATION.md)** | System architecture diagrams, `clinical_v4` KG curation & 8 foundational bug fixes, `O(N d^2)` relation-mean-first `HeteroRGCN` + pure-PyTorch `TorchHeteroGraph` engine, All-3-Stage `8.45M`-edge attribution math, and verified `d = 1024` / `d = 2048` scaling benchmarks |
| **[02_TRAINING_AND_USER_MANUAL.md](file:///home/nchen10/antigravity_prjs/kg/docs/02_TRAINING_AND_USER_MANUAL.md)** | Step-by-step operational manual: dual-backend (`dgl` / `torch`) setup, `clinical_v4` & split-locked `clinical_v5` (`59` relations) curation, local `24 GB` GPU recipes, Google Colab `G4 96 GB` scaling + **1-click Cell `4D` travel ablations**, symmetric bootstrap evaluation, and exports |
| **[03_DOWNSTREAM_APPLICATIONS.md](file:///home/nchen10/antigravity_prjs/kg/docs/03_DOWNSTREAM_APPLICATIONS.md)** | Engineering & translational results for the two earlier downstream benchmarks in `/home/nchen10/cursor_prjs/txgnn/optimuskg_d2048/`: **(1) `34`-Drug Portfolio Repurposing (`:8002`)** and **(2) `CD30 / AMG691` Combination Discovery (`:8003`)** |
| **[04_RESEARCH_AND_NEXT_STEPS_ROADMAP.md](file:///home/nchen10/antigravity_prjs/kg/docs/04_RESEARCH_AND_NEXT_STEPS_ROADMAP.md)** | Architectural bottleneck diagnosis and 2024–2026 literature synthesis (Mondrian Conformal Calibration, `OnSIDES v3.1.1`, `d = 3072` scaling, `clinical_v5` signed pharmacology, biomarker conditioning, and `whyStopped` trial calibration) |
| **[05_AMGEN_REPURPOSING_AND_COMBO_MASTER_PLAN.md](file:///home/nchen10/antigravity_prjs/kg/docs/05_AMGEN_REPURPOSING_AND_COMBO_MASTER_PLAN.md)** | Original blueprint for **Goal 1 (`47` Amgen Drugs -> `18,025` Diseases)**, **Goal 2 (`Amgen × Amgen` & `Amgen × Off-Patent` Combinations)**, and **Google Colab `G4 96 GB` Compute Strategy** |
| **[06_AMGEN_DISCOVERY_STUDIO_DESIGN_AND_FEATURE_GUIDE.md](file:///home/nchen10/antigravity_prjs/kg/docs/06_AMGEN_DISCOVERY_STUDIO_DESIGN_AND_FEATURE_GUIDE.md)** | Complete mathematical reference (`GraphMask` self-explaining edges, `2–10` hop pathfinding, `< 20 ms` `Disease -> Drug` projection) and **tab-by-tab manual for all 15 interactive views** in the Studio & offline Client Decks |
| **[07_END_TO_END_ARCHITECTURE_AND_DATA_ENGINEERING.md](file:///home/nchen10/antigravity_prjs/kg/docs/07_END_TO_END_ARCHITECTURE_AND_DATA_ENGINEERING.md)** | **Post-Audit Monograph (Part I)**: 5-tier system design, `OptimusKG v3.2` -> `clinical_v4` & split-locked `clinical_v5` (`59` relations), MONDO DAG + `OnSIDES v3.1.1` hygiene, FDA Orange/Purple Book IP tiers, `53,952`-triple `combo_v1`, and `768-D` `NCBI MedCPT` feature bank |
| **[08_MODEL_TRAINING_CAMPAIGNS_AND_BENCHMARKS.md](file:///home/nchen10/antigravity_prjs/kg/docs/08_MODEL_TRAINING_CAMPAIGNS_AND_BENCHMARKS.md)** | **Post-Audit Monograph (Part II)**: `O(N d^2)` `HeteroRGCN` + `TorchHeteroGraph` engine, `v5bi` & `v5fm` adapters, complete registry of **all `30` trained models**, and `24`-model within-seed paired bootstrap benchmarks (`0.4380` vs `0.2156` AUPRC) |
| **[09_RANKING_RECOMMENDATION_AND_VISUALIZATION_SYSTEM.md](file:///home/nchen10/antigravity_prjs/kg/docs/09_RANKING_RECOMMENDATION_AND_VISUALIZATION_SYSTEM.md)** | **Post-Audit Monograph (Part III)**: Leak-free within-seed Goal 1 ranker, within-stratum Mondrian conformal `FDR`, Goal 2 MONDO-blocked & hard-negative combo validation, `0.96s` 4-family CPU scorer, and 15-View Studio + Stat/DS Slide Decks |
| **[10_CRITICAL_AUDIT_AND_AMGEN_NEXT_STEPS_PLAN.md](file:///home/nchen10/antigravity_prjs/kg/docs/10_CRITICAL_AUDIT_AND_AMGEN_NEXT_STEPS_PLAN.md)** | **Post-Audit Monograph (Part IV)**: Biostatistics & translational root-cause audit (`RC-1`..`RC-5`), verified Priority 1 + 2a/2c remediations, turnkey Priority 2b + 4 Colab travel ablations, and Priority 3/5/6 Amgen-wide roadmap |
| **[11_AMG757_OPTION1_COMPLETE_GUIDE_AND_COLLEAGUE_PLAYBOOK.md](file:///home/nchen10/antigravity_prjs/kg/docs/11_AMG757_OPTION1_COMPLETE_GUIDE_AND_COLLEAGUE_PLAYBOOK.md)** ([中文](file:///home/nchen10/antigravity_prjs/kg/docs/zh/11_AMG757_OPTION1_COMPLETE_GUIDE_AND_COLLEAGUE_PLAYBOOK_ZH.md)) | **Historical AMG 757 Option-1 (`v2.0` / `v3.2` Biopsy Atlas) Playbook** *(composite MCDA superseded by `docs/16`)*: `DLL3 × CD3` BiTE `AND`-gate, unboosted conformal `p_cal`, canonical subtype registry, within-platform primary biopsy RNA-seq (`2,215` primary + `106` line profiles), and Simon Two-Stage calculators |
| **[12_AMG757_OPTION2_MODALITY_CONDITIONED_BITE_HEAD.md](file:///home/nchen10/antigravity_prjs/kg/docs/12_AMG757_OPTION2_MODALITY_CONDITIONED_BITE_HEAD.md)** ([中文](file:///home/nchen10/antigravity_prjs/kg/docs/zh/12_AMG757_OPTION2_MODALITY_CONDITIONED_BITE_HEAD_ZH.md)) | **Historical Option-2 `OptimusBiTE-Head` (`v1.3 g4_max`) & Google Colab `G4 96 GB` Guide** *(subject to `docs/16` `PE1` audit & Appendix B `Ion Guard`)*: `SVD-LoRA` + `Modality-FiLM` across `5` modalities (`TCE`/`ADC`/`CELL`/`RLT`/`MAB`), `ESM-2 3B` ECD embeddings, `<= 14 nm` synapse biophysics, & `DLL3 + TAA_2` `OR`-gate escape rescue |
| **[13_AMG757_COMPREHENSIVE_INDICATION_EXPANSION_AND_RECOMMENDATIONS.md](file:///home/nchen10/antigravity_prjs/kg/docs/13_AMG757_COMPREHENSIVE_INDICATION_EXPANSION_AND_RECOMMENDATIONS.md)** ([中文](file:///home/nchen10/antigravity_prjs/kg/docs/zh/13_AMG757_COMPREHENSIVE_INDICATION_EXPANSION_AND_RECOMMENDATIONS_ZH.md)) | **Superseded Stage-3 (`v3.2.1`) AMG 757 Historical Baseline**: Preserved for comparison against `v4.3.2` (`67.8%` Shapley `IHC_pos` repetition audit in `results_v4/amg757_pillar_attribution.json`) |
| **[14_AMG691_COMBINATION_THERAPY_AND_BISPECIFIC_DISCOVERY.md](file:///home/nchen10/antigravity_prjs/kg/docs/14_AMG691_COMBINATION_THERAPY_AND_BISPECIFIC_DISCOVERY.md)** ([中文](file:///home/nchen10/antigravity_prjs/kg/docs/zh/14_AMG691_COMBINATION_THERAPY_AND_BISPECIFIC_DISCOVERY_ZH.md)) | **Canonical AMG 691 `v2.0` (`NCT06637371` & `NCT07745907`) Combination Therapy, `T_RM` Biology & Next-Gen Bispecific Report**: `GSE193816` `52,152` airway single cells, `1,642` treatment-refractory human biopsies, `14`-anchor `AUROC = 1.0000`, and Phase 2a combination roadmaps in Asthma/CRSwNP, AD, UC/CD, and HS |
| **[15_AMG691_APPROACH_MODELING_SCORING_AND_WORKFLOW_REPORT_EN.md](file:///home/nchen10/antigravity_prjs/kg/docs/15_AMG691_APPROACH_MODELING_SCORING_AND_WORKFLOW_REPORT_EN.md)** ([中文](file:///home/nchen10/antigravity_prjs/kg/docs/zh/15_AMG691_APPROACH_MODELING_SCORING_AND_WORKFLOW_REPORT_ZH.md)) | **Canonical AMG 691 `v2.0` Mathematical Modeling, Scoring Equations & End-to-End Workflow Reference**: Complete scoring architecture, pharmacology guardrails, non-circular 2025–2026 clinical calibration, and reproducible pipeline specifications |
| **[16_AMG757_V4_EVIDENCE_MODEL.md](file:///home/nchen10/antigravity_prjs/kg/docs/16_AMG757_V4_EVIDENCE_MODEL.md)** ([中文](file:///home/nchen10/antigravity_prjs/kg/docs/zh/16_AMG757_V4_EVIDENCE_MODEL_ZH.md)) | **Canonical AMG 757 (`Tarlatamab` / `IMDELLTRA`) `v4.3.2` Audited Evidence-Backed Indication Expansion & Dual-Route Trial Model** ([Bilingual HTML Studio](file:///home/nchen10/antigravity_prjs/kg/results_v4/AMG757_Tarlatamab_V4_Evidence_Studio.html) • [EN 18-Slide PPTX](file:///home/nchen10/antigravity_prjs/kg/results_v4/AMG757_Biostatistics_Evidence_Model_v4_3_EN.pptx) • [ZH 18-Slide PPTX](file:///home/nchen10/antigravity_prjs/kg/results_v4/AMG757_Biostatistics_Evidence_Model_v4_3_ZH.pptx)): Enriched `120`-trial `DLL3` registry audit (`12` active/committed + `7` historical/completed SCLC studies in **Table A** `$2,610.8M` vs `21` external academic IITs), cross-agent calibrated `Q2` (`a0 = 0.50`), **Table B** Route `V1` External IIT Federation (`0.90` base & `0.75` drug-supply attrition sensitivity; `EVSI = +$12.94M` [`$12.35M` exact `2036` LOE]) vs Route `V2` De Novo Master Basket, `WP9` multi-regime (`A1`–`A3`) basket trade-offs, sealed strict pre-`T` `PE1` discovery back-test (`Q5` retired), unbiased `Q4` check (`recall@50 = 0.000`), Appendix B `Ion Guard`, 4-cadence Maintenance Mode SOP, and `3/3` settled Bitcoin-PoW OpenTimestamps append-only forecast ledger (`41` sealed rows across `2026_11.jsonl` + `2026_11b.jsonl` + `2026_11c.jsonl`, strictly separating `5` `q2_empirical_model` forecasts from `4` `analyst_prior` forecasts) |

Historical design logs and superseded playbooks are preserved in [docs/archive/](file:///home/nchen10/antigravity_prjs/kg/docs/archive/README.md).  
**Simplified Chinese Documentation Suite (`docs/zh/` & `forecasts/`)**: [README_ZH.md](file:///home/nchen10/antigravity_prjs/kg/docs/zh/README_ZH.md) • [METRICS_ZH.md](file:///home/nchen10/antigravity_prjs/kg/docs/zh/METRICS_ZH.md) • [ANTIGRAVITY_PROJECT_HANDOFF_ZH.md](file:///home/nchen10/antigravity_prjs/kg/docs/zh/ANTIGRAVITY_PROJECT_HANDOFF_ZH.md) • [forecasts/README_ZH.md](file:///home/nchen10/antigravity_prjs/kg/forecasts/README_ZH.md) • [11_ZH](file:///home/nchen10/antigravity_prjs/kg/docs/zh/11_AMG757_OPTION1_COMPLETE_GUIDE_AND_COLLEAGUE_PLAYBOOK_ZH.md) • [12_ZH](file:///home/nchen10/antigravity_prjs/kg/docs/zh/12_AMG757_OPTION2_MODALITY_CONDITIONED_BITE_HEAD_ZH.md) • [13_ZH](file:///home/nchen10/antigravity_prjs/kg/docs/zh/13_AMG757_COMPREHENSIVE_INDICATION_EXPANSION_AND_RECOMMENDATIONS_ZH.md) • [14_ZH](file:///home/nchen10/antigravity_prjs/kg/docs/zh/14_AMG691_COMBINATION_THERAPY_AND_BISPECIFIC_DISCOVERY_ZH.md) • [15_ZH](file:///home/nchen10/antigravity_prjs/kg/docs/zh/15_AMG691_APPROACH_MODELING_SCORING_AND_WORKFLOW_REPORT_ZH.md) • [16_ZH](file:///home/nchen10/antigravity_prjs/kg/docs/zh/16_AMG757_V4_EVIDENCE_MODEL_ZH.md).

---

## Verified Post-Audit Production Benchmarks (`Nov 15, 2026` — Canonical Source: [`METRICS.md`](file:///home/nchen10/antigravity_prjs/kg/METRICS.md))

* **Honest 3-Tier Knowledge Graph Comparison vs. PrimeKG**:
  * **Tier A (Raw OptimusKG v3.2)**: `192,813` nodes (`1.49×`), `21,834,669` edges (`5.39×`), `65` resources.
  * **Tier B (Curated GNN Graph `clinical_v4`)**: `139,439` nodes (`1.08×`), `9,385,536` bidir edges (`1.16×`), `30` relation names (`50` typed canonical triplets), `18,640 + 23` drugs (`2.35×`; `3,353` [`18.0%`] carry `>=1` indication), `9,895` indications (`~1.05×` across `1,268` diseases; `93.0%` of `18,025` diseases have zero indications), `11,673` contraindications (`0.38×`).
  * **Tier C (Stage-2 Pharmacology, Safety & IP Side Layers)**: `36,641` signed MoA edges, `1,345,373` 4-type DDIs, `8.90M` Open Targets `L2G` pairs, `34,718` MONDO DAG edges, `OnSIDES v3.1.1` (`44,607` drug–ADE pairs), FDA Orange/Purple Book (`2,494` off-patent generics/biosimilars), `768-D` `MedCPT` bank, and split-locked `59`-relation signed `clinical_v5`.
* **Production Ensemble (`24` Correlated `fp32` Checkpoints + `d = 3072` Stage-3 `GraphMask`) & Reconciled Own-Test Pools**:
  * Largest single model = **`944.2M` GNN weights (`d = 3072`, `35.9×` released `26.3M` TxGNN)**; single-model Own-Test Indication AUPRC peaks at **`0.3504`** (`seed = 1`, range `0.2843–0.3504`).
  * **Reconciled Own-Test Denominators**: Across the **`3,353` active clinical drugs** (`evaluator.py` denominator), the 24-model within-seed ensemble achieves **`0.3379` 3-seed mean AUPRC (`0.3603` on `seed = 1`, beating `0.3504`)**; across **all `18,640` drug nodes** (`5.56×` larger denominator), it achieves **`0.2460`** (beating every individual 6-ckpt family `0.2003–0.2343`).
* **Complete Paired Unique-Disease Cluster Bootstrap vs. External Drive TxGNN (`207` Shared Diseases × `5,297` Shared Drugs, `10,000` Resamples)**:
  * **All `207` Shared Diseases (`398 / 421` = `94.5%` of test pairs were inside Drive TxGNN's PrimeKG training set!)**: **`AUPRC = 0.4380` vs. `0.2156`** (**`+0.2224 [+0.1545, +0.2880]`**, **`2.03×`**, `P = 1.0000`), `Recall@50 = 0.6468` vs. `0.7745`, `AUROC = 0.9597` vs. `0.9903`.
  * **Doubly Held-Out Stratum (`0%` Drive Training Overlap, `n = 22` unique diseases)**: Ours beats Drive on **both `AUPRC` (`0.4542` vs. `0.0509`, `+0.4033 [+0.2232, +0.5874]`, `8.93×`) and `Recall@50` (`0.6818` vs. `0.4545`, `1.50×`)**!
* **Goal 1 — Portfolio Disease Repurposing & Stratified Blinded Cold-Target Triage**:
  * **Blinded Cold-Target Holdout (`n = 124` zero-train-indication evaluations across `16,992` targetable diseases, `deg = 0` forced)**: **`Hits@20 = 77.42%`** (`MedRank = 5.0`), comprising **`90.53%` `Hits@20` (`MedRank = 3.0`) on `n = 95` Same-Target Class-Precedent evaluations** and **`34.48%` `Hits@20` (`293.0×` random, `Hits@50 = 48.28%`, `MedRank = 51.0`) on `n = 29` Strictly Novel Target–Indication evaluations** (`0.96s` per confidential target signature on CPU).
  * **Leak-Free Within-Seed OOF Seen-Drug Ordering (`392` Drugs/Seed × `18,025` Diseases)**: **`Hits@10 = 73.45%`**, **`Hits@30 = 85.11%`**, **`Median Best Rank = 3.0`**.
  * **Deduplicated & Trial-Audited Portfolio Output (`2,350` rows = `47 × 50`)**: **`12` clean deduplicated rows at `FDR <= 10%` across `5 / 47` assets** (`2` at `FDR <= 5%`, `median_best_q = 0.5396`; `42 / 47` exploratory; `47` top-5 rows tagged `Tested in Phase 2/3 — Not Approved`; `0` ontology child/umbrella or `OnSIDES` ADE leaks).
* **Goal 2 — `OptimusCombo-Net` Co-Administration Feasibility & IP/BD Shortlist (`53,952` Clinical Triples, `1,059` Diseases)**:
  * **Strict Rule-Free NCT Clinical Trials 5-Fold MONDO-Blocked OOF (`15,712` NCT multi-agent vs. H2H/antag trials; all `8` rule-overlapping features zeroed; `0` swaps)**: **`0.9714` AUROC / `0.9944` AUPRC** *(vs `0.8762` baseline)*
  * **Rule-Free 5-Fold MONDO-Blocked Across All `53,952` Triples**: **`0.9327` AUROC / `0.8849` AUPRC** *(full-feature MONDO-blocked `0.9499`)*
  * **5-Fold Amgen-Drug-Cold (`3,550` Strictly Held-Out Amgen Triples)**: **`0.9343` AUROC / `0.8949` AUPRC** | Exact Swap Symmetry `5.96e-08` | **`5,760` Tier 1** (`Amgen × Amgen`), **`5,760` Tier 2** (`Amgen × Off-Patent Generic/Biosimilar`), and **`5,760` Tier 3** (`External Clinical Benchmarks`) BD regimens.
* **Canonical AMG 757 / Tarlatamab (`IMDELLTRA®`) `v4.3.2` Audited Evidence Model (`results_v4/` & `docs/16`)**:
  * **Table A Committed SCLC Baseline (`12` Active/Committed SCLC Studies + `7` Historical/Completed Baseline Studies = `19` Registry Studies)**: `EXP-S1` (`DeLLphi-305` OS HR `0.42 [0.32–0.54]` + `DeLLphi-312`) + `EXP-S2` (`NCT07531095` `+ZL-1310`, `NCT06898957` `+YL201`, `DeLLphi-310 SC`) + `EXP-S3` (`DeLLphi-306` + `PrECOG NCT07637513`) = **`$2,610.8M` (`2036` statutory floor) / `$3,306.6M` (`2039` patent scenario)**.
  * **Table B Incremental Options & Dual-Route Rare Non-SCLC Execution (`2036-05-16` Shared Moiety Floor, `-$469.3M` `2L` Cannibalization, `FD&C §529 PRV = $0`)**: `#1 EXP-S4` Active Brain Mets (`NCT07402343`, `N = 35`, `33.0%†` systemic reference ORR, `+$59.7M`; `TRIG-06` 3-zone clinical gate `>=12/35` expand, `8..11/35` subgroup review `P = 36.0%` [`-$21.71M` vs binary `-$34.01M`], `<8/35` halt), `#2 EXP-L1` Pulmonary `LCNEC` (`IFCT NCT06937905`, `0` direct tarlatamab trials, `a0 = 0.50` primary posterior `44.5%` vs `29.9%` drop-Obrixtamig, `V0 +$29.6M` / `V1 +$27.1M` / `V2 +$34.3M`; Thoracic NE `EXP-L1 + EXP-L2` = `V0 +$33.5M` / `V1 +$38.0M` / `V2 +$47.5M`), `#3 EXP-L2` `t-SCLC` (`V2 +$13.2M`), `#4 EXP-A` `MCC` (`V2 +$12.6M`); rare non-SCLC standalone INDs sum to `-$34.6M` (`V0`), vs **`+$1.8M` under Route `V1` (`11` open external IITs, `N = 517`; `-$4.3M` at `0.75` drug-supply attrition sensitivity; Bayesian `EVSI = +$12.94M` [`$12.35M` exact `2036` LOE])**, and **`+$10.6M` under Route `V2` (`De Novo` Amgen Registrational Master Basket; Simon `$20.96M` `<= 4.99%` type I vs `EXNEX<=10%` `$17.24M` `<= 8.25%` type I)**.
  * **Leak-Guarded Calibration & Cryptographic Governance**: `Q1` `LOSO` `100%` coverage (`Winkler95 = 0.615`); `Q2` `LOCO` `100%` coverage (`Winkler95 = 0.510`); sealed `PE1` 32-antigen back-test `P5 = 0.378` (`MLPClassifier((16, 8), activation='tanh', solver='lbfgs', alpha=8.0, tol=0.001, max_iter=300)`) vs static `M5 = 0.501` (`+0.123` post-cutoff degree inflation, `wp25_passed = False` -> `Q5` retired); unbiased `Q4` `recall@50 = 0.000`; `41` append-only `.jsonl` forecast records (`5` `q2_empirical_model` + `4` `analyst_prior` strictly separated) with all `3/3` `.ots` proofs anchored to Bitcoin blocks `#975,929`, `#975,963`, `#976,004` & `#976,009`.
* **Canonical AMG 691 `v2.0` (`CD30/CD30L`, Phase 1 `NCT06637371` & `NCT07745907`, `docs/14–15`)**:
  * Primary human allergic-asthma airway single cells (`GSE193816`, `52,152` cells) confirm **`0.29%` epithelial overlap**, **`15.69×` `CD30L/OX40L` enrichment**, and **`94.99%` `FOXP3+ Treg` sparing**; `GSE157194` confirms **`105.6%` residual lesional `CD30L`** on `Dupilumab`; `14`-anchor non-circular 2025–2026 human clinical benchmark achieves **`AUROC = 1.0000`**; top wholly-owned Phase 2a combinations: **Asthma/CRSwNP `+ Tezepelumab` (`0.7650 / 0.7885`)**, **UC/CD `+ Wezlana` (`0.8329 / 0.7852`)**, and **AD `+ Rocatinlimab` (`0.7432`)**.
* **Historical AMG 757 `Option-1 v2.0/v3.2` & `Option-2 OptimusBiTE-Head` (`v1.3 g4_max`, Superseded by `v4.3.2` for Portfolio Decisions)**:
  * **`Option-2 g4_max` (`24` Checkpoints + `ESM-2 3B` ECD, `10 × 5` Paralog-Grouped Antigen-Cold CV)**: Direction A `MRR = 0.1187 -> 0.3595` (`AUROC = 0.9851`), Direction B (`2,381` surfaceome) `MRR = 0.0120 -> 0.2454`, Held-out `TCE` `MRR = 0.2474 -> 0.6536` (`AUROC = 0.9938`), `DLL3 × TCE` SCLC rank `#1,767 (frozen) -> #16 (DLL3-cold OOF) -> #1 (final)`. Under `v4.3.2` Appendix B `Ion Guard`, CNS/cardiac voltage-gated ion channels (`KCNB2`, `SCN3A`) are demoted (`F_ion_guard = 0.15`), making **`DLL3 + IGSF9` (`0.4745`, `73.3%` bulk / `14.9%` scRNA rescue, `0.49 TPM` brain max, `1.18%` TIL)** the `#1` clean next-generation Track 3 candidate ahead of `DLL3 + PTK7` (`0.2202`) and `DLL3 + MUC4-beta` (`0.2192`).

### Controlled Architecture & Scaling Ladder (`clinical_v4`, `full_graph` Seeds `[42, 1, 2]`, `207` Shared Diseases)

| Model Family (`8` or `2` Within-Seed Checkpoints / Split) | Single-Model GNN Weights | Shared-Drug AUPRC (`207` Dis.) | Paired $\Delta$ vs. Drive TxGNN (`95%` CI) | Own-Test `3,353` Clin. / `18,640` All-Drug AUPRC |
| :--- | :---: | :---: | :---: | :---: |
| **External Ref: Google Drive TxGNN (`PrimeKG`, `d = 512`, `94.5%` in Train)** | `26.3M` | `0.2156` (`0.0509` on `0%`-overlap) | — | — |
| **OptimusKG-TxGNN (`d = 1024`, `u = 0.2`, `s42` control)** | `105.0M` (`4.0×`) | `0.3010` | `+0.0415` (`s42`) | `0.2597` / — |
| **`v4 wmatch` (`d = 2048`, `6`-ckpt within-seed mean)** | `419.7M` (`16.0×`) | `0.4022` | `+0.1866 [+0.1205, +0.2512]` | `0.3122` / `0.2143` |
| **`v5bi` Bidirectional (`d = 2048`, `6`-ckpt within-seed mean)** | `419.7M` (`16.0×`) | `0.3919` | `+0.1763 [+0.1095, +0.2414]` | `0.2990` / `0.2003` |
| **`v5bi` Bidirectional (`d = 3072`, `6`-ckpt within-seed mean)** | `944.2M` (`35.9×`) | `0.3948` | `+0.1792 [+0.1112, +0.2454]` | `0.3266` / `0.2286` |
| **`v5fm` MedCPT + Chem (`d = 2048`, `6`-ckpt within-seed mean)** | `441.8M` (`16.8×`) | `0.4275` | `+0.2119 [+0.1445, +0.2773]` | `0.3145` / `0.2343` |
| **Full `24`-Model Production Ensemble (`8` within-seed / split)** | `419.7M–944.2M` | **`0.4380`** (**`0.4542`** on `0%`-overlap) | **`+0.2224 [+0.1545, +0.2880]` (`2.03×`; `8.93×` on `0%`-overlap)** | **`0.3379` (`s1: 0.3603`) / `0.2460`** |

Zero-shot unseen disease area holdout (`complex_disease`, `5` seeds `[42, 1, 2, 3, 4]`, `d = 1024`): **`0.1403` (`5`-seed `u=0.5+deg` mean) to `0.1678` (`s42 fix_dst`) Indication AUPRC**; turnkey **`d = 2048` zero-shot + signed `clinical_v5` (`59` relations) paired ablation** is packaged in [`run_travel_priority2b_and_4_ablations.py`](file:///home/nchen10/antigravity_prjs/kg/run_travel_priority2b_and_4_ablations.py) (**Colab Cell `4D`**).

---

## Repository Layout

```text
/home/nchen10/antigravity_prjs/kg/
├── Makefile                                  # Offline v4.3.2 audit, test, trial-watcher, forecast-scoring, OTS-upgrade & fresh-clone targets
├── optimus_txgnn/                            # Core library (dual dgl/torch engine, HeteroRGCN, Bayesian Q1–Q6, AMG 691 v2)
│   ├── dgl_compat.py                         # Native DGL + pure-PyTorch TorchHeteroGraph (~256 MB chunked scatter)
│   ├── data.py                               # OptimusTxData loader, 3-way split guards & SHA-256 fingerprints
│   ├── clinical_v5_builder.py                # ~4s zero-raw-parquet builder for split-locked 59-relation signed clinical_v5
│   ├── model.py                              # O(N d^2) HeteroRGCN, gated H^(0) MedCPT adapter & sparse CSR disease prototype
│   ├── trainer.py                            # 3-stage OptimusTxGNN trainer, Full_Graph_NegSampler & AMP/checkpointing
│   ├── evaluator.py                          # Vectorized disease-centric evaluator & paired bootstrap comparator
│   ├── graphmask.py                          # 2-layer SoftConcrete + Lagrangian GraphMask explainer
│   ├── explain.py                            # Exact 2-layer Integrated Gradients & causal multi-hop path ranker
│   ├── ip_registry.py                        # FDA Orange/Purple Book patent/generic IP classifier & Amgen YAML loader
│   ├── ontology_and_safety_filters.py        # MONDO 47,892-edge DAG closure + OnSIDES v3.1.1 / MedDRA & target-class ADE mask
│   ├── calibration.py                        # Per-disease z-score, within-stratum BH FDR, cold/hybrid projection & AmgenRepurposeRanker
│   ├── combo_model.py                        # 3-Head Symmetric OptimusCombo-Net, MONDO-blocked CV & hard-negative stress test
│   ├── models/                               # Canonical AMG 757 v4.3.2 Q1–Q4/Q6 models, Table A/B mapper, decision table & EVSI triggers
│   ├── backtest/                             # Strict pre-cutoff temporal DLL3 & 32-antigen PE1 discovery backtest engines
│   ├── basket_design.py                      # Exact Simon Two-Stage, EXNEX<=10% & Federated IIT basket operating-characteristics engine
│   ├── regulatory_rules.py                   # Statutory PHS §351(k)(7), FD&C §529 PRV ($0) & BPCA §351(m) pediatric exclusivity rules
│   ├── amg691_v2_combination_engine.py       # Canonical AMG 691 v2.0 CD30/CD30L scRNA/biopsy calibration, combo & bispecific engine
│   ├── bite_biomarker_and_expansion.py       # Solid-tumor BiTE CD3 deconfounding, germline LoF mask & unboosted conformal hook
│   ├── option1_protocol_and_omics.py         # Historical Option-1 subtype guardrails, 5-pillar MCDA, Dirichlet & exact Simon engine
│   ├── option2_bite_head.py                  # Option-2 OptimusBiTE-Head (SVD-LoRA + Modality-FiLM, protocol=2 weights_only=True pack)
│   ├── portable_pack.py                      # Cross-Python (3.8 <-> 3.13) portable inference pack serializer (protocol=2 tensor-only)
│   └── studio_ui_*.py                        # Modular 15-View Discovery Studio HTML/CSS/D3/Canvas frontend templates
├── scripts/                                  # Deterministic v4.3.2 builders, report/studio generator, forecast scorer & share-bundle auditor
│   ├── build_evidence_store_v1.py            # Builds & checksums 260-row primary-verified evidence store + 120-trial CT.gov snapshot
│   ├── verify_evidence_sources.py            # Offline SHA-256 & verbatim PubMed/CT.gov source provenance verifier
│   ├── build_amg757_v4_reports_and_studio.py # Single-source generator for docs/16*, docs/zh/16* & bilingual V4 Evidence Studio HTML
│   ├── score_prospective_forecasts.py        # Offline Bitcoin PoW (.ots) verifier, CT.gov trial watcher & separated Q2/analyst scorecards
│   └── audit_share_bundle.py                 # Boundary-safe scanner enforcing zero superseded v3/v4.2 strings across share bundles
├── configs/                                  # Portfolio, regulatory/rNPV, molecular-gate & biomarker YAML registries
├── forecasts/                                # Append-only SHA-256 + 3/3 Bitcoin-PoW (.ots #975,929..#976,009) 41-row forecast ledger
├── results_v4/                               # Git-tracked AMG 757 v4.3.2 CSV/JSON/Parquet outputs, provenance snapshots, Studio & PPTX
├── build_amg757_biostat_pptx.py              # Zero-drift single-source 18-slide EN/ZH Biostatistics PPTX builder
├── build_optimuskg_amg757_amg691_presentation.py # Single-source 16-slide EN/ZH OptimusKG + AMG 757 v4.3.2 + AMG 691 v2.0 PPTX/HTML builder
├── generate_metrics_ledger.py                # Machine-verified generator for METRICS.md (Sections 1–7)
├── docs/                                     # 16 English monographs/reports + docs/zh/ (10 Simplified Chinese counterparts) + figures
└── tests/                                    # 23 pytest suites (100% offline v4.3.2 clean-clone parity + dual dgl/torch backends)
```

---

## Quickstart Commands

### A. AMG 757 `v4.3.2` Audited Evidence Model, Bitcoin-PoW Forecast Ledger, Share-Bundle Audit & Maintenance Mode (`Makefile`)

Because all `v4.3.2` artifacts, enriched offline PubMed/ClinicalTrials.gov (`120` `DLL3` trials)/pan-antigen provenance snapshots, bilingual 18-slide `.pptx` decks, `EVSI` decision triggers, and `3/3` settled Bitcoin-PoW OpenTimestamps append-only prospective forecasts (`41` sealed rows across `2026_11`, `2026_11b`, `2026_11c`; blocks `#975,929`, `#975,963`, `#976,004` & `#976,009`) are tracked in Git under `results_v4/` and `forecasts/`, the complete `v4.3.2` verification and maintenance suite runs **100% offline**:

```bash
# 1. Fast (<30s) offline audit of 260 evidence rows, 41 sealed ledger rows (Bitcoin PoW), share bundle, & all v4.3.2 pytest suites
make verify-fast

# 2. Audit external share bundles (doc_to_gdrive/, visualization_studio/, PPTX, DOCX, HTML, MD) for zero superseded v3/v4.2 drift
make bundle-audit

# 3. Full verification across every pytest suite in the repository (strict weights_only=True across all 42 .pt checkpoints & root bundles)
make verify

# 4. Clone HEAD into an isolated /tmp directory (without gitignored data_optimuskg/ or amgen_discovery_studio/) and verify clean-clone parity
make verify-fresh-clone

# 5. Maintenance Mode SOP (4 cadences: quarterly CT.gov trial watcher [incl. quarantined UCLA NCT06788938], event-driven forecast scoring, OTS Bitcoin upgrade, annual re-fit only if new TCE data arrive)
make watch-trials
make score-forecasts
make ots-upgrade
```

#### Clean-Clone Skipped Legacy Tests (`13` Tests Requiring Local Gitignored `v3` / `AMG 691` / Raw Graph Bundles)

Every AMG 757 `v4.3.2` test runs unconditionally on a fresh clone (`0` skips across `test_evidence_store.py`, `test_registry_and_anchors.py`, `test_q1_q2_bayesian_models.py`, `test_q3_q4_q6_models.py`, `test_basket_design.py`, `test_backtest_no_future_leak.py`, `test_prospective_forecasts.py`, `test_doc16_zero_drift.py`, `test_torch_weights_only_hygiene.py`, `test_regulatory_rules.py`, `test_pillar_attribution.py`, and `test_option2_debiased_v2.py`). On a clean clone without the large local `data_optimuskg/` and `amgen_discovery_studio/` bundles, exactly **`13` legacy `v3` / `AMG 691` / raw-graph tests** intentionally skip:

| # | Test File & Function | Required Local Gitignored Bundle(s) |
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

### B. Amgen Goal 1 + Goal 2 Pipeline, 16-Slide Platform Deck & Live Discovery Studio (`:8004`)

```bash
# 1. Run leak-free within-seed 47-asset Goal 1 + Goal 2 pipeline + blinded target holdout + 4-family CPU pack
.venv/bin/python run_amgen_goal1_and_goal2_pipeline.py
.venv/bin/python evaluate_24model_ensemble_vs_drive.py

# 2. Build Explorer Bundle, 15-View Discovery Studio, Bilingual 16-Slide Platform Decks & Launch :8004 Server
.venv/bin/python build_amgen_explorer_bundle.py
.venv/bin/python build_amgen_studio_and_decks.py
.venv-v4/bin/python build_optimuskg_amg757_amg691_presentation.py
.venv/bin/python serve_amgen_studio.py --port 8004

# 3. Instant 0.96s CPU triage (no GPU required) for any confidential AMG-XXXX target signature
.venv/bin/python score_new_amgen_asset.py \
    --name "AMG-NEXT-DUAL" \
    --targets "TSLP:inhibits,IL13:inhibits" \
    --out_json ./amgen_discovery_studio/results/sample_post_ultra_query.json
```

### C. Turnkey Colab GPU Travel Queue (Priority 2b Zero-Shot Disease + Priority 4 Signed `clinical_v5` Ablation)

```bash
# Locally package & smoke-test colab_upload/ (includes self-contained Cell 4D in OptimusKG_TxGNN_G4_96GB_Colab.ipynb)
.venv/bin/python package_for_colab.py

# Or run directly via CLI on any GPU node (auto-builds split-locked clinical_v5 in ~4s and syncs TRAVEL_PRIORITY_2B_AND_4_REPORT.md):
python run_travel_priority2b_and_4_ablations.py \
    --enable_tf32 \
    --local_runs_root /content/work/optimuskg_txgnn/runs \
    --drive_runs_root /content/drive/MyDrive/optimuskg_txgnn/runs \
    --drive_folder /content/drive/MyDrive/optimuskg_txgnn
```

### D. Historical AMG 757 Option-1 Rebuild & Option-2 `OptimusBiTE-Head` (`protocol=2 weights_only=True`)

```bash
# 1. Deterministic offline rebuild of Option-1 empirical bundle & protocol MCDA baseline
.venv/bin/python build_amg757_option1_empirical_data.py --offline

# 2. Option-2 OptimusBiTE-Head: build/augment 24-checkpoint bank, validate locally, & package Google Colab G4 bundle
.venv/bin/python build_option2_bite_head_bank.py --augment_features_only
.venv/bin/python train_option2_bite_head.py --profile quick --out_dir ./amgen_discovery_studio/option2_bite_head
.venv/bin/python package_option2_colab.py
```

### E. Core CLI Utilities & Dual-Backend Test Suite

```bash
# Verify all pytest suites on Python 3.12 (.venv-v4) across native DGL and pure-PyTorch backends
.venv-v4/bin/pytest -q
OPTIMUS_GRAPH_BACKEND=torch OPTIMUS_DISABLE_NATIVE_DGL=1 .venv-v4/bin/pytest -q
```
