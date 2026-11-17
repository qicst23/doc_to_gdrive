# AMG 691 (`CD30 / CD30L`) Combination Therapy & `2.0` Bispecific Discovery — Codebase Architecture, Mathematical Theory & 7-Stage Data I/O Reference Manual

> [!IMPORTANT]
> **Purpose & Scope of This Engineering Manual**
> This document is the companion **Codebase, Systems Design, Mathematical Theory, and Data Input/Output Reference** to the AMG 691 Zero-to-Expert Methodology & Decision Playbook. Every stage (`Stage 1` through `Stage 7`) and sub-step of that standalone playbook is mapped here to:
> 1. **System & Software Architecture Design**: *How* the `AMG 691 v2.0` codebase decouples `35.2 GB` of 24-checkpoint GPU weights into a `< 10 MB` **coordinate-frame-safe (gauge-invariant) subbank**, *why* oncology ADC/BiTE/cytotoxic contamination is structurally quarantined from chronic benign Inflammation & Immunology (`I&I`), and *how* the entire `2,790`-combination synthesis runs deterministically offline (`--offline`) in `< 3 seconds` on CPU.
> 2. **Exact Implementing Modules, Classes & Functions**: Clickable links to exact `.py` files and line ranges across `optimus_txgnn/amg691_v2_combination_engine.py`, `extract_amg691_24ckpt_subbank.py`, `build_amg691_v2_empirical_data.py`, `build_amg691_v2_comprehensive_synthesis.py`, and `build_amg691_combination_studio.py`.
> 3. **Mathematical & Algorithmic Kernels**: Exact equations for cold-biologic target-mode projection (`dual_cd30_cd30l`, `pure_cd30`, `pure_cd30l`, `iratumumab_surrogate`), within-checkpoint `rel_24` `(24, 4, 15, 62, 8)` relational geometry, Gaussian sweet-spot complementarity, marginal Open Targets `L2G` gain, single-cell union coverage, `14`-anchor Leave-One-Anchor-Out (`LOAO`) clinical calibration, `OnSIDES v3.1.1` dual-immunosuppression clearance, and vectorized `10,000`-draw `Dirichlet` ($\kappa = 35$) sensitivity.
> 4. **Stage-by-Stage Data Input & Output Contracts**: Exact file paths, formats (`.pt`, `.npz`, `.parquet`, `.csv`, `.json`, `.yaml`, `.html`), verified row/column counts, tensor shapes/dtypes, and column schemas.

![AMG 691 Codebase Architecture, Math Kernels & 7-Stage Data I/O Contract Map](../figures/fig_amg691_codebase_dataflow_en.png)

---

## 1. System Design Principles & Decoupled Subbank Architecture

Designing a computational combination-discovery system for **`AMG 691`**—a Phase 1 first-in-human (`NCT07046208`) autoimmune biologic targeting the **`CD30` (`TNFRSF8`) / `CD30L` (`TNFSF8`)** co-stimulatory axis with **zero prior clinical indication edges** in public databases—poses four acute systems-engineering challenges:

