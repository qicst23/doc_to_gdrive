# Share-Ready Executive, Biostatistics & Study-Team Deliverables Index (`AMG 757 v4.3.2` & `AMG 691 v2.0`)

**Updated:** `2026-11-15` · **Release Tags:** **`amg757-v4.3.2`** & **`amg691-v2.0`** · **Audit Status:** `make verify-fast && make bundle-audit` **`0` errors, `0` drift**

This share-ready Google Drive bundle contains **only finalized `AMG 757 v4.3.2` and `AMG 691 v2.0` documents (`.md`, `.docx`, `.pdf`), artifact-compiled bilingual widescreen PowerPoint decks (`.pptx`), self-contained offline interactive HTML5 studios (`.html`), publication figures (`.png`), and audited data tables (`.csv`)** in **1:1 English (`EN`) and Simplified Chinese (`ZH`)**.

本共享文件夹已严格剔除所有编程源码、二进制模型权重及已退役的历史旧版材料（所有 `v3.x`/`v4.2` 历史材料已移出本共享包，可通过 Git 标签 `amg757-v4.2.0`、`amg757-v4.3.0`、`amg757-v4.3.1` 精确复现），仅保留经 `make verify-fast` 与 `make bundle-audit` 零漂移校验通过的 **`AMG 757 v4.3.2`** 与 **`AMG 691 v2.0`** 中英文双语正式定稿。

> [!NOTE]
> **Historical Reproducibility (`amg757-v4.2.0` / `amg757-v4.3.0` / `amg757-v4.3.1`):** Earlier milestones remain reproducibly tagged in Git (`git checkout amg757-v4.2.0`, `amg757-v4.3.0`, or `amg757-v4.3.1`). Every AMG 757 deliverable inside this folder reflects the **final audited `v4.3.2` ground truth**:
> - **`Table A` Committed SCLC Franchise Baseline (`12` active/committed SCLC expansion/lifecycle studies [`2` partner-sponsored] + `7` historical/completed baseline studies = `19` registry entries):** **`$2,610.8M`** (`2036-05-16` `PHS Act §351(k)(7)` floor) / **`$3,306.6M`** (`2039` patent), net of `-$469.3M` `2L` cannibalization (`DeLLphi-305` OS HR `0.42 [0.32–0.54]`, `+ZL-1310 NCT07531095`, `+YL201 NCT06898957`).
> - **`Table B` Incremental Options (`#1 EXP-S4` Active Brain Mets `NCT07402343` `N=35` = `+$59.7M` [`33.0%†` systemic SCLC ref; `TRIG-06` 3-zone clinical gate]; `#2 EXP-L1` Pulmonary `LCNEC` `IFCT NCT06937905` = `+$27.1M` `V1` / `+$34.3M` `V2` [`Q2 = 44.5%` at `a_0 = 0.50`]):** Rare non-SCLC (`EXP-A..G`) sums to **`-$34.6M`** standalone (`Route V0`), **`+$1.8M`** under `Route V1` external IIT signal federation (`-$4.3M` at `0.75` drug-supply attrition; **`EVSI = +$12.94M`** [`$12.35M` exact `2036` LOE] across `11` open IITs, `N=517`), and **`+$10.6M`** under `Route V2` de novo Amgen master basket (`NCT06788938` verified as a closed `29`-patient UCLA single-arm academic study).
> - **Statutory Pediatric Law, Retired `Q5`, `Ion Guard` & Bitcoin `.ots` Ledger:** `FD&C Act §529 PRV = $0.0M`, mandatory `PREA/RACE §505B = 0.0y`, voluntary `BPCA §351(m) = +0.5y` (`+$184.8M` base, `+$172.3M` net on `EXP-G`); `Q5` retired (`wp25_passed = False` on leak-free `PE1_new_indication`, frozen `P5 = 0.378` vs `M5 = 0.501`); Appendix B `Ion Guard` demotes `KCNB2`/`SCN3A` (`DLL3 + IGSF9` `#1` at `0.4745`); `41`-row forecast ledger **`3/3` settled into Bitcoin blocks `#975,929`, `#975,963`, `#976,004` & `#976,009`** (`5` `Q2` model + `4` separated `analyst_prior` scorecards).

---

## Folder Map / 文件夹目录导览