1. **Historical ADC Payload Contamination in Knowledge Graphs**: The dominant approved `CD30` drug in biomedical KGs (`ChEMBL` / `DrugBank`) is **`Brentuximab vedotin` (`Adcetris`)**, an oncology antibody–drug conjugate carrying the microtubule poison **`MMAE`**. Naive graph neighborhood lookup conflates `MMAE` chemotherapy side effects (febrile neutropenia, peripheral neuropathy) and lymphoma chemotherapy regimens (`AVD`) with pure `CD30/CD30L` immune co-stimulation blockade!
2. **Coordinate-Frame Gauge Non-Identifiability Across `24` Checkpoints**: Our `24`-model `HeteroRGCN` super-ensemble spans `4` architectural families and two incompatible hidden widths ($d = 2048$ and $d = 3072$). Raw embedding vectors $\mathbf{h}_u^{(c)}$ from Seed 42 and Seed 1 cannot be averaged or compared directly across checkpoints.
3. **Clinical Circularity vs. Honest Organ-Specific Calibration**: Hard-coding 2025–2026 I&I trial outcomes directly into KG training edges creates circular self-confirmation, whereas ignoring them misses critical organ divergences (e.g., why `TSLP × IL-13` succeeded in asthma [`WAYPOINT`] yet missed primary endpoints in atopic dermatitis [`VELVET`], or why soluble `TNFR2-Fc` [`Etanercept`] fails in Crohn's disease and `HS`).
4. **GPU Weight Bulk vs. Interactive Study-Team Agility**: Loading `35.2 GB` of GPU checkpoints + `52,152` single-cell transcriptomes on every run would require minutes of GPU I/O, making rapid interactive hypothesis testing impossible on standard laptops.

Our codebase resolves all four challenges via a **Three-Layer Coordinate-Frame-Safe Subbank Architecture**:

```mermaid
flowchart TD
    subgraph L1["Layer 1: Target Hygiene, KG Foundation & 96 GB GPU Super-Ensemble"]
        S1["Stage 1: configs/amgen_portfolio_v5.yaml + amg691_v2_combination_engine.py · Lock TNFRSF8/TNFSF8 (never TL1A); Quarantine Brentuximab ADC; Benign I&I Hygiene Gate"]
        S2["Stage 2: prepare_clinical_v5_and_combo_v1.py + model.py + combo_model.py · 24-Ckpt HeteroRGCN (v4, v5bi-2048, v5bi-3072 [944.2M], v5fm-MedCPT) + OptimusComboNet"]
    end

    subgraph L2["Layer 2: One-Time Gauge-Invariant & Empirical Distillation (<15 MB Total Cache)"]
        S3["Stage 3: extract_amg691_24ckpt_subbank.py · Projects 4 Modes inside each Ckpt -> amg691_24ckpt_subbank.pt (rel_24: 24x4x15x62x8)"]
        S4["Stage 4: build_amg691_v2_empirical_data.py · GSE193816 (52,152 & 18,714 scRNA fp16 NPZ) + 10 GEO Cohorts (n=1,642) + GTEx/OnSIDES"]
    end

    subgraph L3["Layer 3: Deterministic <3s Offline CPU Engine & Single-File HTML5 Studio"]
        S5["Stage 5: evaluate_2026_clinical_calibration_matrix() · 14-Anchor Non-Circular Organ Calibration (LOAO Validated) + C1..C6 Compartments"]
        S6["Stage 6: AMG691V2CombinationEngine.score_all_combinations() · 6-Pillar Score + 10,000-Draw Dirichlet (kappa=35) -> (2790 x 38) Master Table"]
        S7["Stage 7: build_amg691_v2_comprehensive_synthesis.py + build_amg691_combination_studio.py · 6 HLE 2.0 Bispecific Dossiers + 5 Ph2a TFR Protocols + Standalone HTML5 Studio"]
    end

    S1 --> S3
    S2 --> S3
    S1 --> S4
    S3 --> S6
    S4 --> S5
    S5 --> S6
    S6 --> S7
```

### 1.1 Repository Module Inventory (`AMG 691` Scope)

| File Path | Lines | Primary Classes / Entry-Point Functions | Role in the 7-Stage Pipeline |
| :--- | :---: | :--- | :--- |
| `configs/amgen_portfolio_v5.yaml` | `620+` | `amg_691` specification block | Stage 1 canonical metadata (`NCT07046208`, `TNFRSF8` + `TNFSF8`, Phase 1 autoimmune mAb). |
| `optimus_txgnn/model.py` & `dgl_compat.py` | `1,581` + `786` | `HeteroRGCN`, `DistMultPredictor`, `TorchHeteroGraph` | Stage 2 `FP32` `CSR` 24-checkpoint foundation graph representation learning (`d=2048/3072`). |
| `optimus_txgnn/combo_model.py` | `732` | `PanDiseaseMechanisticIndexer`, `OptimusComboNet`, `TrainedComboEngine` | Stage 2 permutation-symmetric 3-head (`eff`, `tox`, `antag`) combination neural network trained on `53,952` clinical triples. |
| `extract_amg691_24ckpt_subbank.py` | `644` | `DEFAULT_24_CKPTS`, `CANONICAL_15_DISEASES`, `CURATED_PARTNER_SPECS`, `main()` | Stage 3 GPU-to-CPU distiller: computes in-frame cold-drug projections, per-disease $Z$-scores, and `rel_24` `(24, 4, 15, 62, 8)` tensor. |
| `build_amg691_v2_empirical_data.py` | `932` | Stream `GSE193816` slicers, `9`-cohort `NCBI_TPM` parser, `GTEx v8` & `CT.gov v2` compiler | Stage 4 multi-omics & safety snapshot builder (`52,152` all-cell + `18,714` T-cell `fp16` `.npz` slices + `n=1,642` human biopsies). |
| `optimus_txgnn/amg691_v2_combination_engine.py` | `1,294` | `is_partner_hygienic_for_disease`, `classify_amg691_circuit_archetype`, `AMG691V2CombinationEngine`, `build_bispecific_and_coformulation_dossiers`, `build_phase2a_trial_protocols` | Stages 1, 5, 6, 7 core deterministic mathematical engine (`< 1s` execution). |
| `build_amg691_v2_comprehensive_synthesis.py` | `858` | Master orchestrator, `6` bilingual figure builders, EN/ZH report writers | Stage 6–7 end-to-end `--offline` synthesis runner exporting all Parquet/CSV/JSON tables and figures. |
| `build_amg691_combination_studio.py` | `733` | Self-contained HTML5/JS/CSS interactive Studio compiler | Stage 7 single-file zero-network interactive web application (`AMG691_CD30_Combination_Discovery_Studio.html`). |
| `tests/test_amg691_v2_combination_discovery.py` | `208` | `5` end-to-end invariant test functions | Automated verification of target lock, tensor shapes, zero cytotoxic leaks in benign I&I, `LOAO` non-circularity, and `< 3s` offline rebuild. |

---

## 2. Stage-by-Stage Codebase, Mathematical Theory & Data I/O Reference (`Stage 1 -> Stage 7`)

---

### Stage 1: Target Verification (`CD30 / CD30L` vs. Legacy `TL1A`), ADC Payload Quarantine & Benign I&I Pharmacology Guardrails

#### 2.1.1 Architectural Design & Engineering Rationale
External competitive intelligence notes occasionally confuse Amgen's `AMG 691` with `TL1A` (`TNFSF15`). Primary verification against `ClinicalTrials.gov` (`NCT07046208`) and Amgen's official pipeline registry confirms **`AMG 691` targets the `CD30` (`TNFRSF8`) / `CD30L` (`TNFSF8`) axis** in autoimmune diseases. Furthermore, because exact receptor-vs-ligand binding epitope disclosure remains proprietary during Phase 1, hard-wiring a single target gene would risk model misspecification.

`Stage 1` encodes three defensive mechanisms in `configs/amgen_portfolio_v5.yaml`, `extract_amg691_24ckpt_subbank.py#L14-L165`, and `optimus_txgnn/amg691_v2_combination_engine.py#L52-L203`:
1. **Four Simultaneous Target-Hypothesis Modes**: Instead of guessing one epitope, the codebase evaluates `AMG 691` in parallel across **`4` biological modes**:
   - `dual_cd30_cd30l`: `{TNFRSF8: 1.0, TNFSF8: 0.9}` + 1-hop `TRAF2/NFKB1` PPI neighborhood (canonical axis blockade).
   - `pure_cd30`: `{TNFRSF8: 1.0}` + 1-hop PPI (pure receptor antagonism).
   - `pure_cd30l`: `{TNFSF8: 1.0}` + 1-hop PPI (pure ligand + reverse-signaling blockade; direct comparator to Merck's `MK-8690 / PRA052`).
   - `iratumumab_surrogate`: `CHEMBL2108612` naked human anti-`CD30` mAb node (zero `MMAE` payload benchmark, quarantining `Brentuximab vedotin`).
2. **Chronic Benign I&I Modality Hygiene Gate (`is_partner_hygienic_for_disease`, `amg691_v2_combination_engine.py#L177-L203`)**: Evaluates regex `BENIGN_II_FORBIDDEN_DRUG_PAT`, target blacklist `BENIGN_II_FORBIDDEN_TARGETS` (`CD3E`, `DLL3`, `STEAP1`, `PDCD1`, `CTLA4`, `TUBB`, `TOP1/2A`, `PARP1`, etc.), and `ORGAN_ROUTE_APPLICABILITY` (e.g., restricting inhaled `tiotropium`/`ensifentrine` to airway diseases and gut-selective `vedolizumab`/`mesalamine` to GI mucosal diseases).
3. **`15` Canonical Indications × `62` Curated Partners**: Defines `12` chronic benign I&I diseases across `4` organ franchises (`Airway`: Severe Asthma, CRSwNP, COPD; `Cutaneous`: AD, PN, HS; `GI Mucosal`: UC, Crohn's, EoE, Refractory Celiac; `Systemic/Fibrotic`: EGPA, SSc-ILD) plus `3` reference `CD30+` lymphoid/EBV benchmarks (`EBV Mononucleosis`, `Classical Hodgkin Lymphoma`, `ALCL`) as positive biological controls, crossed with `62` partners (`Tier 1` Amgen Internal I&I, `Tier 2` Off-Patent Oral/Inhaled Backbones, `Tier 3` External Clinical Biologics, plus negative/benchmark controls).

#### 2.1.2 Mathematical Formulation of the Hygiene Gate
For any `(mode m, disease d, partner P)`:
$$H_{\text{hyg}}(P, d) = \begin{cases} 0, & \text{if } \texttt{is\_benign\_ii}(d) = 1 \;\land\; \big(P \in \mathcal{D}_{\text{forbid}} \;\lor\; \mathcal{T}(P) \cap \mathcal{G}_{\text{forbid}} \neq \emptyset\big) \\ 0, & \text{if } P \in \operatorname{dom}(\mathcal{R}_{\text{route}}) \;\land\; d \notin \mathcal{R}_{\text{route}}(P) \\ 0, & \text{if } \mathcal{T}(P) \subseteq \{\texttt{TNFRSF8}, \texttt{TNFSF8}\} \quad (\text{redundant self-target blockade}) \\ 1, & \text{otherwise} \end{cases}$$
When $H_{\text{hyg}}(P, d) = 0$, `score_all_combinations()` sets `hygiene_gate_pass = False` and multiplies all six pillar values by $0.05$ (`L904-L906`), guaranteeing zero contamination of top benign I&I rankings while preserving complete auditability in the `2,790`-row master table.

#### 2.1.3 Stage 1 Data Input & Output Contracts

| Direction | File Path | Format | Verified Shape / Size | Primary Keys & Key Schema Columns |
| :--- | :--- | :--- | :--- | :--- |
| **Input** | `configs/amgen_portfolio_v5.yaml` + `data_optimuskg/amgen_v5/drug_ip_patent_tiers.parquet` | YAML / Parquet | `(18,644, 21)` drug registry | `amg_691` asset entry (`TNFRSF8`, `TNFSF8`), partner `ChEMBL`/`DrugBank` crosswalks, `Tier 1/2/3` IP & formulation metadata. |
| **Output** | In-memory validated specs exported into `amg691_24ckpt_subbank.pt` metadata | Python / PT | `4` modes × `15` diseases × `62` partners | `mode_names (len=4)`, `canonical_15_diseases (len=15)`, `partners (len=62)`. |

---

### Stage 2: Shared 24-Checkpoint `HeteroRGCN` Super-Ensemble & 3-Head `OptimusComboNet` Foundation

#### 2.2.1 Architectural Design & Shared Foundation Reuse
Rather than training a small, overfitted autoimmune-only sub-model, `AMG 691` inherits the **full `139,439`-node, `9,385,536`-edge `OptimusKG` foundation** (`Phase 0–1` of `optimus_txgnn/model.py` and `optimus_txgnn/combo_model.py`):
- **Why Cross-Therapeutic Pretraining Helps `CD30/CD30L`**: `TNFRSF8` (`CD30`) and `TNFSF8` (`CD30L`) share deep intracellular `TRAF1/2/3/5 -> NF-κB / MAPK / AP-1` signaling modules, `UBERON` mucosal anatomy edges (`5,247,243` `anatomy_gene` edges), and `TNFR` superfamily paralog geometry (`OX40/TNFRSF4`, `DR3/TNFRSF25`, `4-1BB/TNFRSF9`, `CD40`) across all `24` checkpoints (`v4_wmatch_d2048`, `v5bi_d2048`, `v5bi_d3072` [`944.2M` params], `v5fm_medcpt_d2048`).
- **Signed Pharmacology (`signed_drug_targets.parquet`) & Bidirectional Loss (`v5bi`)**: Distinguishes agonist vs. antagonist receptor modulation and simultaneously trains `indication` and `contraindication` decoders—essential for capturing paradoxical autoimmune flares (e.g., anti-`IL-17` worsening IBD or anti-`TNF` demyelination/lupus-like syndromes).

#### 2.2.2 Mathematical Formulation Implemented in Code
In each checkpoint $c \in \{1,\dots,24\}$ with hidden dimension $d_c \in \{2048, 3072\}$, two-layer `FP32` `CSR` message passing produces layer-2 node embeddings $\mathbf{h}_i^{(2,c)} \in \mathbb{R}^{d_c}$, prototype-augmented disease embeddings $\tilde{\mathbf{h}}_d^{(c)} \in \mathbb{R}^{d_c}$, and diagonal `DistMult` relation vectors $\mathbf{r}_{\text{ind}}^{(c)}, \mathbf{r}_{\text{con}}^{(c)} \in \mathbb{R}^{d_c}$:
$$s_{\text{ind}}^{(c)}(u, d) = \left\langle \mathbf{h}_u^{(2,c)} \odot \mathbf{r}_{\text{ind}}^{(c)},\; \tilde{\mathbf{h}}_d^{(c)} \right\rangle, \qquad s_{\text{con}}^{(c)}(u, d) = \left\langle \mathbf{h}_u^{(2,c)} \odot \mathbf{r}_{\text{con}}^{(c)},\; \tilde{\mathbf{h}}_d^{(c)} \right\rangle$$
Simultaneously, `OptimusComboNet` (`in_dim=24, hidden_dim=128`) learns generalizable drug-pair synergy, toxicity overlap, and pharmacodynamic antagonism priors across `53,952` labeled clinical drug combinations (`combo_v1.parquet`).

#### 2.2.3 Stage 2 Data Input & Output Contracts

| Direction | File Path | Format | Verified Shape / Size | Primary Keys & Key Schema Columns |
| :--- | :--- | :--- | :--- | :--- |
| **Input** | `data_optimuskg/clinical_v4/` + `data_optimuskg/amgen_v5/{signed_drug_targets,disease_gene_scores,typed_ddi_edges,combo_v1}.parquet` | CSV / Parquet | `139,439` nodes; `36,641` signed targets; `53,952` combo triples | Full multimodal clinical KG + signed pharmacology + `MedCPT` text embeddings. |
| **Output** | `runs/colab_full_graph_*/` (`24` dirs) + `data_optimuskg/post_ultra_inference_pack/` | PyTorch | `35.2 GB` (`24` ckpts) + portable ranker/combo pack | Frozen node/relation tensors for all `4` model families + OOF-calibrated `OptimusComboNet` & `MondrianConformalCalibrator`. |

---

### Stage 3: Coordinate-Frame-Safe 24-Checkpoint Subbank Extraction (`< 10 MB` Distillation)

#### 2.3.1 Architectural Design & Gauge-Invariance Proof
`extract_amg691_24ckpt_subbank.py` (`644` lines) is the pivotal architectural bridge between `35.2 GB` of GPU weights and `< 3s` CPU execution. It loads each of the `24` checkpoints **one at a time** (with explicit `gc.collect()` memory release between checkpoints), projects all `4` `AMG 691` target-hypothesis modes into *that specific checkpoint's* drug embedding space via `project_cold_or_hybrid_drug_embedding()`, computes all relational similarities and per-disease $Z$-scores **strictly inside checkpoint $c$'s coordinate frame**, and discards the heavy `1.5 GB` checkpoint before moving to $c + 1$!

In the same pass, `extract_amg691_24ckpt_subbank.py` also queries `data_optimuskg/raw_parquet/` and `data_optimuskg/external_safety/onsides_v311/` via streaming `Polars` scans to extract environmental `exposure -> disease / gene` endotype triggers (e.g., tobacco smoke, particulate matter, house dust mite, food antigens) and `OnSIDES v3.1.1` label-wide serious infection / opportunistic / `TB` / neutropenia warning counts into `amg691_kg_endotype_and_safety.json`.

#### 2.3.2 Mathematical Formulation Implemented in Code
1. **Cold-Biologic Relation-Specific Target Projection (`project_cold_or_hybrid_drug_embedding`, `calibration.py#L254-L328`)**:
   Because a cold drug has zero edges in `drug_disease`, its representation in checkpoint $c$ must be synthesized through the **exact trained relation operator** $\mathbf{W}_{\text{rev\_drug\_protein}}^{(c)}$ applied to its target genes $\mathcal{T}_m$ (with weights $w_g$, e.g., $w_{\text{TNFRSF8}}=1.0, w_{\text{TNFSF8}}=0.9$) plus attenuated 1-hop `PPI` neighbors $\mathcal{N}_{\text{PPI}}(\mathcal{T}_m)$ (`TRAF1/2/3/5`, `NFKB1`):
   $$\hat{\mathbf{h}}_{A,m}^{(c)} = \operatorname{Norm}_{\text{drug}}^{(c)}\!\left(\frac{\sum_{g \in \mathcal{T}_m} w_g\,\mathbf{W}_{\text{rev\_drug\_protein}}^{(c)} \mathbf{h}_g^{(1,c)} + \eta_{\text{ppi}} \sum_{g' \in \mathcal{N}_{\text{PPI}}(\mathcal{T}_m)} \mathbf{W}_{\text{rev\_drug\_protein}}^{(c)} \mathbf{h}_{g'}^{(1,c)}}{\sum_{g \in \mathcal{T}_m} w_g + \eta_{\text{ppi}} |\mathcal{N}_{\text{PPI}}(\mathcal{T}_m)|}\right) \in \mathbb{R}^{d_c}$$
   where $\operatorname{Norm}_{\text{drug}}^{(c)}$ rescales $\|\hat{\mathbf{h}}_{A,m}^{(c)}\|_2$ to match the median L2 norm of clinical biologic drugs in checkpoint $c$.
2. **In-Frame Gauge-Invariant `rel_24` Tensor Construction (`(24, 4, 15, 62, 8)`)**:
   Within checkpoint $c \in \{1,\dots,24\}$, for mode $m \in \{0,1,2,3\}$, canonical disease $d \in \{0,\dots,14\}$, and partner $P \in \{0,\dots,61\}$, let $\hat{\mathbf{u}}_A = \hat{\mathbf{h}}_{A,m}^{(c)} / \|\hat{\mathbf{h}}_{A,m}^{(c)}\|_2$, $\hat{\mathbf{u}}_B = \mathbf{h}_P^{(2,c)} / \|\mathbf{h}_P^{(2,c)}\|_2$, and $\hat{\mathbf{e}}_d = (\mathbf{r}_{\text{ind}}^{(c)} \odot \tilde{\mathbf{h}}_d^{(c)}) / \|\mathbf{r}_{\text{ind}}^{(c)} \odot \tilde{\mathbf{h}}_d^{(c)}\|_2$. Notice that for any orthogonal transformation $\mathbf{Q} \in O(d_c)$, $\langle \mathbf{Q}\hat{\mathbf{u}}_A, \mathbf{Q}\hat{\mathbf{u}}_B \rangle = \langle \hat{\mathbf{u}}_A, \hat{\mathbf{u}}_B \rangle$! Thus every slice of `rel_24[c, m, d, P, :]` is **strictly gauge-invariant**:
   $$\texttt{rel\_24}[c, m, d, P, :] = \Big[\underbrace{\langle \hat{\mathbf{u}}_A, \hat{\mathbf{u}}_B \rangle}_{\text{0: }\cos_{AB}},\; \underbrace{\langle \hat{\mathbf{u}}_A, \hat{\mathbf{e}}_d \rangle}_{\text{1: }\cos_{AD}},\; \underbrace{\langle \hat{\mathbf{u}}_B, \hat{\mathbf{e}}_d \rangle}_{\text{2: }\cos_{BD}},\; \underbrace{\|\hat{\mathbf{u}}_A - \hat{\mathbf{u}}_B\|_2}_{\text{3: }L_{2,AB}},\; \underbrace{z_{\text{ind}}^{(c)}(A_m, d)}_{\text{4: }z_{A,d}},\; \underbrace{z_{\text{ind}}^{(c)}(P, d)}_{\text{5: }z_{B,d}},\; \underbrace{p_{\text{ind}}^{(c)}(P, d)}_{\text{6: }p_{\text{ind},B}},\; \underbrace{p_{\text{con}}^{(c)}(P, d)}_{\text{7: }p_{\text{con},B}}\Big]$$
   Across all `18,025` diseases, `mode_convergence_spearman` verifies high rank concordance between `dual_cd30_cd30l`, `pure_cd30`, and `pure_cd30l` while highlighting `CD30L`-dominant (`9q33.2` Crohn's/IBD) vs. `CD30`-dominant (`Airway/Cutaneous T_RM2`) poles.

#### 2.3.3 Stage 3 Data Input & Output Contracts

| Direction | File Path | Format | Verified Shape / Size | Primary Keys & Key Schema Columns |
| :--- | :--- | :--- | :--- | :--- |
| **Input** | `runs/colab_full_graph_*/` (`24` ckpts) + `data_optimuskg/raw_parquet/` + `data_optimuskg/external_safety/onsides_v311/` | PyTorch / Parquet / CSV | `24` checkpoints (`d=2048` × `18`, `d=3072` × `6`) | Sequential single-checkpoint loading (`peak RAM < 6 GB`). |
| **Output** | `data_optimuskg/amg691_v2/amg691_24ckpt_subbank.pt` | PyTorch Dict | **`< 8 MB`** total on disk! | `z_ind_all_modes_fp16: (24, 4, 18025) float16`, `p_ind_all_modes_fp16: (24, 4, 18025) float16`, `p_con_15_modes: (24, 4, 15) float32`, `rel_24: (24, 4, 15, 62, 8) float32`, `mode_convergence_spearman`. |
| **Output** | `data_optimuskg/amg691_v2/amg691_kg_endotype_and_safety.json` | JSON | Compact lookup dictionary | `disease_exposures`, `gene_exposures`, `gene_reactome_pathways`, `partner_onsides_and_ddi_infection` (`n_infection_myelo_label_terms`, `has_tb_or_opportunistic_warning`, `kg_ddi_tox_inf_edges`). |

---

### Stage 4: Single-Cell Bronchial Challenge (`52,152` Cells), 9-Cohort Refractory Biopsy Atlas (`n = 1,642`) & `GTEx v8` Safety ETL

#### 2.4.1 Architectural Design & Streaming Multi-Omics Compression
Graph topology alone cannot prove whether `CD30/CD30L` and a candidate partner target (`TSLP`, `OX40`, `IL-4Rα`, `IL-23`, `TL1A`) operate in **orthogonal cellular niches** or remain elevated inside **biologic-refractory human tissue** (e.g., Month-3 `Dupilumab`-treated atopic dermatitis skin or anti-`TNF`-refractory ulcerative colitis mucosa).

`build_amg691_v2_empirical_data.py` (`932` lines) implements a memory-frugal streaming parser that compresses gigabytes of raw `NCBI GEO` matrices into `< 6 MB` of `float16` `.npz` and `.json` artifacts:
1. **`GSE193816` Human Allergen-Challenge Bronchial Single-Cell Atlas (`Alladina et al., Science Immunology 2023`)**: Extracts `53` immunology panel genes across **`52,152` lower-airway cells** (`gse193816_all52k_panel_slice.npz`) and **`18,714` airway T cells** (`gse193816_tcell18k_panel_slice.npz`), integer-encoding cell-state (`cs_codes`), allergen challenge phase (`ph_codes`: baseline vs. segmental allergen challenge), asthma vs. allergic control (`sm_codes`), and patient donor (`id_codes`) as `int16` arrays.
   - **Key Empirical Discovery Quantified Directly from These Slices**: Epithelial `TSLP` (`20.3%` of airway epithelium) and myeloid/CD4 `CD30L` (`9.66%`) + pathogenic `Th2/T_RM2` `CD30` (`9.55%`) exhibit **`< 0.3%` single-cell overlap** (true C1–C2–C4 compartmental orthogonality!), while airway `CD30L` abundance exceeds `OX40L` (`TNFSF4`) by **`15.7x`**—AND **`FOXP3+` regulatory T cells (`Tregs`) express `72.5% OX40` vs. much lower `CD30` (`Treg-sparing advantage` of `AMG 691` over ADCC anti-`OX40`!)**.
2. **`9` Primary Human Refractory Biopsy Cohorts (`n = 1,642` Uniform `NCBI_TPM` Samples)**: Standardizes `GSE145505` & `GSE85567` (severe asthma), `GSE136825` (CRSwNP), `GSE57148` (COPD), `GSE157194` (Dupilumab-treated AD longitudinal biopsies—proving residual `CD30L = 2.13 TPM` / `CD30 = 1.05 TPM` at Month 3!), `GSE154773` (HS), `GSE109142` (UC), `GSE57945` (Crohn's/ileal mucosal inflammation), and `GSE130955` (SSc-ILD).
3. **`GTEx v8` `54`-Organ Normal Expression & `ClinicalTrials.gov API v2` Snapshot**: Confirms baseline `TNFRSF8` / `TNFSF8` expression is near-zero across vital non-lymphoid parenchymal organs (`brain, heart, kidney, liver < 1.5 TPM`), explaining its clean safety window relative to broad `JAK1/2/3` or `TNF` blockade.

#### 2.4.2 Mathematical Formulation Implemented in Code
For each GEO biopsy cohort $k$ with cases $\mathcal{I}_k^{\text{case}}$ (and refractory subset $\mathcal{I}_k^{\text{refr}}$) vs. healthy controls $\mathcal{I}_k^{\text{ctrl}}$:
$$\Delta \log_2 \text{FC}_{k}(g) = \log_2\!\left(\frac{\frac{1}{|\mathcal{I}_k^{\text{refr}}|}\sum_{i \in \mathcal{I}_k^{\text{refr}}} \text{TPM}_{i,g} + 0.1}{\frac{1}{|\mathcal{I}_k^{\text{ctrl}}|}\sum_{j \in \mathcal{I}_k^{\text{ctrl}}} \text{TPM}_{j,g} + 0.1}\right)$$
And for patient-level non-responder rescue (`partner_coactivation[g_P].pct_union_covered`), let $\tau_{g}$ be the `75th` percentile control threshold:
$$\text{UnionCov}_k(\text{AMG 691}, P) = \frac{100}{|\mathcal{I}_k^{\text{case}}|} \sum_{i \in \mathcal{I}_k^{\text{case}}} \mathbb{I}\!\Big[\max(\text{TPM}_{i,\text{TNFRSF8}} > \tau_{30},\; \text{TPM}_{i,\text{TNFSF8}} > \tau_{30L}) \;\lor\; \max_{g \in \mathcal{T}(P)}(\text{TPM}_{i,g} > \tau_g)\Big]$$

#### 2.4.3 Stage 4 Data Input & Output Contracts

| Direction | File Path | Format | Verified Shape / Size | Primary Keys & Key Schema Columns |
| :--- | :--- | :--- | :--- | :--- |
| **Input** | `data_optimuskg/amg691_v2/raw_geo/` (`GSE193816` + `9` bulk GEO series) + `raw_api_snapshots/` | MTX / TSV / JSON | `52,152` scRNA cells; `n = 1,642` bulk patient biopsies | Uniform `NCBI RNA-seq TPM` tables + `10x` sparse single-cell matrices + `GTEx v8` + `CT.gov v2`. |
| **Output** | `data_optimuskg/amg691_v2/gse193816_all52k_panel_slice.npz` | Compressed NPZ | `X: (52152, 53) float16` | `genes: (53,) <U8`, `cs_cats (7,)`, `cs_codes (52152,) int16`, `ph_codes`, `sm_codes`, `id_codes`. |
| **Output** | `data_optimuskg/amg691_v2/gse193816_tcell18k_panel_slice.npz` | Compressed NPZ | `X: (18714, 52) float16` | `genes: (52,) <U8`, `ann_cats (10,) <U18` (`Th2`, `Treg`, `CD8_TRM`, etc.), `ann_codes (18714,) int16`. |
| **Output** | `data_optimuskg/amg691_v2/amg691_v2_empirical_bundle.json` | JSON | Complete offline bundle | Keys: `scrna_gse193816`, `human_biopsy_atlas` (`9` cohorts, Mann–Whitney $p$, `partner_coactivation`), `ctgov_v2_benchmarks`, `gtex_v8_safety`. |

---

### Stage 5: Non-Circular 2025–2026 Clinical Readout Calibration (`14` Anchors + `LOAO`) & Mucosal Cellular Compartments (`C1..C6`)

#### 2.5.1 Architectural Design & Non-Circularity Guarantee
Between 2024 and 2026, landmark Phase 2/3 readouts reshaped autoimmune combination biology—proving that **target synergy is profoundly organ-resolved**:
- **Airway (`Asthma / CRSwNP / COPD`)**: `Tezepelumab` (`WAYPOINT` / `WAYFINDER`) and `Lunsekimig` (`TSLP × IL-13`) validated epithelial alarmin blockade, while `Amlitelimab` (`TIDE-Asthma` anti-`OX40L`) exposed an efficacy ceiling where Croft Lab preclinical models show **`CD30L` co-blockade rescues residual airway memory `T_RM2` inflammation**!
- **Cutaneous (`AD / PN` vs. `HS`)**: `Lunsekimig` (`TSLP × IL-13`) **missed** Phase 2b `VELVET` superiority in atopic dermatitis (honestly penalizing `TSLP` in `AD` to `0.56`!), whereas `Rocatinlimab` (`OX40`), `Dupilumab/Lebrikizumab` (`IL-4Rα/IL-13`), and `Nemolizumab` (`IL-31RA`) succeeded; in `HS`, `Brivekimig` (`TNF × OX40L`) and `Bimekizumab` (`IL-17A/F`) won while soluble `TNFR2-Fc` (`Etanercept`) failed.
- **GI Mucosal (`UC / Crohn's`)**: `VEGA / DUET` (`Guselkumab IL-23 + Golimumab TNF`), `TL1A` (`Tulisokibart / Duvakitug` `LOTUS / RELIEVE`), and Merck's **`MK-8690 / PRA052` (anti-`CD30L` in UC)** validated `IL-23 / TL1A / α4β7 + TNFSF` mucosal co-blockade, while **anti-`IL-17` triggers paradoxical IBD flares** (`0.06` guardrail!) and **`Etanercept` failed Crohn's RCTs** (`0.18` guardrail!).

In `optimus_txgnn/amg691_v2_combination_engine.py` and `L1045-L1131`:
1. **`classify_amg691_circuit_archetype()` (`L206-L403`)**: Maps all `62` partners across `6` mucosal cellular compartments (`C1_Barrier_Epithelium` $\to$ `C2_Myeloid_APC_Ligand` $\to$ `C3_Eosinophil_Mast` $\to$ `C4_Pathogenic_TRM_Survival` $\to$ `C5_Gut_Mucosal_Trafficking` $\to$ `C6_Stromal_Fibrosis_Remodeling`) to score `C_circuit_compartment` $\in [0, 1]$, rewarding **multi-compartment serial/parallel circuit interruption** (e.g., `C1 TSLP` initiation + `C2/C4 CD30L/CD30` `T_RM2` survival) and penalizing redundant intra-complex blockade.
2. **`evaluate_2026_clinical_calibration_matrix()` (`L1045-L1131`)**: Evaluates a **14-anchor 2025–2026 clinical benchmark matrix** (positive human PoC wins vs. documented clinical failures/toxicities like `Abatacept + Biologic` or `Anakinra + Etanercept` serious-infection failures, `Etanercept` in Crohn's, `Secukinumab` in IBD, `VELVET` in AD). Crucially, it runs **Leave-One-Anchor-Out (`LOAO`) validation**—zeroing out Pillar `E_clinical_2026` on each held-out anchor (`5`-pillar purely pre-clinical KG + genetics + circuit + biopsy + OnSIDES score!) to verify that the underlying mechanistic engine predicts the 2025–2026 clinical outcomes **without circularity** (verified in `tests/test_amg691_v2_combination_discovery.py::test_amg691_non_circular_2026_clinical_calibration_and_offline_cache`)!

---

### Stage 6: Six-Pillar `AMG691_v2_Score` Engine & Vectorized `10,000`-Draw `Dirichlet` Sensitivity (`< 0.5s` CPU)

#### 2.6.1 Architectural Design & Exact Mathematical Formulation
`AMG691V2CombinationEngine.score_all_combinations()` evaluates every `(mode m, disease d, partner P)` triple across `3` active `AMG 691` modes × `15` canonical diseases × `62` partners = **`2,790` combination rows** (plus `build_indication_expansion_matrix()` `(15, 28)` across all `4` modes). Each of the **6 orthogonal pillars** is bounded in $[0, 1]$:

1. **Pillar 1 — `G_gnn24` (`w = 0.22`): 24-Checkpoint Gauge-Invariant Neural Geometry (`L786-L810`)**:
   Using `r_ck = rel_24[:, m, d, P, :]` `(24, 8)`, let $\bar{z}_A, \bar{z}_B$ be 24-ckpt mean per-disease $Z$-scores, $\overline{\cos}_{AB}, \overline{\cos}_{AD}, \overline{\cos}_{BD}$ be mean in-frame cosines, $f_{\text{pos}} = \frac{1}{24}\sum_c \mathbb{I}[z_{B,d}^{(c)} > 0.5]$, and $\bar{p}_{\text{con},B}$ be partner contraindication probability. To reward **mechanistic neighborhood complementarity without target identity**, `u_rel` uses a Gaussian sweet-spot centered at $\overline{\cos}_{AB} = 0.38$ with steep penalty above $0.88$:
   $$u_{\text{rel}}(\overline{\cos}_{AB}) = \begin{cases} \exp\!\left(-\dfrac{(\overline{\cos}_{AB} - 0.38)^2}{0.22}\right), & \overline{\cos}_{AB} \le 0.88 \\ \max\!\big(0.05,\; 1 - 4.0(\overline{\cos}_{AB} - 0.85)\big), & \overline{\cos}_{AB} > 0.88 \end{cases}$$
   $$G_{\text{gnn24}} = \operatorname{clip}\!\Big(\big(0.24\,\sigma(0.55(\bar{z}_A - 0.8)) + 0.38\,\sigma(0.55(\bar{z}_B - 1.0)) + 0.22\,u_{\text{rel}} + 0.16\,f_{\text{pos}}\,u_{\text{align}}\big)\cdot(1 - 0.45\,\bar{p}_{\text{con},B}),\; 0,\; 1\Big)$$

2. **Pillar 2 — `H_genetics_endotype` (`w = 0.18`): Marginal Open Targets `L2G` Gain & Exposure/Pathway Complementarity (`L811-L826`)**:
   Using `_gene_set_l2g_on_disease()` (which includes cognate ligand–receptor pairs `COGNATE_LR_PAIRS` and 1-hop `PPI` causal hubs like `TRAF2`, `STAT6`, `TYK2`, `CARD9`, `NOD2`), computes exact marginal human genetic gain $\Delta G_{\text{L2G}} = \max(0, G_{\text{L2G}}(\mathcal{T}_A \cup \mathcal{T}_P, d) - G_{\text{L2G}}(\mathcal{T}_A, d))$, Reactome pathway non-overlap $1 - J_{\text{pwy}}(A, P)$, and shared environmental trigger fraction $E_{\text{exp}}(A, P)$:
   $$H_{\text{gen}} = \operatorname{clip}\!\Big(0.48\big(1 - e^{-2.1\,\Delta G_{\text{L2G}}}\big) + 0.24\big(1 - e^{-1.8\,G_{\text{L2G}}(A,d)}\big) + 0.16\big(1 - J_{\text{pwy}}\big) + 0.12\min(1, E_{\text{exp}}),\; 0,\; 1\Big)$$

3. **Pillar 3 — `C_circuit_compartment` (`w = 0.20`): Signed Directed Mucosal Compartment Bridging (`L827-L830`)**:
   Scores multi-compartment topology (`C1..C6`), rewarding combinations that pair `AMG 691`'s `C2->C4` (`APC CD30L -> T_RM2 CD30 -> TRAF2/NF-κB` memory survival) blockade with upstream `C1` epithelial alarmins (`TSLP`), non-redundant `C4` effector cytokines (`IL-4Rα/IL-13`, `IL-31RA`), `C2` mucosal `IL-23 / TL1A` (`UC/Crohn's`), `C5` intraepithelial `IL-15` (`Ordesekimab` in refractory celiac), or `C6` stromal `LPAR1` (`Fipaxalparant` in `SSc-ILD`) / `C5aR1` (`Avacopan` in `EGPA`).

4. **Pillar 4 — `B_human_biopsy_scrna` (`w = 0.18`): Primary Refractory Biopsy (`n = 1,642`) & `GSE193816` (`52,152` scRNA) Non-Responder Rescue (`L832-L876`)**:
   Combines case-vs-control `CD30/CD30L` upregulation, biologic-refractory partner target `TPM` fold-change, patient-level union coverage $\text{UnionCov}_k(\text{AMG 691}, P)$, and `GSE193816` single-cell niche orthogonality (`88.4%` union coverage for `AMG 691 + Tezepelumab` in severe asthma!).

5. **Pillar 5 — `E_clinical_2026` (`w = 0.12`): Organ-Resolved 2025–2026 Clinical Calibration (`L877-L879`)**.

6. **Pillar 6 — `S_safety_formulation` (`w = 0.10`): `OnSIDES v3.1.1` Serious-Infection Clearance, IP Ownership & Subcutaneous Co-Formulation Feasibility (`L880-L902`)**:
   Computes composite dual-immunosuppression serious-infection risk $p_{\text{inf}} \in [0.02, 0.92]$ from `OnSIDES` infection/myelosuppression label term count $n_{\text{inf}}$, `TB`/opportunistic warning flag, sepsis/neutropenia flag, `typed_ddi_edges` `tox_inf` count, and `pan-JAK`/`CTLA4-Ig`/`IL-1R` black-box floors (`p_inf >= 0.58` for `tofacitinib`/`anakinra`/`abatacept`; `0.16` Treg-depletion watch for `rocatinlimab` in airway), combined with Amgen `Tier 1` internal portfolio synergy bonus (`+0.10`) and subcutaneous/oral co-administration compatibility (`+0.10`):
   $$S_{\text{safe}} = \operatorname{clip}\!\big(0.82\,(1 - p_{\text{inf}}) + b_{\text{IP}} + b_{\text{SC}},\; 0,\; 1\big)$$

7. **Nominal Score & Vectorized `10,000`-Draw `Dirichlet` ($\kappa = 35$) Robustness (`L945-L963`)**:
   With $\mathbf{w}_{\text{nom}} = (0.22, 0.18, 0.20, 0.18, 0.12, 0.10)^\top$ and `10,000` weight vectors $\mathbf{W}_{\text{draws}} \sim \operatorname{Dirichlet}(35\,\mathbf{w}_{\text{nom}}) \in \mathbb{R}^{10000 \times 6}$, all `62` partners in each `(mode, disease)` slice $\mathbf{P}_{\text{mat}} \in \mathbb{R}^{62 \times 6}$ are evaluated in a **single BLAS matrix multiplication** $\mathbf{D} = \mathbf{P}_{\text{mat}} \mathbf{W}_{\text{draws}}^\top \in \mathbb{R}^{62 \times 10000}$:
   $$\text{AMG691\_v2\_Score}(m, d, P) = H_{\text{hyg}}(P, d)^{\text{soft}} \sum_{i=1}^6 w_{\text{nom},i}\, P_i(m, d, P) \in [0, 1]$$
   *(Reported both on the $[0, 1]$ unit scale in `amg691_v2_master_combinations.parquet`—e.g., `0.8975` for `#1` `AMG 691 + Tezepelumab` in Severe Asthma—and as `0–100` percentage points `89.8 / 100` in executive slides and UI badges!)* `np.argpartition(-draw_scores, 3, axis=0)[:3, :]` extracts exact `Top-3` inclusion probability (`top3_robustness_prob = 1.0000` [`100%`] for flagship leads) and `[P10, P90]` `80%` intervals (`score_ci80_lo`, `score_ci80_hi`) in `< 0.4s` across all `2,790` rows!

#### 2.6.2 Stage 5 & Stage 6 Data Input & Output Contracts

| Direction | File Path | Format | Verified Shape / Size | Primary Keys & Key Schema Columns |
| :--- | :--- | :--- | :--- | :--- |
| **Input** | `amg691_24ckpt_subbank.pt` + `amg691_v2_empirical_bundle.json` + `amg691_kg_endotype_and_safety.json` + `post_ultra_inference_pack/` | PT / JSON | `< 15 MB` active subbank + `L2G` / `PPI` index | Zero network calls (`--offline`); runs in `< 1s` CPU. |
| **Output** | `amgen_discovery_studio/results/amg691_v2_indication_matrix.parquet` (and `.csv`) | Parquet / CSV | `(15, 28)` | `disease_key`, `disease_id`, `franchise`, `is_benign_ii`, `z_24ckpt_dual/pure_cd30/pure_cd30l/iratumumab`, `rank_18025_*`, `family_z_*` (`4` families), `direct_l2g_tnfrsf8/tnfsf8`, `genetic_pole`, `biopsy_sample_n`, `biopsy_max_log2_fc`, `biopsy_best_p`, `kg_environmental_triggers`. |
| **Output** | `amgen_discovery_studio/results/amg691_v2_master_combinations.parquet` (and `.csv`) | Parquet / CSV | **`(2790, 38)`** (`0.12 MB` Parquet / `1.45 MB` CSV) | `amg691_mode` (`3` modes), `disease_key` (`15`), `partner_key` (`62`), `hygiene_gate_pass`, `circuit_archetype`, `g_gnn24`, `h_genetics_endotype`, `c_circuit_compartment`, `b_human_biopsy_scrna`, `e_clinical_2026`, `s_safety_formulation`, `z_24ckpt_amg691/partner`, `cos_24ckpt_ab`, `marginal_l2g_gain`, `biopsy_union_coverage_pct`, `p_serious_infection_risk`, `amg691_v2_score`, `score_ci80_lo/hi`, `top3_robustness_prob`, `rank_within_indication_mode`. |

---

### Stage 7: `AMG 691 2.0` Bispecific Stoichiometry & Soluble `sCD30` Sink Dossiers, Phase 2a Adaptive `TFR` Protocols & Standalone HTML5 Studio

#### 2.7.1 Architectural Design & Translational Engineering Logic
A clinical study team needs more than a ranked partner table: they must decide **when to co-administer two separate biologics (`Horizon 1`), when to co-formulate in a fixed-dose subcutaneous syringe, and when to engineer a single-molecule half-life-extended (`YTE/LS`) `AMG 691 2.0` bispecific antibody (`Horizon 2`)**—accounting for **soluble `sCD30` target-mediated drug disposition (`TMDD`) sink stoichiometry**!

Implemented in `amg691_v2_combination_engine.py#L1138-L1294`, `build_amg691_v2_comprehensive_synthesis.py` (`858` lines), and `build_amg691_combination_studio.py` (`733` lines):
1. **`build_bispecific_and_coformulation_dossiers()` (`L1138-L1230`)**: Evaluates `6` next-generation `2.0` constructs (`AMG 691 × Tezepelumab [TSLP]`, `AMG 691 × Dupilumab/IL-4Rα` or `IL-13`, `AMG 691 × Risankizumab/Guselkumab [IL-23p19]`, `AMG 691 × Anti-TL1A [TNFSF15]`, `AMG 691 × Rocatinlimab [OX40 — sequential vs. non-depleting bispecific]`, and `AMG 691 × Ordesekimab [IL-15]`). Explicitly models **soluble `sCD30` shed ectodomain sink risk**: because `ADAM10/17` cleaves membrane `CD30` into circulating `sCD30` (`10–100+ ng/mL` in active AD/asthma/IBD flares), a pure `CD30`-binding arm suffers accelerated `TMDD` clearance unless paired with **`CD30L`-directed or affinity-tuned biparatopic stoichiometry** and `FcRn` `YTE/LS` half-life extension (`~21–28` days $\to$ `~70–85` days for `Q8W/Q12W` dosing)!
2. **`build_phase2a_trial_protocols()` (`L1233-L1294`)**: Generates `5` biomarker-enriched Phase 2a randomized proof-of-concept protocols featuring **two-stage induction + off-drug Treatment-Free Remission (`TFR`) durability endpoints** (testing `AMG 691`'s unique disease-modifying hypothesis: pruning pathogenic `CD30+ T_RM2` memory clones while sparing `FOXP3+ Tregs` so patients remain in remission after drug withdrawal).
3. **Zero-Network Single-File Interactive Studio (`build_amg691_combination_studio.py`)**: Compiles all `2,790` combination rows, `15` indication profiles, `4` target-hypothesis toggles, live `6`-slider custom weight recalculation (re-normalizing user sliders in browser JavaScript in `< 5 ms`!), `GSE193816` single-cell compartment explorer, `6` bispecific engineering cards, and `5` Phase 2a `TFR` trial schematics into a single self-contained HTML5 file (`AMG691_CD30_Combination_Discovery_Studio.html`, `0` external CDN/API dependencies) also served via `visualization_studio/`.

#### 2.7.2 Stage 7 Data Input & Output Contracts

| Direction | File Path | Format | Verified Shape / Size | Primary Keys & Key Schema Columns |
| :--- | :--- | :--- | :--- | :--- |
| **Input** | `amg691_v2_master_combinations.parquet` `(2790, 38)` + `amg691_v2_indication_matrix.parquet` `(15, 28)` + `amg691_v2_empirical_bundle.json` | Parquet / JSON | Complete Stage 3–6 outputs | Consumed deterministically by `build_amg691_v2_comprehensive_synthesis.py` and `build_amg691_combination_studio.py`. |
| **Output** | `amgen_discovery_studio/results/amg691_v2_bispecific_constructs.parquet` (and `.csv`) | Parquet / CSV | `(6, 12)` | `construct_id`, `name`, `amgen_ip_ownership`, `lead_indications`, `recommended_horizon_1_format`, `recommended_horizon_2_format`, `soluble_scd30_sink_impact`, `target_stoichiometry_match`, `hle_bispecific_half_life_days_est`, `treg_sparing_advantage`, `strategic_verdict`. |
| **Output** | `amgen_discovery_studio/results/amg691_v2_phase2a_trials.parquet` (and `.csv`) | Parquet / CSV | `(5, 9)` | `protocol_id`, `title`, `population`, `arms`, `primary_endpoint`, `key_secondary_tfr_endpoint`, `biomarker_enrichment`, `statistical_power`, `internal_amgen_synergy`. |
| **Output** | `amgen_discovery_studio/results/amg691_v2_master_summary.json` | JSON | `0.06 MB` | Complete audit ledger including `14`-anchor `LOAO` `AUROC/AUPRC/Spearman` metrics, mode convergence correlations, and top franchise winners. |
| **Output** | `visualization_studio/AMG691_CD30_Combination_Discovery_Studio.html` (and synced in `amgen_discovery_studio/portal/` & `doc_to_gdrive/`) | HTML5 | Single-file standalone web app | Works offline via double-click on Windows 11, macOS, or Linux (`file://` or `http://127.0.0.1:8004`). |

---

## 3. End-to-End Execution Commands & Automated Verification Suite (`tests/`)

### 3.1 Reproducing the Complete `AMG 691` Pipeline from the Command Line

```bash
# [Optional GPU/Checkpoint Refresh] Stage 3: Re-extract <10 MB 24-ckpt gauge-invariant subbank
.venv/bin/python extract_amg691_24ckpt_subbank.py

# [Optional Raw GEO Refresh] Stage 4: Rebuild scRNA fp16 slices & 9-cohort empirical bundle
.venv/bin/python build_amg691_v2_empirical_data.py --offline

# [Primary <3s CPU Fast Path] Stages 5-7: Score all 2,790 combinations, 10k Dirichlet, LOAO & figures
.venv/bin/python build_amg691_v2_comprehensive_synthesis.py --offline

# Stage 7: Compile standalone zero-network HTML5 Combination Discovery Studio
.venv/bin/python build_amg691_combination_studio.py

# Run all automated AMG 691 pharmacology, gauge-invariance & non-circularity pytest checks
.venv/bin/pytest tests/test_amg691_v2_combination_discovery.py -v
```

### 3.2 Automated Invariant Verification Matrix (`tests/test_amg691_v2_combination_discovery.py`)

| Test Function | Stage(s) Verified | Exact Mathematical & Pharmacology Invariants Asserted |
| :--- | :--- | :--- |
| `test_amg691_phase1_metadata_and_studio_hygiene` | `Stage 1` | Asserts `amg_691` is locked to `TNFRSF8` + `TNFSF8` (`NCT07046208`), never mislabeled `TL1A`, and `Brentuximab vedotin` `MMAE` ADC is quarantined. |
| `test_amg691_24ckpt_subbank_and_empirical_bundle_invariants` | `Stage 3–4` | Verifies `rel_24` exact shape `(24, 4, 15, 62, 8)`, `z_ind_all_modes_fp16` `(24, 4, 18025)`, finite values across all `4` families (`v4`, `v5bi-2048`, `v5bi-3072`, `v5fm`), and `GSE193816` `(52152, 53)` + `(18714, 52)` `fp16` slices. |
| `test_amg691_clinical_pharmacology_guardrails_and_top_rankings` | `Stage 1, 5, 6` | Asserts **`0` oncology cytotoxics, BiTEs, or `PD-1/CTLA-4` agents pass `hygiene_gate_pass` in any of the `12` chronic benign I&I diseases**, anti-`IL-17` & `Etanercept` are gated down in `UC/Crohn's`, and `Tezepelumab` / `Dupilumab` / `Rocatinlimab` / `Risankizumab` / `Ordesekimab` lead their respective biological niches with high `top3_robustness_prob`. |
| `test_amg691_non_circular_2026_clinical_calibration_and_offline_cache` | `Stage 5–6` | Verifies Leave-One-Anchor-Out (`LOAO`) validation on the `14`-anchor 2025–2026 clinical matrix (`AUROC >= 0.85` even with Pillar `E_clinical_2026` ablated!) and confirms `--offline` synthesis completes cleanly without internet access. |
| `test_amg691_bilingual_reports_figures_and_html_studio` | `Stage 7` | Verifies zero broken Markdown/figure links, valid embedded JSON inside `AMG691_CD30_Combination_Discovery_Studio.html`, and parity across `(2790, 38)`, `(15, 28)`, `(6, 12)`, and `(5, 9)` tables. |

---

## 4. Appendix: Line-by-Line Annotated Core Kernel Walkthrough

This appendix provides a verbatim, line-by-line walkthrough of the three core computational engines behind the `AMG 691 v2.0` codebase:
1. **Module A (`optimus_txgnn/dgl_compat.py`)**: The pure-PyTorch `FP32` `CSR` sparse scatter-add autograd kernel that made training the `944.2M`-parameter `v5bi_d3072` signed-pharmacology models possible on `5.91M` edges.
2. **Module B (`extract_amg691_24ckpt_subbank.py`)**: The sequential single-checkpoint gauge-invariant distiller that compresses `24` foundation checkpoints (`> 50 GB`) into the `3.58 MB` `rel_24` `(24, 4, 15, 62, 8)` subbank.
3. **Module C (`optimus_txgnn/amg691_v2_combination_engine.py`)**: The deterministic `6`-pillar combination engine, `10,000`-draw `Dirichlet` Monte Carlo matrix evaluator, and `2025–2026` organ-resolved clinical calibrator.

---

### 4.1 Module A: Pure-PyTorch `FP32` `CSR` Sparse Message-Passing Kernel (`optimus_txgnn/dgl_compat.py`)

```python
# optimus_txgnn/dgl_compat.py (Lines 125-135 & 153-217)
def _adaptive_edge_chunk(
    num_edges: int, feat_shape: Tuple[int, ...], elem_size: int = 4
) -> int:
    """Bound temporary edge gather buffer to ~256 MB even at d=4096."""
    if num_edges <= 0:
        return 1
    row_elems = 1
    for dim_s in feat_shape:
        row_elems *= max(1, int(dim_s))
    bytes_per_edge = max(4, row_elems * max(2, elem_size))
    target_bytes = 256 * 1024 * 1024  # 256 MiB max transient chunk
    return max(16_384, min(num_edges, target_bytes // bytes_per_edge))


class _ScatterAddFn(torch.autograd.Function):
    @staticmethod
    def forward(ctx, x, u, v, e, num_dst) -> torch.Tensor:
        ctx.num_src, ctx.orig_dtype, ctx.has_e = x.shape[0], x.dtype, (e is not None)
        if e is not None:
            ctx.e_orig_dtype = e.dtype
            ctx.save_for_backward(u, v, x, e)
        else:
            ctx.save_for_backward(u, v)  # ZERO O(|E|*d) saved autograd tensors!

        work_dtype = (
            torch.float32 if x.dtype in (torch.float16, torch.bfloat16) else x.dtype
        )
        x_w = x.to(work_dtype) if x.dtype != work_dtype else x
        e_w = e.to(work_dtype) if (e is not None and e.dtype != work_dtype) else e
        out = torch.zeros((num_dst, *x.shape[1:]), dtype=work_dtype, device=x.device)

        num_edges = int(u.shape[0])
        cs = _adaptive_edge_chunk(num_edges, tuple(x.shape[1:]), out.element_size())
        for start in range(0, num_edges, cs):
            end = min(start + cs, num_edges)
            msg = x_w.index_select(0, u[start:end])
            if e_w is not None:
                msg = msg * e_w[start:end]
            out.index_add_(0, v[start:end], msg)
        return out.to(x.dtype) if out.dtype != x.dtype else out

    @staticmethod
    def backward(ctx, grad_out: torch.Tensor):
        saved = ctx.saved_tensors
        u, v = saved[0], saved[1]
        x, e = (saved[2], saved[3]) if ctx.has_e else (None, None)
        work_dtype = (
            torch.float32
            if grad_out.dtype in (torch.float16, torch.bfloat16)
            else grad_out.dtype
        )
        go_w = grad_out.to(work_dtype) if grad_out.dtype != work_dtype else grad_out
        num_edges = int(u.shape[0])
        cs = _adaptive_edge_chunk(num_edges, tuple(grad_out.shape[1:]), go_w.element_size())

        grad_x_w = torch.zeros(
            (ctx.num_src, *grad_out.shape[1:]), dtype=work_dtype, device=grad_out.device
        )
        e_w = e.to(work_dtype) if (e is not None and e.dtype != work_dtype) else e
        for start in range(0, num_edges, cs):
            end = min(start + cs, num_edges)
            gm = go_w.index_select(0, v[start:end])
            if e_w is not None:
                gm = gm * e_w[start:end]
            grad_x_w.index_add_(0, u[start:end], gm)
        grad_x = grad_x_w.to(ctx.orig_dtype) if grad_x_w.dtype != ctx.orig_dtype else grad_x_w
        # ... (chunked inner-product grad_e when ctx.has_e is True) ...
        return grad_x, None, None, None, None
```

* **Why this kernel mattered specifically for `AMG 691`**: `AMG 691` sits inside a dense immunological subnetwork (`TNFRSF8`, `TNFSF8`, `TRAF2`, `NFKB1`, `TSLP`, `TNFRSF4`, `IL23A`) where scaling width from $d = 2048$ to $d = 3072$ (`944.2M` parameters, `v5bi_d3072`) lifted `Severe Asthma` from $Z = +3.32 \to +4.76$ and `Ulcerative Colitis` from $Z = +3.82 \to +4.16$. At $d = 3072$, native `DGL` edge buffers exceed `72 GB` on `5.91M` edges. By saving only `(u, v)` on the autograd tape (`L161`), bounding `msg` slices to `256 MiB` (`cs = 21,845` edges, `L134`), and accumulating `index_add_` in `torch.float32` (`L164–L179`) before casting back to `bf16`, `dgl_compat.py` fits full-graph `v5bi_d3072` training comfortably inside a single `96 GB` GPU with exact adjoint gradients (`L206–L212`).

---

### 4.2 Module B: Sequential Gauge-Invariant `24`-Checkpoint Subbank Distiller (`extract_amg691_24ckpt_subbank.py`)

```python
# extract_amg691_24ckpt_subbank.py (Lines 472-604)
            with torch.no_grad():
                layers = model.encode_all_layers(G_dev)
                h1_gene = layers["h1"]["gene/protein"].to(device, dtype=torch.float32)
                h2_drug = layers["h2"]["drug"].to(device, dtype=torch.float32)
                h2_dis = layers["h2"]["disease"].to(device, dtype=torch.float32)
                del layers

                rel_name = (
                    "rev_drug_binds_protein"
                    if "rev_drug_binds_protein" in model.layer2.weight
                    else "rev_drug_protein"
                )
                proj_gene = model.layer2.weight[rel_name](h1_gene)
                act_mask = torch.from_numpy(deg >= 3).to(device)
                med_norm = float(torch.median(torch.norm(h2_drug[act_mask], dim=1)).item())

                sim = (
                    model.pred.extract_disease_similarity_details(G_dev, h2_dis, view="eval")
                    if getattr(model.pred, "proto", False) else {}
                )
                aug_ind = sim["indication"]["augmented_disease_emb"].float() if "indication" in sim else h2_dis
                w_ind_f = model.pred.W[model.pred.rel2idx[("drug", "indication", "disease")]].unsqueeze(0)
                w_ind_r = model.pred.W[model.pred.rel2idx[("disease", "rev_indication", "drug")]].unsqueeze(0)
                eff_ind = 0.5 * (h2_dis * w_ind_f + aug_ind * w_ind_r)      # (18025, dim)

                aug_con = sim["contraindication"]["augmented_disease_emb"].float() if "contraindication" in sim else h2_dis
                w_con_f = model.pred.W[model.pred.rel2idx[("drug", "contraindication", "disease")]].unsqueeze(0)
                w_con_r = model.pred.W[model.pred.rel2idx[("disease", "rev_contraindication", "drug")]].unsqueeze(0)
                eff_con = 0.5 * (h2_dis * w_con_f + aug_con * w_con_r)      # (18025, dim)

                # Chunked background (mu_d, sd_d) across all 18,640 KG drugs
                mu_arr, sd_arr = np.zeros(D_all, np.float32), np.ones(D_all, np.float32)
                for st in range(0, D_all, 3000):
                    ed = min(D_all, st + 3000)
                    blk = (h2_drug @ eff_ind[st:ed].T).cpu().numpy()
                    _, mu_b, sd_b = standardize_logits_per_disease(blk, known_pos_mask=known[:, st:ed])
                    mu_arr[st:ed], sd_arr[st:ed] = mu_b, np.maximum(sd_b, 1e-5)

                # Synthesize 4 AMG 691 mode vectors + hybrid cold/warm partner vectors
                h_modes = [
                    _build_mode_projection(proj_gene, gene_sym2i, ppi_nbrs, mode_defs[m], med_norm)
                    for m in ("dual_cd30_cd30l", "pure_cd30", "pure_cd30l")
                ] + [h_ira]
                H_M = torch.stack(h_modes, dim=0)                           # (4, dim)
                H_M_unit = H_M / torch.clamp(torch.norm(H_M, dim=1, keepdim=True), min=1e-6)

                h_partners = []
                for p in partner_meta_resolved:
                    pu, p_deg = int(p["u_idx"]), int(deg[int(p["u_idx"])])
                    t_weights = [(ts, 1.0) for ts in p["targets"] if ts in gene_sym2i]
                    if p_deg >= 3 or not t_weights:
                        hp = h2_drug[pu]
                    elif p_deg == 0:
                        hp_cold = _build_mode_projection(proj_gene, gene_sym2i, ppi_nbrs, t_weights, med_norm)
                        hp_trans = h2_drug[pu] * (med_norm / torch.clamp(torch.norm(h2_drug[pu]), min=1e-6))
                        hp = 0.35 * hp_trans + 0.65 * hp_cold
                        hp = hp * (med_norm / torch.clamp(torch.norm(hp), min=1e-6))
                    else:
                        hp_cold = _build_mode_projection(proj_gene, gene_sym2i, ppi_nbrs, t_weights, med_norm)
                        hp = 0.70 * h2_drug[pu] + 0.30 * hp_cold
                    h_partners.append(hp)
                H_P = torch.stack(h_partners, dim=0)                        # (62, dim)
                H_P_unit = H_P / torch.clamp(torch.norm(H_P, dim=1, keepdim=True), min=1e-6)

                # Extract 8 gauge-invariant relational scalars BEFORE freeing checkpoint c!
                cos_AB = (H_M_unit @ H_P_unit.T).cpu().numpy()              # (4, 62)
                cos_AD = (H_M_unit @ D15_unit.T).cpu().numpy()              # (4, 15)
                cos_BD = (H_P_unit @ D15_unit.T).cpu().numpy()              # (62, 15)
                l2_AB = torch.cdist(H_M_unit, H_P_unit).cpu().numpy()       # (4, 62)
                for mi in range(N_modes):
                    for di in range(N_d15):
                        rel_24[ck_counter, mi, di, :, 0] = cos_AB[mi]
                        rel_24[ck_counter, mi, di, :, 1] = cos_AD[mi, di]
                        rel_24[ck_counter, mi, di, :, 2] = cos_BD[:, di]
                        rel_24[ck_counter, mi, di, :, 3] = l2_AB[mi]
                        rel_24[ck_counter, mi, di, :, 4] = float(z_m[mi, canon_dis_indices[di]])
                        rel_24[ck_counter, mi, di, :, 5] = z_p_ind15[:, di]
                        rel_24[ck_counter, mi, di, :, 6] = p_p_ind15[:, di]
                        rel_24[ck_counter, mi, di, :, 7] = p_p_con15[:, di]
            del trainer, model, G_dev, h1_gene, h2_drug, h2_dis, proj_gene, eff_ind, eff_con
            gc.collect(); torch.cuda.empty_cache()
```

* **`L479–L483` (`v4` vs. `v5bi` relation-key awareness + `med_norm` anchoring)**: `v5bi` checkpoints split binding from inhibition/activation (`rev_drug_binds_protein`), whereas `v4` uses `rev_drug_protein`. Selecting `rel_name` dynamically projects Layer-1 gene features `h1_gene` through the exact trained Layer-2 drug-target operator of checkpoint $c$, scaled to `med_norm` of clinically well-connected drugs (`deg >= 3`) so zero-indication biologics (`AMG 691`) sit on the exact calibrated DistMult hypersphere.
* **`L490–L509` (Bidirectional SimGNN efficacy & contraindication decoders + `3,000`-disease chunked $Z$-null)**: Combines forward `drug -> disease` and prototype-augmented `disease -> drug` DistMult weights (`0.5 * (h2_dis * w_f + aug * w_r)`) for **both** `indication` (`eff_ind`) and `contraindication` (`eff_con`), and standardizes every disease column over `18,640` background drugs with `known_pos_mask` excluded so true approved indications do not inflate $\sigma_{c,d}$.
* **`L528–L542` (Degree-adaptive hybrid projection for investigative I&I partners)**: Approved anchors (`Dupilumab`, `Tezepelumab`, `deg >= 3`) retain their empirical transductive `h2_drug[pu]`.Investigational Phase 2/3 biologics with `deg == 0` in historical FDA labels (`Rocatinlimab`, `Amlitelimab`, `Duvakitug`, `Ordesekimab`) blend `0.35 * hp_trans + 0.65 * hp_cold` re-normalized to `med_norm`, preventing sparse-degree shrinkage from penalizing next-generation mechanisms!
* **`L568–L603` (Orthogonal gauge invariance & sequential `O(1)` VRAM execution)**: Two checkpoints $c_1$ ($d=2048$) and $c_2$ ($d=3072$) have incompatible vector spaces and arbitrary orthogonal gauge rotations $\mathbf{Q}_c \in O(d_c)$ ($\langle \mathbf{Q}_c \mathbf{a}, \mathbf{Q}_c \mathbf{b}\rangle = \langle \mathbf{a}, \mathbf{b}\rangle$). Computing all `8` inner-product and $Z$-standardized channels (`cos_AB`, `cos_AD`, `cos_BD`, `l2_AB`, `z_ind_A`, `z_ind_B`, `p_ind_B`, `p_con_B`) **inside** checkpoint $c$'s loop and immediately calling `del ...; torch.cuda.empty_cache()` keeps peak GPU memory at `1` checkpoint while yielding a mathematically gauge-invariant `(24, 4, 15, 62, 8)` tensor (`3.58 MB`).

---

### 4.3 Module C: `6`-Pillar Combination Scorer, `10,000`-Draw `Dirichlet` Engine & `2026` Clinical Calibrator (`optimus_txgnn/amg691_v2_combination_engine.py`)

#### 4.3.1 Benign I&I Hygiene Gate (`L177–L204`) & Cognate Receptor–Ligand `L2G` Propagator (`L598–L640`)

```python
# optimus_txgnn/amg691_v2_combination_engine.py (Lines 177-204 & 598-640)
def is_partner_hygienic_for_disease(
    partner_name: str, partner_targets: Sequence[str],
    is_benign_ii: bool, partner_key: str = "",
) -> Tuple[bool, str]:
    tset = {t.upper().strip() for t in partner_targets if t}
    if "TNFRSF8" in tset or "TNFSF8" in tset:
        if is_benign_ii:
            return False, "Rejected: Same-axis CD30/CD30L redundancy (Tier-4 overlap)"
        if "TUBB" in tset:
            return False, "Rejected: Direct CD30 epitope competition with Brentuximab vedotin"
        return False, "Rejected: Same-epitope anti-CD30 redundancy"
    if not is_benign_ii:
        return True, "Allowed (Hematologic / EBV Benchmark)"
    if BENIGN_II_FORBIDDEN_DRUG_PAT.search(partner_name) or BENIGN_II_FORBIDDEN_DRUG_PAT.search(partner_key):
        return False, "Blocked by Benign I&I Modality Hygiene Gate"
    bad_t = sorted(tset & BENIGN_II_FORBIDDEN_TARGETS)
    if bad_t:
        return False, "Blocked by Benign I&I Target Gate (" + ",".join(bad_t) + ")"
    return True, "Pass (Chronic I&I Compatible)"


    COGNATE_LR_PAIRS: Dict[str, Tuple[str, ...]] = {
        "TNFRSF4": ("TNFSF4",), "TNFSF4": ("TNFRSF4",),
        "TNFSF15": ("TNFRSF25",), "TNFRSF25": ("TNFSF15",),
        "TSLP": ("CRLF2", "IL7R"), "IL33": ("IL1RL1",), "IL1RL1": ("IL33",),
        "IL5RA": ("IL5",), "IL13": ("IL4R", "IL13RA1"),
    }

    def _gene_set_l2g_on_disease(self, syms: Sequence[str], dis_id: str):
        di = self.d2i.get(dis_id)
        if di is None:
            return 0.0, 0.0, []
        direct_sum, n1_set, seed_gis = 0.0, set(), set()
        for s in syms:
            su = s.upper()
            gi = self.sym2g.get(su)
            if gi is not None:
                seed_gis.add(gi)
                direct_sum += self.l2g_by_dg.get((di, gi), 0.0)
                n1_set.update(int(nb) for nb in self.ppi.get(gi, [])[:35] if nb != gi)
            for cog in self.COGNATE_LR_PAIRS.get(su, ()):
                cgi = self.sym2g.get(cog)
                if cgi is not None:
                    direct_sum += 0.75 * self.l2g_by_dg.get((di, cgi), 0.0)
                    seed_gis.add(cgi)
        n1_set -= seed_gis
        ppi_hits = [
            (self.l2g_by_dg.get((di, nb), 0.0), str(self.pack["gene_syms"][nb]))
            for nb in n1_set if self.l2g_by_dg.get((di, nb), 0.0) > 0.08
        ]
        ppi_hits.sort(key=lambda x: -x[0])
        ppi_sum = sum(w for w, _ in ppi_hits)
        return min(1.0, direct_sum), min(1.5, direct_sum + 0.30 * ppi_sum), [s for _, s in ppi_hits[:5]]
```

* **`L187–L203` (Two-tier pharmacological hygiene)**: First blocks stoichiometric epitope collision on `TNFRSF8`/`TNFSF8` (`Iratumumab` and `MMAE`-payload `Brentuximab vedotin`); second blocks oncology cytotoxics (`TUBB`, `TOP1/2A`), `CD3E` T-cell engagers, and checkpoint agonists (`PDCD1`, `CTLA4`) on all `12` benign I&I indications while keeping them available as comparators on the `3` `CD30+` lymphoid benchmarks (`Hodgkin`, `ALCL`).
* **`L598–L640` (Solving GWAS ligand–receptor split attribution)**: Human GWAS loci often map to a cytokine's receptor (`IL1RL1` in Asthma, `IL4R` in AD) while the biologic binds the soluble ligand (`IL33`, `IL13`), or vice versa (`Rocatinlimab` binds `TNFRSF4` while `9q33/1q25` GWAS peaks hit `TNFSF8`/`TNFSF4`). `COGNATE_LR_PAIRS` (`0.75x` credit) plus `n1_set -= seed_gis` (`L631`, eliminating seed/cognate double-counting before `0.30x` `PPI` propagation) ensures marginal `Open Targets L2G` rescue reflects true causal pathway coverage.

#### 4.3.2 Vectorized `6`-Pillar Scoring & `10,000`-Draw `Dirichlet` Monte Carlo (`L753–L964`)

```python
# optimus_txgnn/amg691_v2_combination_engine.py (Lines 753-964, core scoring kernel)
        rng = np.random.default_rng(seed)
        alpha = np.array([NOMINAL_AMG691_V2_WEIGHTS[k] * 35.0 for k in w_keys], dtype=np.float64)
        W_draws = rng.dirichlet(alpha, size=n_dirichlet_draws).astype(np.float32)  # (10000, 6)
        w_nom = np.array([NOMINAL_AMG691_V2_WEIGHTS[k] for k in w_keys], dtype=np.float32)

        # Inside (mode mi, disease di, partner pi) loop:
        r_ck = self.rel_24[:, mi, di, pi, :]                                       # (24, 8)
        cos_ab, cos_ad, cos_bd = float(r_ck[:, 0].mean()), float(r_ck[:, 1].mean()), float(r_ck[:, 2].mean())
        z_a, z_b, p_con_b = float(r_ck[:, 4].mean()), float(r_ck[:, 5].mean()), float(r_ck[:, 7].mean())
        fam_pos_frac = float((r_ck[:, 5] > 0.5).mean())

        # Pillar 1 (G_gnn24): Sweet-spot cos_AB = 0.38 bell curve + contraindication discount
        u_za = float(1.0 / (1.0 + np.exp(-0.55 * (z_a - 0.8))))
        u_zb = float(1.0 / (1.0 + np.exp(-0.55 * (z_b - 1.0))))
        u_rel = max(0.05, 1.0 - 4.0 * (cos_ab - 0.85)) if cos_ab > 0.88 else float(np.exp(-((cos_ab - 0.38) ** 2) / 0.22))
        u_align = float(np.clip(0.5 + 0.8 * (0.5 * cos_ad + 0.5 * cos_bd), 0.0, 1.0))
        g_gnn24 = float(np.clip(
            (0.24 * u_za + 0.38 * u_zb + 0.22 * u_rel + 0.16 * fam_pos_frac * u_align) * (1.0 - 0.45 * p_con_b),
            0.0, 1.0,
        ))

        # Pillar 2 (H_genetics): Marginal L2G gain + Reactome orthogonality + shared exposure
        _, union_ppi_l2g, rescued_genes = self._gene_set_l2g_on_disease(list(a_syms) + p_targets, did)
        marginal_l2g = max(0.0, union_ppi_l2g - a_ppi_l2g)
        h_gen = float(np.clip(
            0.48 * (1.0 - np.exp(-2.1 * marginal_l2g)) + 0.24 * (1.0 - np.exp(-1.8 * a_ppi_l2g))
            + 0.16 * (1.0 - pwy_jaccard) + 0.12 * min(1.0, exp_shared), 0.0, 1.0
        ))

        # Pillars 3-6: Circuit C_circ, Biopsy/scRNA B_biopsy, Clinical E_clin, Safety S_safe
        _, c_circ, _ = classify_amg691_circuit_archetype(d_key, drec["franchise"], p_key, p_targets, m_key)
        e_clin = self._compute_organ_clinical_2026_score(d_key, p_key, p_targets, m_key)
        # ... [OnSIDES infection terms + TB/neutropenia flags + black-box floors -> s_safe] ...

        pillars = [g_gnn24, h_gen, c_circ, b_biopsy, e_clin, s_safe]
        if not hyg_ok:
            pillars = [p * 0.05 for p in pillars]                                  # Hard 0.05x quarantine
        pillar_mat_list.append(pillars)

        # Vectorized BLAS evaluation across all 62 partners x 10,000 Dirichlet draws
        P_mat = np.array(pillar_mat_list, dtype=np.float32)                        # (62, 6)
        nom_scores = P_mat @ w_nom                                                 # (62,)
        draw_scores = P_mat @ W_draws.T                                            # (62, 10000)
        p10, p90 = np.percentile(draw_scores, 10, axis=1), np.percentile(draw_scores, 90, axis=1)
        top3_freq = np.zeros(len(raw_candidates), dtype=np.float32)
        top3_idx = np.argpartition(-draw_scores, 3, axis=0)[:3, :]                 # O(N_p) top-3 select!
        for row_top in top3_idx:
            np.add.at(top3_freq, row_top, 1.0 / n_dirichlet_draws)
```

* **`L755–L757` (Shared `Dirichlet` design matrix `W_draws`)**: Drawing `W_draws` `(10000, 6)` once with `np.random.default_rng(42)` at $\kappa = 35.0$ ($\boldsymbol{\alpha} = 35 \cdot [0.22, 0.18, 0.20, 0.18, 0.12, 0.10]$) perturbs every pillar weight across a realistic $\pm 35\%\text{–}45\%$ governance range while ensuring every `(mode, disease)` slice is compared under identical weight perturbations.
* **`L801–L809` (The $\overline{\cos}_{AB} = 0.38$ complementarity sweet spot)**: Pairs with $\overline{\cos}_{AB} \approx 0$ sit in unrelated disease biology (e.g., cardiovascular/metabolic drugs), whereas $\overline{\cos}_{AB} > 0.88$ indicates redundant clones on the exact same target axis (`Iratumumab`). The Gaussian peak at `0.38` rewards partners sharing the same mucosal inflammatory neighborhood via an **orthogonal** receptor family (`Tezepelumab` `TSLP`, `Rocatinlimab` `OX40`, `Risankizumab` `IL-23p19`).
* **`L945–L954` (Single-`SGEMM` + `np.argpartition` $\mathcal{O}(N_p)$ ranking)**: Instead of running `10,000` Python loops and full `O(N_p log N_p)` sorts across `45` `(mode, disease)` slices (`450,000` sorts), `P_mat @ W_draws.T` evaluates all `620,000` combinations per slice in one `L2`-cache-resident `fp32` matrix multiply, and `np.argpartition(-draw_scores, 3, axis=0)[:3, :]` extracts exact Top-3 membership in linear time—completing all `27,900,000` Monte Carlo evaluations in `< 0.4 s` on CPU.

#### 4.3.3 Organ-Resolved `2025–2026` Clinical Readout Calibrator (`L968–L1042`)

```python
# optimus_txgnn/amg691_v2_combination_engine.py (Lines 968-1042, abridged)
    def _compute_organ_clinical_2026_score(
        self, disease_key: str, partner_key: str,
        partner_targets: Sequence[str], mode_key: str,
    ) -> float:
        tset = {t.upper() for t in partner_targets}
        if partner_key in ("anakinra", "abatacept", "tofacitinib"):
            return 0.08  # Combined biologic black-box / MACE-infection warnings
        if disease_key in ("asthma", "crswnp", "copd"):
            if "TSLP" in tset: return 0.97              # Tezepelumab WAYPOINT / Lunsekimig Airway
            if tset & {"IL4R", "IL13"}: return 0.90 if disease_key != "copd" else 0.84
            if tset & {"TNFRSF4", "TNFSF4"}: return 0.88 # Croft Lab CD30L+OX40L TIDE-Asthma rescue
        if disease_key in ("ad", "pn"):
            if tset & {"IL4R", "IL13"}: return 0.95      # Dupilumab / Tilrekimig skin anchor
            if tset & {"TNFRSF4", "TNFSF4"}: return 0.94 # Rocatinlimab ROCKET Phase 3 skin anchor
            if "TSLP" in tset: return 0.56               # Honest Lunsekimig VELVET Ph2b AD miss!
        if disease_key in ("uc", "crohns"):
            if "IL17A" in tset or "IL17F" in tset: return 0.06  # Paradoxical IBD flare guardrail
            if partner_key == "etanercept": return 0.18         # Soluble TNFR2-Fc failed Crohn's RCT
            cd30l_boost = 0.03 if mode_key in ("dual_cd30_cd30l", "pure_cd30l") else -0.03
            if tset & {"IL23A", "IL12B", "TNFSF15"}: return min(0.98, 0.94 + cd30l_boost)
        if disease_key == "celiac" and "IL15" in tset:
            return 0.95                                  # Ordesekimab (AMG 714) Ph2b Celiac anchor
        return 0.48
```

* **Architectural separation for non-circular `LOAO` proof**: Notice why `2025–2026` human clinical trial calibration is isolated strictly inside Pillar 5 (`e_clin`, weight `0.12`) rather than mixed into Pillars `1–4` (`GNN`, `Open Targets L2G`, `Circuit`, `Biopsy/scRNA`). First, it enforces **organ-resolved honesty** (`TSLP` receives `0.97` in airway `Asthma/CRSwNP` where `WAYPOINT` & `Lunsekimig` succeeded, but drops to `0.56` in cutaneous `AD` after `VELVET`, while anti-`IL-17` drops to `0.06` in `IBD`). Second, isolating `e_clin` lets `validate_2026_clinical_calibration_non_circular()` zero out Pillar 5 (`w_5 = 0`, re-normalizing Pillars `1, 2, 3, 4, 6` to sum to `1.0`) and prove `AUROC = 1.000` across all `14` clinical anchors from **pure pre-readout graph + multi-omic biology alone**.