### `01_AMG757_Indication_Expansion/` — `IMDELLTRA` (`AMG 757` / `Tarlatamab`) Canonical `v4.3.2`
1. **`01_Presentation_Decks_and_Interactive_Demo/`**
   - `AMG757_Biostatistics_Evidence_Model_v4_3_EN.pptx` & `AMG757_Biostatistics_Evidence_Model_v4_3_ZH.pptx` — Artifact-compiled `18`-slide widescreen decks (`0` hand-typed numbers).
   - `AMG757_Tarlatamab_V4_Evidence_Studio.html` — Single-file offline bilingual (`EN / 中文`) interactive HTML5 governance studio (`5` tabs).
   - `AMG757_VISUALIZATION_LIVE_DEMO_GUIDE_EN.md` & `_ZH.md` — `7`-minute click-by-click demo script & Q&A tab cheat sheet.
2. **`02_Presentation_Plans_and_Speaker_Playbooks/`**
   - `AMG757_BIOSTATISTICS_PRESENTATION_PLAN_EN.md` & `_ZH.md` — Complete `18`-slide speaker script + Biostatistics/Regulatory/Finance red-team Q&A defense matrix.
3. **`03_Plain_Language_Step_by_Step_Reports/`**
   - `AMG757_STEP_BY_STEP_PLAIN_LANGUAGE_REPORT_EN.md` & `_ZH.md` — Non-jargon step-by-step executive & cross-functional walkthrough.
4. **`04_Technical_Deep_Dive_Reports/`** (Markdown `.md` + Word `.docx` + Print-Ready `.pdf` in `EN` & `ZH`)
   - `AMG757_V4_EVIDENCE_MODEL_{EN,ZH}.{md,docx,pdf}` (plus `16_AMG757_V4_EVIDENCE_MODEL*.md`) — Zero-drift `v4.3.2` biostatistical, registry, valuation, basket & forecast monograph.
   - `AMG757_COMPLETE_METHODOLOGY_AND_DECISION_PLAYBOOK_{EN,ZH}.{md,docx,pdf}` — Zero-to-expert `12`-chapter educational textbook (`ADC vs BiTE` physics -> `OptimusKG` -> `24`-GNN campaign -> `Phase 8 / v4.3.2` overhaul).
   - `AMG757_CODEBASE_ARCHITECTURE_MATH_AND_IO_GUIDE_{EN,ZH}.{md,docx,pdf}` — Companion `9`-phase software architecture, mathematical equations, and Data I/O contract manual.
5. **`05_Data_Tables_CSV/` & `figures/`**
   - All `13` active `results_v4/*.csv` decision, prevalence, posterior, back-test, basket OC, IIT inventory, VoI trigger, and construct tables (plus `2` supporting Q5/Q6 reference CSVs = `15` total CSVs in `05_Data_Tables_CSV/`) + bilingual publication figures (`figures/` & `figures/zh/`).

---

### `02_AMG691_Indication_and_Combination_Expansion/` — `AMG 691` (`CD30 / CD30L`) Canonical `v2.0`
1. **`01_Presentation_Decks_and_Interactive_Demo/`** — `AMG691_Study_Team_Knowledge_Graph_Combination_Strategy_v2_0_{EN,ZH}.pptx`, `AMG691_CD30_Combination_Discovery_Studio.html`, and live demo guides (`EN` / `ZH`).
2. **`02_Presentation_Plans_and_Speaker_Playbooks/`** — Slide-by-slide speaker playbooks (`EN` / `ZH`).
3. **`03_Plain_Language_Step_by_Step_Reports/`** — Plain-language immunology & combination walkthroughs (`EN` / `ZH`).
4. **`04_Technical_Deep_Dive_Reports/`** — Full `v2.0` combination reports, zero-to-expert playbooks (`.md`, `.docx`, `.pdf`), and codebase/math manuals (`.md`, `.docx`, `.pdf`) in `EN` & `ZH`.
5. **`05_Data_Tables_CSV/` & `figures/`** — `amg691_v2_*.csv` master tables and bilingual figures.

---

### `03_OptimusKG_Platform_and_Metrics_Reference/` — Foundation Platform & Cross-Program Overview
- `OptimusKG_Models_Apps_AMG757_AMG691_Presentation_{EN,ZH}.pptx` & `OptimusKG_Models_Apps_AMG757_AMG691_Slide_Deck_{EN,ZH}.html` — Unified platform + `AMG 757 v4.3.2` + `AMG 691 v2.0` executive presentations.
- `README.md` / `README_ZH.md` & `METRICS.md` / `METRICS_ZH.md` — Synchronized `1:1` bilingual repository index and single-source quantitative metrics ledgers.
- `01_DESIGN_AND_IMPLEMENTATION.md`, `06_AMGEN_DISCOVERY_STUDIO_DESIGN_AND_FEATURE_GUIDE.md`, `08_MODEL_TRAINING_CAMPAIGNS_AND_BENCHMARKS.md`, `09_RANKING_RECOMMENDATION_AND_VISUALIZATION_SYSTEM.md` — Core `OptimusKG` platform architecture & training documentation.
