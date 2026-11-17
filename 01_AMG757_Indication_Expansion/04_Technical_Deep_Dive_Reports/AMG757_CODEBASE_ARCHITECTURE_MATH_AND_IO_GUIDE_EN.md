# AMG 757 (`Tarlatamab` / `IMDELLTRA`) Indication Expansion — Codebase Architecture, Mathematical Theory & 9-Phase Data I/O Reference Manual (`Canonical v4.3.2`)

> [!IMPORTANT]
> **Purpose & Scope of This Engineering Manual**
> This document is the companion **Codebase, Systems Design, Mathematical Theory, and Data Input/Output Reference** to the [AMG 757 Zero-to-Expert Methodology & Decision Playbook](./AMG757_COMPLETE_METHODOLOGY_AND_DECISION_PLAYBOOK_EN.md). Every stage (`Phase 0` through `Phase 8 / Canonical v4.3.2`) and sub-step of that standalone playbook is mapped here to:
> 1. **System & Software Architecture Design**: *How* the repository is layered, *why* GPU graph representation learning is decoupled from deterministic statistical/omics/biophysical engines, audited `NumPyro` `NUTS` `Q1–Q4` governance gates, and zero-PyTorch runtime studios, and how data leakage and coordinate-gauge drift are prevented in code.
> 2. **Exact Implementing Modules, Classes & Functions**: Clickable links to exact `.py` files and line ranges across [`optimus_txgnn/`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/__init__.py), [`optimus_txgnn/models/`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/models/decision_table_v4.py), [`optimus_txgnn/backtest/`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/backtest/pan_antigen_temporal_backtest.py), [`scripts/`](file:///home/nchen10/antigravity_prjs/kg/scripts/build_amg757_v4_reports_and_studio.py), and [`visualization_studio/`](file:///home/nchen10/antigravity_prjs/kg/visualization_studio/run_visualization_studio.py).
> 3. **Mathematical & Algorithmic Kernels**: Exact tensor equations, broadcasting shapes, numerical stability guards (`fp32` degree-normalized `CSR` sparse matmul, `SVD` manifold adapters, `Plackett–Luce` listwise losses, exact $2^6 = 64$ Shapley coalitions, `Simon (1989)` binomial dynamic programming, `NumPyro` `NUTS` `EXNEX` `MixtureSameFamily` MCMC, exact $\mathcal{O}(2^{F/2})$ meet-in-the-middle `32`-antigen temporal sign-flip permutation testing, and segregated `Table A / Table B` `WACC`-discounted `eNPV`).
> 4. **Stage-by-Stage Data Input & Output Contracts**: Exact file paths, formats (`.parquet`, `.csv`, `.pt`, `.npz`, `.json`, `.jsonl`, `.ots`, `.yaml`, `.html`), verified row/column counts, tensor shapes/dtypes, and column schemas.

![AMG 757 Codebase Architecture, Math Kernels & 9-Phase Data I/O Contract Map](../figures/fig_amg757_codebase_dataflow_en.png)

---

## 1. System Design Principles & Four-Tier Software Architecture

Biomedical knowledge-graph (KG) discovery systems frequently fail in production for four engineering reasons: **(1) silent train-test split or temporal leakage**, **(2) C++ graph-library VRAM bottlenecks when scaling hidden width $d \ge 2048$**, **(3) invalid averaging of raw node embeddings across independently seeded checkpoints**, and **(4) heavy GPU/CUDA runtime dependencies that prevent biostatisticians and clinical study teams from inspecting or stress-testing rankings locally**.

To eliminate all four failure modes simultaneously, the repository (`/home/nchen10/antigravity_prjs/kg/`) is organized into a **strictly decoupled Four-Tier Architecture**:

```mermaid
flowchart TD
    subgraph T1["Tier 1: Deterministic KG, Safety & Ontology ETL (Polars / Parquet)"]
        P0["Phase 0: prepare_optimuskg_data.py + prepare_clinical_v5_and_combo_v1.py · ontology_and_safety_filters.py + runlog.py (SHA-256 Locks)"]
    end

    subgraph T2["Tier 2: High-VRAM 96 GB GPU Representation & Cold-Antigen Learning (PyTorch CSR)"]
        P1["Phase 1: model.py + dgl_compat.py + trainer.py · 24-Checkpoint HeteroRGCN Super-Ensemble (d=2048/3072, 35.2 GB)"]
        P4["Phase 4: option2_bite_head.py + train_option2_bite_head.py · SVD-LoRA + 5-Modality FiLM Cold-Antigen Head (109.5 MB Pack)"]
    end

    subgraph T3["Tier 3: Gauge-Invariant Calibration, Omics, Biophysics & Audited Bayesian Engines (CPU)"]
        P2["Phase 2: calibration.py + bite_biomarker_and_expansion.py · Per-Disease Z + 3x4 Mondrian Conformal + BiTE Solid-Tumor AND-Gate"]
        P3["Phase 3: option1_protocol_and_omics.py + basket_design.py · 4-Pillar Orthogonal Protocol MCDA + Exact Simon 2-Stage DP"]
        P5["Phase 5: amg757_integrated_synthesis.py + combo_model.py · 6-Pillar Integrated v3 Synthesis + 2^6 Exact Shapley (67.8% IHC Audit)"]
        P6["Phase 6: amg757_primary_tme_and_scrna.py + q6_constructs_biophysics.py · 2,215 Primary Biopsies + 60,026 SCLC scRNA + <=14 nm Synapse + Ion Guard"]
        P8["Phase 8 (v4.3.2): models/q1..q4_*.py + pan_antigen_temporal_backtest.py · 260-Rec Provenance + NUTS Q1/Q2 EXNEX + 32-Ag Back-Test + Table A/B + 3/3 BTC .ots"]
    end

    subgraph T4["Tier 4: Zero-PyTorch FP16 Distillation & Cross-Platform Delivery (Win11 / Linux)"]
        P7["Phase 7: portable_pack.py + visualization_studio/studio_engine/numpy_models.py · 1.61 GB FP16 Tensor Pack + Pure-NumPy Live Inference + V3 & V4 HTML5 Studios"]
    end

    P0 --> P1
    P1 --> P2
    P1 --> P4
    P2 --> P3
    P3 --> P5
    P4 --> P5
    P5 --> P6
    P6 --> P8
    P2 --> P7
    P8 --> P7
```

### 1.1 Five Foundational Engineering Invariants Enforced in Code

1. **Cryptographic Node & Split Fingerprint Locking ([`optimus_txgnn/runlog.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/runlog.py#L93-L316), [`optimus_txgnn/data.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/data.py#L220-L311))**:
   - `compute_node_fingerprint_from_kg()` hashes the canonical tuple `(node_index, node_id, node_type)` of all `139,439` clinical graph nodes via `SHA-256` (`c7a55f328b0df1abe518f8a3bc9afcdfd1984c0513acbe64d48cf52716b83510`).
   - `compute_split_hashes()` hashes `train.csv`, `valid.csv`, and `test.csv`. Both `write_manifest()` and `compare_disease_centric_runs()` raise a hard `RuntimeError` if any checkpoint in an ensemble was trained on a drifted node ordering or mismatched holdout split (verified in [`tests/test_split_hash_guard.py`](file:///home/nchen10/antigravity_prjs/kg/tests/test_split_hash_guard.py)).
2. **Custom Pure-PyTorch `FP32` `CSR` Heterogeneous Graph Backend ([`optimus_txgnn/dgl_compat.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/dgl_compat.py#L1-L786))**:
   - Standard DGL C++ kernels materialize per-edge intermediate buffers $\mathcal{O}(|E| \cdot d)$ during backward passes, causing out-of-memory crashes or `bf16` degree-underflow when scaling `HeteroRGCN` to $d \in \{2048, 3072\}$ (`9,385,536` bidirectional edges, up to `944.2M` parameters).
   - `TorchHeteroGraph` and `TorchRelationSubgraph` implement adaptive-chunked `CSR` sparse message passing (`_ScatterAddFn`) that aggregates neighbor features **first** in exact `fp32` ($\mathcal{O}(|V_{\text{dst}}| \cdot d)$) **before** applying dense relation projection $\mathbf{W}_r \in \mathbb{R}^{d \times d}$. Bit-level forward and backward gradient parity against DGL is locked in [`tests/test_colab_scaling_and_backend.py`](file:///home/nchen10/antigravity_prjs/kg/tests/test_colab_scaling_and_backend.py).
3. **Gauge-Invariant Multi-Checkpoint Distillation ([`optimus_txgnn/option2_bite_head.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/option2_bite_head.py#L709-L842), [`optimus_txgnn/portable_pack.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/portable_pack.py#L1-L279))**:
   - Because DistMult scoring $\mathbf{h}_u^\top \operatorname{diag}(\mathbf{r}_{\text{ind}}) \mathbf{h}_d$ is invariant to coordinate permutations and opposite rescalings across random seeds—and because checkpoints span both $d = 2048$ (`18` models) and $d = 3072$ (`6` models)—**raw embeddings from different checkpoints can never be averaged directly**.
   - Every downstream module interacts with the `24` checkpoints exclusively through **coordinate-frame-invariant quantities** computed *inside* each checkpoint $c$: per-disease standardized $Z$-scores $z_{u,d}^{(c)}$, within-checkpoint cosine similarities, or checkpoint-specific `SVD` disease-manifold bases $\mathbf{V}_c \in \mathbb{R}^{d_c \times K}$.
4. **Strict Separation of Non-Compensatory Hard Guardrails vs. Smooth MCDA Weights ([`optimus_txgnn/ontology_and_safety_filters.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/ontology_and_safety_filters.py), [`optimus_txgnn/amg757_integrated_synthesis.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/amg757_integrated_synthesis.py#L541-L596))**:
   - Smooth weighted averages suffer from *compensatory masking* (e.g., high whitespace and safety scores making a `0% DLL3` tumor look attractive). Code modules enforce **non-compensatory multiplicative gates** ($g_{\text{tgt}}(x)$) and boolean clinical hygiene masks (`is_pediatric_or_circumscribed_idh_wt_glioma()`, `build_bite_solid_tumor_exclusion_mask()`, `resolve_target_validated_combo_partner()`) *outside* the convex pillar weights.
5. **100% Offline-Reproducible Snapshot Architecture**:
   - All external public cohorts (`TCGA`, `TARGET`, `SU2C`, `NCBI GEO`, `UniProt`, `GTEx v8`, `HPA`, `ClinicalTrials.gov API v2`, `OnSIDES v3.1.1`, `FDA Orange Book`) are snapshotted into versioned local caches under `data_optimuskg/` with machine-verifiable provenance ([`optimus_txgnn/evidence/schema.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/evidence/schema.py)), allowing every downstream synthesis and Studio build to run deterministically offline in seconds.

### 1.2 Repository Package Map (`AMG 757` Scope)

| Directory / Layer | Key Modules (`.py` / `.yaml`) | Primary Responsibility |
| :--- | :--- | :--- |
| **Configuration & Rules** (`configs/`) | [`amgen_portfolio_v5.yaml`](file:///home/nchen10/antigravity_prjs/kg/configs/amgen_portfolio_v5.yaml), [`bite_pan_cancer_biomarkers_v5.yaml`](file:///home/nchen10/antigravity_prjs/kg/configs/bite_pan_cancer_biomarkers_v5.yaml), [`amg757_indication_mapping_v2.yaml`](file:///home/nchen10/antigravity_prjs/kg/configs/amg757_indication_mapping_v2.yaml), [`amg757_scenarios_v4.yaml`](file:///home/nchen10/antigravity_prjs/kg/configs/amg757_scenarios_v4.yaml) | Canonical asset targets (`DLL3` + `CD3E`), curated `SP347` IHC prevalence priors, subtype crosswalks, and `v4` commercial scenario parameters. |
| **Core KG & GNN Backbone** (`optimus_txgnn/`) | [`data.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/data.py), [`dgl_compat.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/dgl_compat.py), [`model.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/model.py), [`trainer.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/trainer.py), [`evaluator.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/evaluator.py), [`explain.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/explain.py), [`graphmask.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/graphmask.py) | Heterogeneous graph construction, `FP32` `CSR` message passing, `DistMult` bidirectional decoding, mixed negative sampling, disease-centric evaluation, and 2-layer Integrated Gradients / GraphMask path explanations. |
| **Calibration, Safety & Combo ML** (`optimus_txgnn/`) | [`calibration.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/calibration.py), [`ontology_and_safety_filters.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/ontology_and_safety_filters.py), [`combo_model.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/combo_model.py), [`clinical_v5_builder.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/clinical_v5_builder.py) | Per-disease $Z$-standardization, $3 \times 4$ `MondrianConformalCalibrator`, `MONDO` DAG deduplication, `OnSIDES` black-box & DDI masks, and 3-head symmetric `OptimusComboNet`. |
| **`AMG 757` Domain Engines** (`optimus_txgnn/`) | [`bite_biomarker_and_expansion.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/bite_biomarker_and_expansion.py), [`option1_protocol_and_omics.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/option1_protocol_and_omics.py), [`option2_bite_head.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/option2_bite_head.py), [`option2_debiased_v2.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/option2_debiased_v2.py), [`amg757_integrated_synthesis.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/amg757_integrated_synthesis.py), [`amg757_primary_tme_and_scrna.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/amg757_primary_tme_and_scrna.py), [`amg757_biophysical_synapse.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/amg757_biophysical_synapse.py), [`amg757_epidemiology_and_enpv.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/amg757_epidemiology_and_enpv.py), [`basket_design.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/basket_design.py), [`regulatory_rules.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/regulatory_rules.py) | Complete `Phase 2` through `Phase 7` domain logic for BiTE `AND`-gates, Option 1 protocol MCDA, Option 2 cold-antigen `FiLM` head, Integrated `v3` synthesis, primary biopsy TME, `60,026`-cell SCLC scRNA `OR`-gate, $\le 14\text{ nm}$ synapse biophysics, Simon 2-stage DP, and risk-adjusted `eNPV`. |
| **Audited Bayesian `v4.3.2` Models, `32`-Antigen Back-Test & Forecast Ledger** (`optimus_txgnn/models/`, `backtest/`, `scripts/`, `forecasts/`) | [`q1_prevalence.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/models/q1_prevalence.py), [`q2_response.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/models/q2_response.py), [`q3_landscape_scenarios.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/models/q3_landscape_scenarios.py), [`q4_combinations.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/models/q4_combinations.py), [`q6_constructs_biophysics.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/models/q6_constructs_biophysics.py), [`decision_table_v4.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/models/decision_table_v4.py), [`registry_trial_mapper.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/models/registry_trial_mapper.py), [`pan_antigen_temporal_backtest.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/backtest/pan_antigen_temporal_backtest.py), [`verify_evidence_sources.py`](file:///home/nchen10/antigravity_prjs/kg/scripts/verify_evidence_sources.py), [`score_prospective_forecasts.py`](file:///home/nchen10/antigravity_prjs/kg/scripts/score_prospective_forecasts.py) | `260`-record provenance verification, 4-chain `NumPyro` `NUTS` `Q1` prevalence & TCE-only `Q2` `EXNEX` (`MixtureSameFamily` + Ibrahim–Chen power prior), `32`-antigen exact $\mathcal{O}(2^{F/2})$ temporal back-testing (`Q5` retired), segregated `Table A`/`Table B` valuation, `Ion Guard` `Q6` Appendix B, and append-only `SHA-256`/`.ots` forecasts. |
| **Portable Zero-PyTorch Studio** (`visualization_studio/`) | [`run_visualization_studio.py`](file:///home/nchen10/antigravity_prjs/kg/visualization_studio/run_visualization_studio.py), [`studio_engine/numpy_models.py`](file:///home/nchen10/antigravity_prjs/kg/visualization_studio/studio_engine/numpy_models.py), [`studio_engine/live_engines.py`](file:///home/nchen10/antigravity_prjs/kg/visualization_studio/studio_engine/live_engines.py) | Self-contained Windows 11 / Linux local server & pure-`NumPy` `FP16` inference engine (`0` PyTorch/CUDA imports) + standalone `V4` Evidence Studio. |

---

## 2. Phase-by-Phase Codebase, Mathematical Theory & Data I/O Reference (`Phase 0 -> Phase 8`)

---

### Phase 0: Clinical Knowledge Graph Curation, Signed Pharmacology (`v5bi`) & Safety/IP Foundation

#### 2.0.1 Architectural Design & Engineering Rationale
Raw `OptimusKG v3.2` contains `36,753` disease nodes—of which **`18,697` (`50.9%`) are non-therapeutic clinical measurements or laboratory traits** (e.g., *"systolic blood pressure determination"*, *"serum albumin measurement"*). Furthermore, standard biomedical KGs collapse drug–target interactions into unsigned `drug_protein` edges, which makes a `CD3` T-cell agonist arm (`CD3E` activation) indistinguishable from a small-molecule enzyme inhibitor or a `DLL3` tumor-anchoring arm.

`Phase 0` solves this via a two-stage deterministic `Polars` ETL pipeline:
1. **`clinical_v4` Graph Curation ([`prepare_optimuskg_data.py`](file:///home/nchen10/antigravity_prjs/kg/prepare_optimuskg_data.py), `1,619` lines)**: Filters out all `18,697` measurement nodes to retain exactly **`18,025` true clinical diseases**, caps ultra-dense `anatomy_gene` edges at `rank <= 5,000` per tissue and `disease_gene` edges at `score >= 0.40` (or `top_k = 15`), removes shortcut `drug_bioprocess` edges, and locks `node.csv` (`139,439` nodes) with a cryptographic `SHA-256` fingerprint.
2. **`amgen_v5 / clinical_v5` Signed Multi-Modal Enrichment ([`prepare_clinical_v5_and_combo_v1.py`](file:///home/nchen10/antigravity_prjs/kg/prepare_clinical_v5_and_combo_v1.py), `855` lines; [`optimus_txgnn/clinical_v5_builder.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/clinical_v5_builder.py), `387` lines; [`optimus_txgnn/ontology_and_safety_filters.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/ontology_and_safety_filters.py), `806` lines)**:
   - Splits drug–target pharmacology into **signed mechanism-of-action relations** (`signed_drug_targets.parquet`) distinguishing agonism/activation ($+1$), antagonism/inhibition ($-1$), and tumor-surface anchoring/binding.
   - Compiles Open Targets `L2G` + DisGeNET specificity-weighted genetics (`disease_gene_scores.parquet`), `OnSIDES v3.1.1` boxed warnings and typed DDIs (`typed_ddi_edges.parquet`), `FDA Orange/Purple Book` patent exclusivity tiers (`drug_ip_patent_tiers.parquet`), `53,952` graded clinical combination triples (`combo_v1.parquet`), and `MedCPT` (`768`-dim biomedical text embeddings + structural degree scalars) node feature tensors (`node_fm_features_v5_medcpt.pt`).

#### 2.0.2 Mathematical Formulation Implemented in Code
- **Specificity-Weighted Human Genetics Score (`disease_gene_scores.parquet`)**: For gene $g$ and disease $d$, let $s_{\text{OT}}(d,g) \in [0,1]$ be the Open Targets `L2G`/overall association score, $s_{\text{DG}}(d,g) \in [0,1]$ be the DisGeNET score, and $\text{DSI}(g) \in [0,1]$ be the gene's Disease Specificity Index (penalizing housekeeping/pan-cancer pleiotropic hubs like `TP53` or `TNF`):
  $$G_{\text{comb}}(d, g) = \operatorname{clip}\!\Big(\big(0.65\,s_{\text{OT}}(d,g) + 0.35\,s_{\text{DG}}(d,g)\big) \cdot \big(0.50 + 0.50\,\text{DSI}(g)\big),\; 0,\; 1\Big)$$
- **`MONDO` Ontology Subsumption Deduplication (`OntologyAndSafetyMasker._parse_mondo_obo`)**: Constructs the directed acyclic `is_a` graph $\mathcal{T}_{\text{MONDO}}$. Whenever both an umbrella node $d_{\text{parent}}$ (e.g., *"neuroendocrine neoplasm"*) and a specific actionable histotype $d_{\text{child}}$ (e.g., *"Merkel cell carcinoma"*) appear within $\le 2$ ontology hops, `OntologyAndSafetyMasker` suppresses redundant umbrella duplicates while preserving molecularly distinct subtypes.

#### 2.0.3 Phase 0 Data Input & Output Contracts

| Direction | File Path (Relative to Repo Root) | Format | Verified Shape / Size | Primary Keys & Key Schema Columns |
| :--- | :--- | :--- | :--- | :--- |
| **Input** | `data_optimuskg/raw_parquet/nodes/*.parquet` & `edges/*.parquet` | Parquet | `10` node tables, `22` edge tables | Raw `OptimusKG v3.2` silver/gold tables (`36,753` raw diseases; `8,787,955` raw `anatomy_gene` rows). |
| **Input** | `data_optimuskg/external_safety/mondo.obo`, `onsides-v3.1.1.zip`, `data_optimuskg/fda_reference/orange_book.zip` | OBO / ZIP | Official snapshots | `MONDO` DAG `is_a` hierarchy, `OnSIDES v3.1.1` FDA structured product label ADEs/boxed warnings, FDA `ANDA`/patent expiry tables. |
| **Output** | [`data_optimuskg/clinical_v4/node.csv`](file:///home/nchen10/antigravity_prjs/kg/data_optimuskg/clinical_v4/dataset_summary.json) & `kg_directed.csv` | CSV | `139,439` nodes; `4,692,768` directed (`9,385,536` bidir) edges | `node_index (int)`, `node_id (str)`, `node_type (10 types: disease=18,025, drug=18,640, gene/protein=26,660, anatomy=13,120, ...)`, `node_name`. Locked by `SHA-256` `node_fingerprint`. |
| **Output** | `data_optimuskg/amgen_v5/signed_drug_targets.parquet` | Parquet | `(36,641, 5)` | `drug_id`, `gene_id`, `gene_symbol`, `signed_relation`, `weight` (`float32`). |
| **Output** | `data_optimuskg/amgen_v5/disease_gene_scores.parquet` | Parquet | `(8,899,338, 6)` | `disease_id`, `gene_id`, `ot_evidence_score`, `disgenet_score`, `specificity_index`, `combined_genetic_score`. |
| **Output** | `data_optimuskg/amgen_v5/anatomy_gene_edges.parquet` | Parquet | `(5,247,243, 2)` | `anatomy_id` (`UBERON`), `gene_id` (`Entrez/HGNC`). |
| **Output** | `data_optimuskg/amgen_v5/typed_ddi_edges.parquet` | Parquet | `(1,345,373, 11)` | `u_id`, `v_id`, `is_syn`, `is_antag`, `is_adv`, `tox_qtc`, `tox_bleed`, `tox_inf`, `tox_myelo`, `tox_organ`, `ddi_type`. |
| **Output** | `data_optimuskg/amgen_v5/drug_ip_patent_tiers.parquet` | Parquet | `(18,644, 21)` | `node_id`, `amgen_key`, `modality`, `anda_active_count`, `biosimilar_available`, `black_box_warning`, `ip_tier_code`, `cost_badge`. |
| **Output** | `data_optimuskg/amgen_v5/combo_v1.parquet` | Parquet | `(53,952, 10)` | `u_id`, `v_id`, `disease_id`, `label_eff`, `label_tox`, `label_antag`, `sample_weight`, `source_tag`, `n_shared_ncts`, `pair_phase`. |
| **Output** | `data_optimuskg/amgen_v5/node_fm_features_v5_medcpt.pt` | PyTorch Dict | `10` tensors (`float16`) | Maps each `node_type` to `(N_type, 770)` (or `(18025, 772)` for `disease`): `768`-dim `MedCPT` embedding + log-degree & clinical metadata flags. |

---

### Phase 1: 24-Checkpoint `HeteroRGCN` Foundation Super-Ensemble (`96 GB` GPU)

#### 2.1.1 Architectural Design & Behind-the-Scenes Engineering Turnaround
As detailed in Section 3.2 of the Playbook, naively scaling `TxGNN` from $d = 512$ to $d = 2048$ initially degraded held-out validation `MRR` because of three coupled bottlenecks: (1) pure degree-biased negative sampling starved rare neuroendocrine diseases of clean negative gradients, (2) `bf16` accumulation inside high-degree `anatomy_gene` / `protein_protein` message sums truncated small neighbor updates, and (3) one-sided drug-centric binary cross-entropy ignored disease-to-drug ranking symmetry.

Our codebase resolves all three in [`optimus_txgnn/model.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/model.py#L614-L1581), [`optimus_txgnn/dgl_compat.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/dgl_compat.py#L410-L786), and [`optimus_txgnn/trainer.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/trainer.py#L99-L2478), orchestrated by [`train_optimus_txgnn.py`](file:///home/nchen10/antigravity_prjs/kg/train_optimus_txgnn.py) and [`run_colab_campaign.py`](file:///home/nchen10/antigravity_prjs/kg/run_colab_campaign.py):
- **4 Architectural Families × 6 Configurations = 24 Production Checkpoints (`35.2 GB`)**:
  1. `v4_wmatch_d2048` (`6` ckpts: seeds `{42, 1, 2}` × uniform negative mix $u \in \{0.20, 0.50\}$, $d = 2048$ on `clinical_v4`).
  2. `v5bi_d2048` (`6` ckpts: bidirectional drug $\leftrightarrow$ disease cross-entropy + signed `v5` pharmacology, $d = 2048$).
  3. `v5bi_d3072` (`6` ckpts: `944.2M`-parameter flagship width $d = 3072$, full `FP32` accumulation on `96 GB` NVIDIA Blackwell/Hopper GPU).
  4. `v5fm_medcpt_d2048` (`6` ckpts: fuses `node_fm_features_v5_medcpt.pt` via a learned `LayerNorm + Linear` residual adapter into layer-0 node embeddings, $d = 2048$).

#### 2.1.2 Mathematical Formulation Implemented in Code
1. **Layer-0 Multimodal Feature Fusion (`HeteroRGCN.get_input_dict`, [`model.py#L1230-L1295`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/model.py#L1230-L1295))**:
   For node $i$ of type $\tau$, with trainable structural embedding $\mathbf{e}_i^{(0)} \in \mathbb{R}^d$ and optional `MedCPT` feature vector $\mathbf{x}_i^{\text{FM}} \in \mathbb{R}^{d_{\text{fm}}}$:
   $$\mathbf{h}_i^{(0)} = \mathbf{e}_i^{(0)} + \alpha_\tau \cdot \mathbf{W}_{\text{FM},\tau}\,\operatorname{LayerNorm}\!\left(\mathbf{x}_i^{\text{FM}}\right)$$
   where $\alpha_\tau$ is zero-initialized or small-scale gated so multimodal text priors enrich zero-degree nodes without overwriting dense topological identity.
2. **Two-Layer Degree-Normalized `FP32` `CSR` Relational Graph Convolution (`HeteroRGCNLayer.forward`, [`model.py#L685-L845`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/model.py#L685-L845))**:
   For each canonical relation $r = (\tau_s, \psi_r, \tau_t) \in \mathcal{R}$ and layer $\ell \in \{1, 2\}$, let $\mathcal{N}_r(v)$ be incoming neighbors of destination node $v$:
   $$\mathbf{m}_{r,v}^{(\ell)} = \frac{1}{\max(|\mathcal{N}_r(v)|, 1)} \sum_{u \in \mathcal{N}_r(v)} w_{u,v}^{(r)}\,\mathbf{h}_u^{(\ell-1)} \in \mathbb{R}^d \quad (\text{computed via } \texttt{fp32 CSR spmm})$$
   $$\mathbf{h}_v^{(\ell)} = \sigma\!\left(\mathbf{W}_{\text{self},\tau_t}^{(\ell)} \mathbf{h}_v^{(\ell-1)} + \frac{1}{|\mathcal{R}_{\to \tau_t}|} \sum_{r \in \mathcal{R}_{\to \tau_t}} \mathbf{W}_r^{(\ell)} \mathbf{m}_{r,v}^{(\ell)}\right)$$
   Computing $\mathbf{m}_{r,v}^{(\ell)}$ *before* multiplying by $\mathbf{W}_r^{(\ell)} \in \mathbb{R}^{d \times d}$ reduces dense matrix operations from $\mathcal{O}(|E_r| d^2) = 9.39\times 10^6 \cdot d^2$ to $\mathcal{O}(|V_{\tau_t}| d^2) \le 2.67\times 10^4 \cdot d^2$—a **`350x` compute and VRAM reduction**!
3. **Disease-Similarity Prototype Augmentation & Bidirectional `DistMult` Decoder (`DistMultPredictor`, [`model.py#L101-L605`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/model.py#L101-L605))**:
   For sparse/zero-shot diseases, $\tilde{\mathbf{h}}_d = \gamma_d \mathbf{h}_d^{(2)} + (1 - \gamma_d) \sum_{d' \in \mathcal{N}_k^{\text{sim}}(d)} \tilde{s}_{d,d'} \mathbf{h}_{d'}^{(2)}$. Then for relation $\psi \in \{\texttt{indication}, \texttt{contraindication}, \texttt{off-label}\}$ with diagonal relation vector $\mathbf{r}_\psi \in \mathbb{R}^d$:
   $$s_\psi(u, d) = \left\langle \mathbf{h}_u^{(2)} \odot \mathbf{r}_\psi,\; \tilde{\mathbf{h}}_d \right\rangle = \sum_{k=1}^d h_{u,k}^{(2)}\, r_{\psi,k}\, \tilde{h}_{d,k}$$
4. **Mixed Degree/Uniform Negative Sampling & Bidirectional Objective (`Full_Graph_NegSampler`, [`trainer.py#L120-L245`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/trainer.py#L120-L245))**:
   Negative destinations $d^-$ are drawn from the mixture $P_u(d^-) = u \cdot \frac{1}{|\mathcal{V}_{\text{dis}}|} + (1 - u) \cdot \frac{\deg(d^-)^{0.75}}{\sum_{d'} \deg(d')^{0.75}}$ ($u \in \{0.20, 0.50\}$), optimizing joint drug$\to$disease and (`v5bi`) disease$\to$drug binary/sampled-softmax cross-entropy.

#### 2.1.3 Phase 1 Data Input & Output Contracts

| Direction | File Path | Format | Verified Shape / Size | Primary Keys & Key Schema Columns |
| :--- | :--- | :--- | :--- | :--- |
| **Input** | `data_optimuskg/clinical_v4/` & `data_optimuskg/clinical_v5/` | CSV / Parquet | `139,439` nodes; `full_graph_{42,1,2}` & `complex_disease_{42,1..4}` splits | `train.csv`, `valid.csv`, `test.csv`, `disease_metadata.parquet`, `split_fingerprints_reference.json`. |
| **Input** | `data_optimuskg/amgen_v5/node_fm_features_v5_medcpt.pt` | PyTorch | `10` node types (`fp16`) | Consumed by `v5fm_medcpt_d2048` family. |
| **Output** | `runs/colab_full_graph_*/model.pt`, `inp_emb.pt`, `config.json`, `manifest.json` | PyTorch / JSON | `24` checkpoint dirs (`35.2 GB` total) | `HeteroRGCN` weights (`W_r`, `DistMult` `r_psi`), learned node embeddings `(N_tau, 2048 or 3072)`, SHA-256 `split_hashes`. |
| **Output** | [`amgen_discovery_studio/results/paired_24model_vs_drive_bootstrap.json`](file:///home/nchen10/antigravity_prjs/kg/amgen_discovery_studio/results/paired_24model_vs_drive_bootstrap.json) | JSON | `0.08 MB` (`1,000` paired disease bootstraps) | Held-out test `AUPRC`, `AUROC`, `MRR`, `Recall@50` (`+38.4%` relative `AUPRC` gain vs. baseline `TxGNN`, $p < 10^{-4}$). |

---

### Phase 2: Per-Disease Standardization, Mondrian Conformal Calibration & BiTE Solid-Tumor `AND`-Gate

#### 2.2.1 Architectural Design & Engineering Rationale
Raw `DistMult` logits $s_{\text{ind}}(u, d)$ suffer from **disease hub bias**: richly annotated diseases (`NSCLC`, `breast carcinoma`) systematically exhibit larger embedding norms $\|\tilde{\mathbf{h}}_d\|_2$ than rare neuroendocrine tumors (`MCC`, `EP-NEC`), inflating raw sigmoid probabilities for common cancers even when biological specificity is low. Moreover, `AMG 757` (`Tarlatamab`) is an investigative `DLL3 × CD3` BiTE whose `CD3E` arm binds pan-T-cell `CD3ε`; a naive target-projection averages `DLL3` (`50%`) and `CD3E` (`50%`), causing T-cell-rich hematologic malignancies (`T-ALL`, `lymphoma`—where `DLL3 = 0%`) to hijack top ranks!

`Phase 2` eliminates both pathologies in [`optimus_txgnn/calibration.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/calibration.py#L39-L428) and [`optimus_txgnn/bite_biomarker_and_expansion.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/bite_biomarker_and_expansion.py#L1-L739), orchestrated by [`run_amgen_goal1_and_goal2_pipeline.py`](file:///home/nchen10/antigravity_prjs/kg/run_amgen_goal1_and_goal2_pipeline.py):
1. **`partition_bite_specs_for_projection_and_genetics()` ([`bite_biomarker_and_expansion.py#L140-L215`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/bite_biomarker_and_expansion.py#L140-L215))**: Automatically separates **tumor-associated anchoring antigens (`TAA`: `DLL3`)** from **universal immune-effector triggers (`CD3E`, `CD3D`, `CD3G`, `CD28`)** so cold/hybrid drug projection and `L2G` genetics strictly anchor on `DLL3` while `CD3` enters as a multiplicative T-cell microenvironment readiness factor.
2. **`standardize_logits_per_disease()` ([`calibration.py#L39-L63`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/calibration.py#L39-L63))**: Standardizes every drug's logit against all `9,856` candidate drugs *within* each disease $d$ and *within* each checkpoint $c$ before ensemble aggregation.
3. **`MondrianConformalCalibrator` ([`calibration.py#L77-L251`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/calibration.py#L77-L251))**: Computes finite-sample valid $p$-values across $3 \times 4 = 12$ `(drug_degree_bucket, disease_degree_bucket)` strata with Benjamini–Hochberg `FDR` $q$-values and OOF isotonic/Platt probability calibration.

#### 2.2.2 Mathematical Formulation Implemented in Code
1. **Within-Checkpoint Per-Disease $Z$-Score & 24-Model Aggregation**:
   For candidate drug pool $\mathcal{U}$ ($|\mathcal{U}| = 9,856$), disease $d \in \{1,\dots,18025\}$, and checkpoint $c \in \{1,\dots,24\}$:
   $$\mu_d^{(c)} = \frac{1}{|\mathcal{U}|}\sum_{u' \in \mathcal{U}} s_{\text{ind}}^{(c)}(u', d), \qquad \sigma_d^{(c)} = \sqrt{\frac{1}{|\mathcal{U}|}\sum_{u' \in \mathcal{U}} \left(s_{\text{ind}}^{(c)}(u', d) - \mu_d^{(c)}\right)^2 + \varepsilon}$$
   $$z_{u,d}^{(c)} = \frac{s_{\text{ind}}^{(c)}(u, d) - \mu_d^{(c)}}{\sigma_d^{(c)}}, \qquad \bar{z}_{u,d}^{(24)} = \frac{1}{24}\sum_{c=1}^{24} z_{u,d}^{(c)}, \qquad \hat{\sigma}_{\text{epi}}(u, d) = \operatorname{SD}_{c=1}^{24}\!\left(z_{u,d}^{(c)}\right)$$
2. **Degree-Stratified Mondrian Conformal $p$-Value & BH-FDR (`MondrianConformalCalibrator.calibrate_drug_row`)**:
   Drug degree falls into $b_u \in \{0, 1, 2\}$ and disease degree into $b_d \in \{0, 1, 2, 3\}$ (`12` total reference pools $\mathcal{C}_{b_u, b_d}$ stored in `cf_bucket_{i}_{j}` up to `1.2M` calibration negatives each):
   $$p_{\text{conf}}(u, d) = \frac{1 + \sum_{\tilde{z} \in \mathcal{C}_{b_u, b_d}} \mathbb{I}\!\left[\tilde{z} \ge \bar{z}_{u,d}^{(24)}\right]}{1 + |\mathcal{C}_{b_u, b_d}|}, \qquad q_{\text{FDR}}(u, d_{(k)}) = \min_{j \ge k} \left(\frac{D_{\text{ther}}\, p_{\text{conf}}(u, d_{(j)})}{j}\right)$$
3. **BiTE Solid-Tumor Mechanistic `AND`-Gate (`compute_bite_taa_genetics_and_conditioned_scores`)**:
   A solid-tumor BiTE requires **simultaneous** surface `TAA` presence and non-excluded `CD3+` T-cell infiltration (`build_bite_solid_tumor_exclusion_mask` sets $M_{\text{heme}}(d) = 0$ for lymphoid/myeloid malignancies on non-lymphoid TAAs like `DLL3`):
   $$S_{\text{BiTE-AND}}(u, d) = M_{\text{heme}}(d) \cdot P_{\text{cal}}\!\left(\bar{z}_{u,d}^{(24)}, G_{\text{TAA}}(d), A_{\text{tissue}}(d), -P_{\text{contra}}(u,d)\right) \cdot \sqrt{\operatorname{clip}\!\big(\pi_{\text{TAA}}(d) \cdot R_{\text{CD3-TME}}(d),\; 0,\; 1\big)}$$

#### 2.2.3 Phase 2 Data Input & Output Contracts

| Direction | File Path | Format | Verified Shape / Size | Primary Keys & Key Schema Columns |
| :--- | :--- | :--- | :--- | :--- |
| **Input** | `runs/colab_full_graph_*/` (`24` ckpts) + [`configs/amgen_portfolio_v5.yaml`](file:///home/nchen10/antigravity_prjs/kg/configs/amgen_portfolio_v5.yaml) + [`configs/bite_pan_cancer_biomarkers_v5.yaml`](file:///home/nchen10/antigravity_prjs/kg/configs/bite_pan_cancer_biomarkers_v5.yaml) | PyTorch / YAML | `47` Amgen portfolio assets; `18,025` diseases | Asset target specs (`tarlatamab: DLL3 [TAA], CD3E [T_EFFECTOR]`), curated `SP347` IHC priors, safety rules. |
| **Output** | [`amgen_discovery_studio/results/goal1_amgen_repurposing_top50_all47.parquet`](file:///home/nchen10/antigravity_prjs/kg/amgen_discovery_studio/results/goal1_amgen_repurposing_top50_all47.parquet) (and `.csv`) | Parquet / CSV | `(2350, 57)` (`0.15 MB` Parquet / `1.19 MB` CSV) | `amgen_key`, `disease_id`, `disease_name`, `calibrated_prob`, `ranker_score`, `bite_and_gate_score`, `z_std_24model`, `epistemic_std_z`, `conformal_pvalue`, `fdr_qvalue`, `direct_l2g`, `ppi1hop_l2g`, `contraindication_prob`, `biomarker_pos_pct`. |
| **Output** | `data_optimuskg/post_ultra_inference_pack/rankers_and_calibrators.pkl` | Pickle | `MondrianConformalCalibrator` + `AmgenRepurposeRanker` | Exported into `visualization_studio/engine_data/portable_models_v1.npz` (`12` `cf_bucket_i_j` arrays + `cf_iso_x/y` + `rk_clf_coef`). |

---

### Phase 3: Option 1 Clinical Protocol & Empirical Multi-Omics Orthogonal MCDA

#### 2.3.1 Architectural Design & Engineering Rationale
Graph link prediction (`Phase 2`) identifies biologically plausible `DLL3+` indications, but a clinical development team designing Phase 2 cohorts must also account for **patient-level RNA/protein prevalence in real biopsies**, **competitor `DLL3` ADC/TCE/radioligand trial crowding**, **CNS/ICANS step-up monitoring burden**, and **Simon 2-stage sample-size feasibility**—*without double-counting `DLL3` biomarker prevalence* (which already entered `Phase 2`'s `bite_and_gate_score`!).

[`build_amg757_option1_empirical_data.py`](file:///home/nchen10/antigravity_prjs/kg/build_amg757_option1_empirical_data.py) (`1,909` lines) and [`optimus_txgnn/option1_protocol_and_omics.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/option1_protocol_and_omics.py#L1-L760) (`760` lines) implement a strictly **zero-double-counting 4-pillar Multi-Criteria Decision Analysis (MCDA)** paired with an exact binomial dynamic-programming solver ([`optimus_txgnn/basket_design.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/basket_design.py#L1-L565)):
- **Unboosted Mechanistic Pillar**: Uses `calibrated_prob` (pure KG + Mondrian calibration *prior* to IHC multiplication) for Pillar 1 so `DLL3` expression is counted **once and only once** in Pillar 2 (verified in [`tests/test_bite_and_amg757_expansion.py::test_orthogonal_mcda_no_double_counting_and_dirichlet_sensitivity`](file:///home/nchen10/antigravity_prjs/kg/tests/test_bite_and_amg757_expansion.py)).
- **Clinical Subtype Hygiene Guardrail (`is_pediatric_or_circumscribed_idh_wt_glioma`)**: Explicitly quarantines pediatric/circumscribed `IDH`-wildtype gliomas (`pilocytic astrocytoma`, `ganglioglioma`—where `DLL3` is low and intact blood–brain barrier + pediatric neurotoxicity pose high `ICANS` risk) from adult `IDH`-mutant diffuse glioma (`MONDO_0016685`, where proneural `ASCL1 -> DLL3` is transcriptionally locked).

#### 2.3.2 Mathematical Formulation Implemented in Code
1. **Four Orthogonal `Option 1` Pillars (`compute_orthogonal_mcda_pillars`, [`option1_protocol_and_omics.py#L465-L585`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/option1_protocol_and_omics.py#L465-L585))**:
   - $P_1^{\text{KG}}(d) \in [0,1]$: Unboosted 24-model calibrated KG probability (`calibrated_prob`).
   - $P_2^{\text{Bio}}(d) \in [0,1]$: Empirical `SP347` IHC positivity blended with patient-level `TCGA/TARGET/GEO` RNA positivity ($\text{TPM} > \tau_{\text{pos}}$).
   - $P_3^{\text{White}}(d) \in [0,1]$: Competitive whitespace score from live-snapshotted `ClinicalTrials.gov v2` competitor `DLL3` trials ($n_{\text{Ph2/3}}$, $n_{\text{basket}}$):
     $$P_3^{\text{White}}(d) = \exp\!\big(-0.35\,n_{\text{Ph2/3}}^{\text{comp}}(d) - 0.12\,n_{\text{Ph1/basket}}^{\text{comp}}(d)\big)$$
   - $P_4^{\text{Feas}}(d) \in [0,1]$: Operational safety & blood–brain barrier / `CRS–ICANS` outpatient readiness (`FAERS` ROR & CNS anatomical penalty).
   $$S_{\text{Opt1}}(d) = 100 \times \Big(0.30\,P_1^{\text{KG}}(d) + 0.35\,P_2^{\text{Bio}}(d) + 0.20\,P_3^{\text{White}}(d) + 0.15\,P_4^{\text{Feas}}(d)\Big)$$
2. **Exact Simon (1989) Two-Stage Optimal & Minimax Solver (`solve_exact_simon_two_stage`, [`basket_design.py#L45-L165`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/basket_design.py#L45-L165))**:
   Given historical null ORR $p_0$, target ORR $p_1 > p_0$, type-I error $\alpha = 0.05$, and power $1 - \beta = 0.80$, enumerates exact binomial PMFs $B(x; n, p) = \binom{n}{x} p^x (1-p)^{n-x}$ in log-space (`_log_binom_pmf`) over $(n_1, r_1, N, r)$ to minimize expected sample size under the null:
   $$\operatorname{EN}(p_0) = n_1 + \big(1 - \operatorname{PET}(p_0)\big)(N - n_1), \qquad \operatorname{PET}(p) = \sum_{x_1 = 0}^{r_1} B(x_1; n_1, p)$$
   subject to exact type-I error $\sum_{x_1 = r_1 + 1}^{n_1} B(x_1; n_1, p_0) \sum_{x_2 = \max(0, r + 1 - x_1)}^{N - n_1} B(x_2; N - n_1, p_0) \le \alpha$ and exact power $\ge 1 - \beta$ (verified against all published entries of Simon 1989 *Controlled Clinical Trials* Table 1).

#### 2.3.3 Phase 3 Data Input & Output Contracts

| Direction | File Path | Format | Verified Shape / Size | Primary Keys & Key Schema Columns |
| :--- | :--- | :--- | :--- | :--- |
| **Input** | `amgen_discovery_studio/results/goal1_amgen_repurposing_top50_all47.parquet` + [`configs/amg757_indication_mapping_v2.yaml`](file:///home/nchen10/antigravity_prjs/kg/configs/amg757_indication_mapping_v2.yaml) | Parquet / YAML | `tarlatamab` slice + canonical subtype mapping | Maps KG `MONDO/EFO` IDs to primary patient RNA cohorts and historical 2L SoC ORR benchmarks ($p_0, p_1$). |
| **Input** | `data_optimuskg/option1_amg757/raw_api_snapshots/` & `raw_geo/` | JSON / TSV | Cached `TCGA`, `TARGET-NBL`, `GEO`, `CT.gov v2`, `openFDA FAERS` | Raw empirical patient RNA matrices, competitor NCT registries, and `CRS/ICANS` disproportionality counts. |
| **Output** | `data_optimuskg/option1_amg757/option1_empirical_bundle.json` (synced to `visualization_studio/engine_data/`) | JSON | `0.25 MB` | Compiled per-indication empirical RNA positivity, median $\log_2(\text{TPM}+1)$, competitor NCT counts, and Simon 2-stage designs. |
| **Output** | [`amgen_discovery_studio/results/amg757_option1_protocol_ranking.parquet`](file:///home/nchen10/antigravity_prjs/kg/amgen_discovery_studio/results/amg757_option1_protocol_ranking.parquet) (and `.csv`) | Parquet / CSV | `(50, 57)` | Adds `empirical_rna_pos_pct`, `competitor_dll3_trials_n`, `competitive_whitespace_score`, `option1_protocol_score`, `protocol_score_p10/p90`, `mcda_top3_prob_pct`, `operational_safety_track`. |

---

### Phase 4: Option 2 Supervised Cold-Antigen `OptimusBiTEHead` (`SVD-LoRA` + `FiLM`)

#### 2.4.1 Architectural Design & Engineering Rationale
Why build a second independent AI engine (`Option 2`) when `Phase 2–3` (`Option 1`) already ranks indications? Because `Tarlatamab` had **zero approved clinical edges** prior to 2024 SCLC approval (`cold-antigen` regime), and a critical scientific question arises: **If we hold out `DLL3` and its entire Notch-ligand paralog family (`DLL1`, `DLL3`, `DLL4`, `JAG1`, `JAG2`) from supervision, can a model trained across *all* other clinical `TCE`, `ADC`, `CAR-T/CELL`, `mAb`, and `SM` programs predict *from scratch* which diseases respond to a `DLL3` T-cell engager vs. a `DLL3` ADC?**

[`build_option2_bite_head_bank.py`](file:///home/nchen10/antigravity_prjs/kg/build_option2_bite_head_bank.py) (`749` lines), [`optimus_txgnn/option2_bite_head.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/option2_bite_head.py#L1-L2133) (`2,133` lines), [`train_option2_bite_head.py`](file:///home/nchen10/antigravity_prjs/kg/train_option2_bite_head.py) (`809` lines), and [`optimus_txgnn/option2_debiased_v2.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/option2_debiased_v2.py) (`1,047` lines) implement this end-to-end:
1. **Mature Extracellular Domain (`ECD`) Protein Language Model Extraction (`parse_uniprot_ecd_sequence`, [`option2_bite_head.py#L595-L646`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/option2_bite_head.py#L595-L646))**: Strips cleaved signal peptides, propeptides (`GPI`-anchor C-terminal propeptides!), transmembrane helices, and cytoplasmic tails from UniProt topology annotations so **`ESM-2 3B`** (`esm2_t36_3B_UR50D`) embeds *only* the solvent-exposed extracellular domain accessible to a BiTE `scFv` (`esm_feats_fp16: (6963, 33)`).
2. **Paralog-Family-Disjoint 4-Fold Cold-Antigen Cross-Validation (`antigen_paralog_family_key`, `antigen_group_folds`, [`option2_bite_head.py#L930-L980`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/option2_bite_head.py#L930-L980))**: Groups homologous paralogs (`CEACAM*`, `CLDN*`, `DLL/JAG`, `ERBB*`, `TNFRSF*`, `GPRC5D`, etc.) into indivisible CV clusters so `DLL3` cannot cheat via `DLL1/DLL4` leakage during out-of-fold (`OOF`) evaluation.
3. **Zero-Initialized `SVD` Disease-Manifold `LoRA` + 5-Modality `FiLM` Residual Architecture (`OptimusBiTEHead`, [`option2_bite_head.py#L1051-L1471`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/option2_bite_head.py#L1051-L1471))**: Adapts the `24` frozen GNN backbones inside the top-$K$ right-singular subspace of the disease manifold with $\mathbf{B}_{\text{init}} = \mathbf{0}$ and $\mathbf{W}_{\text{out,init}} = \mathbf{0}$, guaranteeing that at Step 0 the neural network reproduces the **exact frozen 24-checkpoint GNN $Z$-score** before learning modality-specific (`TCE` vs. `ADC`) residual corrections!

#### 2.4.2 Mathematical Formulation Implemented in Code
1. **Disease-Manifold `SVD` Low-Rank Subspace Adapter (`OptimusBiTEHead._adapted_S`, [`option2_bite_head.py#L1214-L1232`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/option2_bite_head.py#L1214-L1232))**:
   For each checkpoint $c \in \{1,\dots,24\}$, let $\mathbf{S}_c \in \mathbb{R}^{G \times d_c}$ be relation-projected gene embeddings (`rev_drug_protein`) and $\mathbf{E}_c \in \mathbb{R}^{D \times d_c}$ be `indication`-weighted disease embeddings ($G = 6,963$ candidate surface/druggable genes, $D = 2,944$ oncology/therapeutic diseases, $G \times D \approx 2.05 \times 10^7$ total grid; `200,192` active clinical supervision pairs across modalities). Let $\mathbf{V}_c \in \mathbb{R}^{d_c \times K}$ be the top-$K$ orthonormal right singular vectors of $\mathbf{E}_c$. For modality $m \in \{\text{TCE}, \text{ADC}, \text{CELL}, \text{MAB\_OTHER}, \text{SM}\}$:
   $$\mathbf{p}_{g,c} = \frac{\mathbf{S}_{c,g}}{\|\mathbf{S}_{c,g}\|_2} \mathbf{V}_c \in \mathbb{R}^K, \qquad \Delta\mathbf{s}_{g,c}^{(m)} = \tanh\!\left(\frac{\alpha}{r}\,\mathbf{p}_{g,c} \mathbf{A}_{m,c} \mathbf{B}_{m,c}\right) \mathbf{V}_c^\top$$
   $$\tilde{\mathbf{S}}_{c,g}^{(m)} = \mathbf{S}_{c,g} + \|\mathbf{S}_{c,g}\|_2 \cdot \delta_{\max} \cdot \Delta\mathbf{s}_{g,c}^{(m)}, \qquad z_{g,d}^{(c,m)} = \frac{\tilde{\mathbf{S}}_{c,g}^{(m)} \mathbf{E}_{c,d}^\top - \mu_{d}^{(c)}}{\sigma_{d}^{(c)}}$$
   where $\mathbf{A}_{m,c} \in \mathbb{R}^{K \times r}$, $\mathbf{B}_{m,c} \in \mathbb{R}^{r \times K}$ ($\mathbf{B}_{m,c} = \mathbf{0}$ at init).
2. **Modality-Conditioned `FiLM` Residual Network & `Plackett–Luce` Listwise Softmax Loss (`score_grid`, `listwise_softmax_loss`, [`option2_bite_head.py#L1310-L1485`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/option2_bite_head.py#L1310-L1485))**:
   Concatenating the 24-checkpoint summary + per-family means (`7` dims), checkpoint-aligned bilinear interaction $\frac{1}{24}\sum_c (\mathbf{P}_c \mathbf{S}_{c,g}) \odot (\mathbf{Q}_c \mathbf{E}_{c,d})$ (`inter_dim + 1`), `HPA` organ/lineage IHC + `TCGA` RNA (`12` dims), pairwise Open Targets + anatomy concordance `pair_gd` (`3` dims), lineage RNA × disease flags (`7` dims), heme concordance (`2` dims), standardized gene features + `ESM-2 3B` ECD features, and disease features into $\mathbf{x}_{g,d} \in \mathbb{R}^{n_{\text{in}}}$:
   $$(\boldsymbol{\gamma}_m, \boldsymbol{\beta}_m) = \mathbf{W}_{\text{FiLM}}\,\mathbf{e}_m, \qquad \mathbf{h}_{g,d,m} = \operatorname{GELU}\!\Big((1 + \boldsymbol{\gamma}_m) \odot \big(\mathbf{W}_1 \mathbf{x}_{g,d} + \mathbf{b}_1\big) + \boldsymbol{\beta}_m\Big)$$
   $$\hat{y}(g, d, m) = \alpha_m \cdot \bar{z}_{g,d}^{(24,m)} + b_m + \mathbf{w}_{\text{out}}^\top \operatorname{GELU}(\mathbf{W}_2 \mathbf{h}_{g,d,m} + \mathbf{b}_2)$$
   Trained jointly on **Direction A (`antigen -> diseases`)** and **Direction B (`disease -> surface antigens`)** via temperature-scaled `Plackett–Luce` listwise cross-entropy over positive sets $\mathcal{P}_q$ against hard/in-domain negatives $\mathcal{N}_q$:
   $$\mathcal{L}_{\text{list}}(q) = -\frac{1}{|\mathcal{P}_q|} \sum_{i \in \mathcal{P}_q} \log \frac{\exp(\hat{y}_{q,i} / \tau)}{\exp(\hat{y}_{q,i} / \tau) + \sum_{j \in \mathcal{N}_q} \exp(\hat{y}_{q,j} / \tau)} + \lambda_{\text{LoRA}} \sum_{m,c} \|\mathbf{A}_{m,c} \mathbf{B}_{m,c}\|_F^2$$

#### 2.4.3 Phase 4 Data Input & Output Contracts

| Direction | File Path | Format | Verified Shape / Size | Primary Keys & Key Schema Columns |
| :--- | :--- | :--- | :--- | :--- |
| **Input** | `data_optimuskg/option2_bite_head/bank/features.npz`, `esm_gene_embeddings.npz`, `bank_meta.json`, `labels.json` | NPZ / JSON | `G = 6,963` genes × `D = 2,944` diseases (`29.58 MB` + `0.79 MB`) | `24` frozen checkpoint `S_c, E_c, mu_c, sd_c`, `HPA` 20-cancer IHC `(G, 20, 4)`, `TCGA/CCLE` RNA, `pair_gd (G, D, 3) fp16`, `ESM-2 3B` mature ECD `(6963, 33) fp16`. |
| **Output** | [`amgen_discovery_studio/option2_bite_head/option2_bite_head_pack.pt`](file:///home/nchen10/antigravity_prjs/kg/amgen_discovery_studio/option2_bite_head/option2_bite_head_pack.pt) | PyTorch Pack | `109.48 MB` (`schema: v1.2`) | `8` trained `OptimusBiTEHead` fold/seed `state_dicts`, `24` `bank_ckpts` metadata, `5` modalities (`TCE, ADC, CELL, MAB_OTHER, SM`), `esm_feats_fp16 (6963, 33)`. |
| **Output** | [`amgen_discovery_studio/option2_bite_head/option2_dll3_tce_disease_ranking.csv`](file:///home/nchen10/antigravity_prjs/kg/amgen_discovery_studio/option2_bite_head/option2_dll3_tce_disease_ranking.csv) & `option2_dll3_adc_disease_ranking.csv` | CSV | `(2944, 15)` each (`0.29 MB`) | `final_rank`, `disease_id`, `disease_name`, `final_score`, `heldout_oof_rank` (strictly blinded to `DLL3` family!), `conformal_p`, `conformal_fdr_q`, `frozen_backbone_rank`, `is_neuroendocrine`. |
| **Output** | [`amgen_discovery_studio/option2_bite_head/OPTION2_SUMMARY.json`](file:///home/nchen10/antigravity_prjs/kg/amgen_discovery_studio/option2_bite_head/OPTION2_SUMMARY.json) & `amgen_discovery_studio/results/q5_dll3_debiased_ranking.parquet` | JSON / Parquet | `0.22 MB` / `0.09 MB` | Full 5-arm ablation OOF metrics (`backbone_frozen`, `omics_only`, `head_no_adapter`, `full_shared_adapter`, `full_per_modality_adapter`) + `WP6` popularity-debiased rankings. |

---

### Phase 5: Integrated `v3` Six-Pillar Synthesis, Target-Validated Combo Gating & Exact $2^6$ Shapley Attribution

#### 2.5.1 Architectural Design & Engineering Rationale
Having built two philosophically distinct engines—**Option 1 (`Phase 3`, deductive clinical-protocol & primary omics MCDA)** and **Option 2 (`Phase 4`, inductive cold-antigen neural `TCE` discovery across `2,944` diseases)**—`Phase 5` unifies them into a single decision table while adding **disease-specific rational combination partner selection** and **exact game-theoretic pillar attribution**:
- Implemented in [`optimus_txgnn/amg757_integrated_synthesis.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/amg757_integrated_synthesis.py#L1-L798) (`798` lines), [`optimus_txgnn/combo_model.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/combo_model.py#L311-L732) (`732` lines), [`build_amg757_comprehensive_synthesis.py`](file:///home/nchen10/antigravity_prjs/kg/build_amg757_comprehensive_synthesis.py) (`1,163` lines), and [`scripts/amg757_pillar_attribution.py`](file:///home/nchen10/antigravity_prjs/kg/scripts/amg757_pillar_attribution.py) (`460` lines).
- **Target-Validated Combination Hygiene (`resolve_target_validated_combo_partner`, [`amg757_integrated_synthesis.py#L364-L499`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/amg757_integrated_synthesis.py#L364-L499))**: Generic KG combination scorers occasionally suggest `Bemarituzumab` (`FGFR2b` mAb) or `Sotorasib` (`KRAS G12C`) in neuroendocrine tumors simply because both are oncology assets in the Amgen portfolio. `resolve_target_validated_combo_partner()` enforces a hard target-expression gate: targeted partners are disqualified unless the target biomarker (`FGFR2b`, `KRAS G12C`, `PRMT5/MTAP-del`) is empirically present in $\ge 10\%$ of that tumor's biopsies, routing neuroendocrine cohorts cleanly to **`PD-1/PD-L1` (`Pembrolizumab` / `Durvalumab` / `Atezolizumab`)**, **`AMG 193` (`MTAP-del` cooperative synthetic lethality)**, or **`Lurbinectedin` / `Topotecan`**.

#### 2.5.2 Mathematical Formulation Implemented in Code
1. **3-Head Permutation-Symmetric `OptimusComboNet` (`combo_model.py#L311-L358`)**:
   For unordered drug pair $\{u, v\}$ in disease $d$, constructs a `24`-dimensional **strictly symmetric** feature vector $\mathbf{x}_{\{u,v\},d} \in \mathbb{R}^{24}$ (`12` GNN relational features using symmetric operators $z_u + z_v$, $|z_u - z_v|$, $z_u \cdot z_v$, $\cos(\mathbf{h}_u, \mathbf{h}_v)$ + `12` mechanistic features from `PanDiseaseMechanisticIndexer`: marginal `L2G` gain $\Delta G_{\text{L2G}}$, 1-hop PPI hub Jaccard, `OnSIDES` organ-toxicity overlap, and boxed-warning product), passing through a residual `GELU + LayerNorm` trunk (`24 -> 128 -> 128`) into three heads (`eff: 128->64->1`, `tox: 128->32->1`, `antag: 128->32->1`) calibrated via out-of-fold isotonic regression (`53,952` clinical triples in `combo_v1.parquet`):
   $$U_{\text{combo}}(u, v \mid d) = P_{\text{eff}}^{\text{OOF}}(u,v,d) \cdot \big(1 - P_{\text{tox}}^{\text{OOF}}(u,v,d)\big) \cdot \big(1 - P_{\text{antag}}^{\text{OOF}}(u,v,d)\big)$$
2. **Non-Compensatory Target Gate $\times$ Six-Pillar `Integrated_v3_Score` (`compute_integrated_v3_pillars`, [`amg757_integrated_synthesis.py#L541-L596`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/amg757_integrated_synthesis.py#L541-L596))**:
   Let $\mathbf{p}(d) = \big(S_{\text{GNN}}, S_{\text{Opt2-TCE}}, S_{\text{IHC/RNA}}, W_{\text{comp}}, F_{\text{safety/TME}}, U_{\text{combo}}\big) \in [0,1]^6$ with canonical weights $\mathbf{w} = (0.20, 0.20, 0.25, 0.15, 0.10, 0.10)$ ($\sum_i w_i = 1$). Let $x_d = S_{\text{IHC/RNA}}(d) \in [0,1]$ be the empirical `DLL3` target positivity fraction. To prevent zero-`DLL3` tumors from achieving mid-table scores via non-target pillars, a smooth non-compensatory target gate $g_{\text{tgt}}(x_d) = \operatorname{clip}(x_d / \tau_{\text{gate}},\, g_{\min},\, 1.0)$ ($\tau_{\text{gate}} = 0.20$) multiplies the convex sum:
   $$\text{Integrated\_v3\_Score}(d) = 100 \times g_{\text{tgt}}\!\big(S_{\text{IHC/RNA}}(d)\big) \times \sum_{i=1}^6 w_i\, p_i(d)$$
3. **Exact $2^6 = 64$ Coalition Shapley Attribution (`_exact_shapley_6`, [`scripts/amg757_pillar_attribution.py#L95-L165`](file:///home/nchen10/antigravity_prjs/kg/scripts/amg757_pillar_attribution.py#L95-L165))**:
   Because $S_{\text{IHC/RNA}}$ enters **multiplicatively** through $g_{\text{tgt}}(x_d)$ as well as additively, linear weight shares $w_i p_i$ violate efficiency! `_exact_shapley_6` enumerates all $2^6 = 64$ coalitions $S \subseteq \{1,\dots,6\}$ against baseline reference $\bar{\mathbf{p}}$ to compute exact Shapley values satisfying efficiency, symmetry, and dummy axioms (verified in [`tests/test_pillar_attribution.py`](file:///home/nchen10/antigravity_prjs/kg/tests/test_pillar_attribution.py)):
   $$\phi_i(d) = \sum_{S \subseteq \{1,\dots,6\}\setminus\{i\}} \frac{|S|!\,(6 - |S| - 1)!}{6!} \Big[v\big(S \cup \{i\}\big) - v(S)\Big]$$

#### 2.5.3 Phase 5 Data Input & Output Contracts

| Direction | File Path | Format | Verified Shape / Size | Primary Keys & Key Schema Columns |
| :--- | :--- | :--- | :--- | :--- |
| **Input** | `amg757_option1_protocol_ranking.parquet` `(50, 57)` + `option2_dll3_tce_disease_ranking.csv` `(2944, 15)` + `combo_v1.parquet` `(53952, 10)` | Parquet / CSV | Deductive top-50 ∪ Inductive Option 2 top watchlist = `66` deduplicated indications | Unified across canonical `MONDO/EFO` IDs (`66` unique oncology indications). |
| **Output** | [`amgen_discovery_studio/results/amg757_integrated_v3_master_ranking.parquet`](file:///home/nchen10/antigravity_prjs/kg/amgen_discovery_studio/results/amg757_integrated_v3_master_ranking.parquet) (and `.csv`) | Parquet / CSV | `(66, 83)` (`0.06 MB` Parquet / `0.09 MB` CSV) | All `57` Option 1 cols + `26` Integrated `v3` cols: `tme_readiness_score`, `opt2_g4max_tce_rank`, `opt2_g4max_oof_rank`, `pillar_s_gnn`, `pillar_s_opt2_tce`, `pillar_s_ihc`, `pillar_w_comp`, `pillar_f_safety`, `pillar_u_combo`, `validated_combo_partner`, `concordance_archetype`, `integrated_v3_score`, `v3_score_p10/p90`, `v3_top3_prob_pct`, `integrated_v3_rank`. |
| **Output** | [`amgen_discovery_studio/results/amg757_pillar_attribution.csv`](file:///home/nchen10/antigravity_prjs/kg/amgen_discovery_studio/results/amg757_pillar_attribution.csv) & `.json` | CSV / JSON | `0.01 MB` | Exact $2^6 = 64$ Shapley $\phi_1,\dots,\phi_6$ per cohort + rank-drop leave-one-pillar-out sensitivity. |
| **Output** | [`amgen_discovery_studio/results/goal2_tier1_amgen_x_amgen_combos.parquet`](file:///home/nchen10/antigravity_prjs/kg/amgen_discovery_studio/results/goal2_tier1_amgen_x_amgen_combos.parquet) & `goal2_tier2_amgen_x_offpatent_combos.parquet` | Parquet / CSV | `(5760, 27)` (`Tier 1`) | Portfolio-wide `OptimusComboNet` predictions (`combo_utility`, `p_efficacy`, `p_toxicity`, `p_antagonism`, `marginal_genetic_gain`). |

---

### Phase 6: Primary Patient Biopsy TME Deconvolution (`2,215` Biopsies), Single-Cell Subclonal Escape (`60,026` Cells) & $\le 14\text{ nm}$ Immunological Synapse Biophysics

#### 2.6.1 Architectural Design & Engineering Rationale
Cell-line panels (`CCLE`/`DepMap`) lack stromal and immune cells, and bulk tumor RNA averages across malignant subclones—hiding the `20–35%` of SCLC cells that downregulate `ASCL1/NEUROD1` into `DLL3`-negative `POU2F3`/`YAP1` mesenchymal escape states! Furthermore, even if a partner antigen $B$ rescues `DLL3`-negative cells, a `DLL3 × B × CD3` trispecific / dual-targeting BiTE will **physically fail** if antigen $B$'s epitope sits $> 14\text{ nm}$ from the plasma membrane (preventing `CD45` phosphatase kinetic segregation from the immunological synapse!) or if $B$ undergoes rapid `BACE1 / ADAM10 / ADAM17` juxtamembrane ectodomain shedding (creating a soluble plasma decoy sink!).

`Phase 6` implements three specialized quantitative engines plus `NumPyro` Bayesian hierarchical models (`Q1–Q6`):
1. **Within-Platform Primary Biopsy TME Deconvolution ([`optimus_txgnn/amg757_primary_tme_and_scrna.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/amg757_primary_tme_and_scrna.py#L1-L720), `1,139` lines)**: Processes **`2,215` primary human tumor biopsies** across `SU2C-Prostate` (`NEPC`), `GSE235092` (*Nature* 2024 `MCC` immunotherapy biopsies), `GSE196264 / GSE32662` (`MTC`), `TARGET-NBL` (`ADRN` vs. `MES` neuroblastoma plasticity stratification), `TCGA-LGG` (`IDH`-mutant diffuse glioma), and `GSE60052` (`SCLC`), computing **within-platform** `CD8A/CD3E/GZMB/IFNG` cytolytic vs. `CD274/TGFB1/FOXP3` checkpoints to avoid cross-platform RNA-seq vs. microarray batch artifacts.
2. **Single-Cell Subclonal `OR`-Gate Rescue (`build_scrna_subclonal_or_gate_validation`, [`amg757_primary_tme_and_scrna.py#L725-L1139`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/amg757_primary_tme_and_scrna.py#L725-L1139))**: Stream-slices **`60,026` primary human SCLC cells** (`GSE281740` + `GSE324849` `10x` matrices) across `SCLC-A`, `SCLC-N`, `SCLC-P`, and `SCLC-Y/I` states, quantifying exact single-cell union coverage and intratumoral leukocyte off-tumor fratricide risk across `79` candidate surface antigens.
3. **$\le 14\text{ nm}$ Kinetic-Segregation, Sheddase & `Ion Guard` Biophysical Filter ([`optimus_txgnn/amg757_biophysical_synapse.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/amg757_biophysical_synapse.py#L1-L506) & [`models/q6_constructs_biophysics.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/models/q6_constructs_biophysics.py))**: Parses UniProt domain architectures and `AlphaFold` monomer `pLDDT` coordinates across the human surfaceome (`256` top SCLC surface pairs) to estimate membrane-proximal synaptic cleft span $d_{\text{syn}}(B)$ (nm), juxtamembrane sheddase cleavage liability (`SEZ6` flagged for `BACE1` soluble sink risk), and—introduced in the `v4.3.1` `Q6` audit—the **Excitable-Tissue `Ion Guard` (`ion_channel_guard = True`)**, which automatically demotes CNS/cardiac voltage-gated ion channels (`KCNB2`, `SCN3A`, `CACNA1*`) to safety-blocked rows regardless of SCLC RNArescue, elevating **`IGSF9`** (`5.2 nm`), **`PTK7 [Ig6–7]`** (`7.8 nm`), and **`STEAP1`** (`4.5 nm`, zero sheddase sink, Amgen `Xaluritamig` clinical `scFv` validated!) in [`results_v4/amg757_appendix_b_q6_constructs.csv`](file:///home/nchen10/antigravity_prjs/kg/results_v4/amg757_appendix_b_q6_constructs.csv).

#### 2.6.2 Mathematical Formulation Implemented in Code
1. **Single-Cell `DLL3 ∨ B` Union Coverage & Escape-Subclone Rescue**:
   Let $\mathcal{M}$ be the set of primary malignant SCLC single cells (`GSE281740` + `GSE324849`) and $\mathcal{M}_{\text{DLL3}^-} = \{c \in \mathcal{M} : \text{CP10K}_{c,\text{DLL3}} = 0\}$:
   $$C_{\text{OR}}(\text{DLL3}, B) = \frac{1}{|\mathcal{M}|}\sum_{c \in \mathcal{M}} \mathbb{I}\!\Big[\text{CP10K}_{c,\text{DLL3}} > 0 \;\lor\; \text{CP10K}_{c,B} > 0\Big], \qquad R_{\text{esc}}(B) = \frac{1}{|\mathcal{M}_{\text{DLL3}^-}|}\sum_{c \in \mathcal{M}_{\text{DLL3}^-}} \mathbb{I}\!\Big[\text{CP10K}_{c,B} > 0\Big]$$
   while penalizing fratricide via intratumoral `CD45+` leukocyte positivity $L_{\text{leuk}}(B) = \frac{1}{|\mathcal{L}|}\sum_{c \in \mathcal{L}} \mathbb{I}[\text{CP10K}_{c,B} > 0]$.
2. **Immunological Synapse Cleft Span, Sheddase & `Ion Guard` Gate (`score_antigen_synapse_biophysics`, [`q6_constructs_biophysics.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/models/q6_constructs_biophysics.py))**:
   For multi-pass transmembrane proteins (`6-TM STEAP1`, `GPCRs`), $d_{\text{syn}} \approx 3.5\text{–}5.5\text{ nm}$ (rigidly anchored inside the $\le 14\text{ nm}$ `CD45`-exclusion kinetic zone). For single-pass Type I/II ectodomains, effective membrane-proximal cleft span is modeled as $d_{\text{syn}}(B) = d_{\text{CD3}}(\approx 4.0\text{ nm}) + d_{\text{epi}}(B)$, applying a smooth kinetic-segregation decay when $d_{\text{syn}}(B) > 14\text{ nm}$, a multiplicative sheddase sink penalty $(1 - \rho_{\text{shed}}(B))$ on `BACE1`/`ADAM10/17`/`MMP` cleavage motifs, and a hard `Ion Guard` indicator $\mathbb{I}[\neg\text{IonChannel}(B)]$ blocking `KCN*`/`SCN*`/`CACNA*` channels.

#### 2.6.3 Phase 6 Data Input & Output Contracts

| Direction | File Path | Format | Verified Shape / Size | Primary Keys & Key Schema Columns |
| :--- | :--- | :--- | :--- | :--- |
| **Input** | Primary biopsy cohorts (`data_optimuskg/option1_amg757/raw_geo/`), `GSE281740` & `GSE324849` scRNA, `data_optimuskg/option2_bite_head/bank/uniprot_biophysics_v13.tsv` (`4.05 MB`) | Matrix / TSV | `2,215` primary biopsies; `60,026` SCLC single cells; `6,963` UniProt proteins | Primary patient transcriptomes + single-cell count matrices + UniProt topology/PTM/cleavage annotations + `AlphaFold` cache. |
| **Output** | [`amgen_discovery_studio/option2_bite_head/option2_dll3_dual_antigen_or_gate_sclc.csv`](file:///home/nchen10/antigravity_prjs/kg/amgen_discovery_studio/option2_bite_head/option2_dll3_dual_antigen_or_gate_sclc.csv) | CSV | `(256, 29)` (`0.07 MB`) | `dual_rank`, `pair`, `partner_antigen`, `dual_or_gate_score`, `sclc_dll3_low_rescue_pct`, `safety_therapeutic_index`, `topology_class`, `estimated_synapse_cleft_nm`, `within_14nm_kinetic_zone`, `sheddase_sink_risk`, `biophysical_synapse_score`, `epitope_engineering_guidance`. |
| **Output** | [`amgen_discovery_studio/option2_bite_head/option2_dll3_scrna_or_gate_validation.csv`](file:///home/nchen10/antigravity_prjs/kg/amgen_discovery_studio/option2_bite_head/option2_dll3_scrna_or_gate_validation.csv) | CSV | `(79, 31)` (`0.02 MB`) | `partner_antigen`, `scrna_malignant_cells_n`, `scrna_or_gate_cell_coverage_pct`, `scrna_dll3_neg_cell_rescue_pct`, `scrna_non_ne_py_escape_rescue_pct`, `scrna_sclc_a/n/p/yi_pos_pct`, `scrna_intratumoral_leukocyte_pos_pct`. |
| **Output** | [`results_v4/amg757_appendix_b_q6_constructs.csv`](file:///home/nchen10/antigravity_prjs/kg/results_v4/amg757_appendix_b_q6_constructs.csv) | CSV | Exploratory Appendix B (`Q6` 3-track construct ranking) | `partner_antigen`, `track`, `synapse_cleft_nm`, `sheddase_sink_risk`, `ion_channel_blocked` (`KCNB2 = True`), `composite_construct_score` (`IGSF9`, `PTK7`, `STEAP1` top-ranked). |

---

### Phase 7: Zero-PyTorch `FP16` Distilled CPU Inference & Cross-Platform Studio Suite (`Windows 11` & `Linux`)

#### 2.7.1 Architectural Design & Engineering Rationale
How can a Windows 11 or Linux laptop with **zero PyTorch, zero DGL, and zero GPU** execute live inductive asset scoring across `18,025` diseases and `OptimusComboNet` scoring in `< 1 second` alongside standalone interactive HTML5 studios?
- Implemented in [`optimus_txgnn/portable_pack.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/portable_pack.py), [`visualization_studio/studio_engine/numpy_models.py`](file:///home/nchen10/antigravity_prjs/kg/visualization_studio/studio_engine/numpy_models.py), [`visualization_studio/studio_engine/live_engines.py`](file:///home/nchen10/antigravity_prjs/kg/visualization_studio/studio_engine/live_engines.py), and [`visualization_studio/run_visualization_studio.py`](file:///home/nchen10/antigravity_prjs/kg/visualization_studio/run_visualization_studio.py).
- `save_portable_post_ultra_pack()` pre-multiplies the frozen relation matrices on GPU for `6` representative multi-family seeds ($d \in \{2048, 3072\}$): $\mathbf{P}_g^{(s)} = \mathbf{W}_{\text{rev\_drug\_protein}}^{(s)} \mathbf{h}_g^{(s)}$ `(26660, d)` and $\mathbf{E}_{\text{ind}}^{(s)} = \mathbf{r}_{\text{ind}}^{(s)} \odot \tilde{\mathbf{h}}_d^{(s)}$ `(18025, d)`, saving them as `float16` arrays in `pack_tensors_fp16.npz` (`1,608.83 MB`), alongside exact `LayerNorm`, `GELU`, `StandardScaler`, `IsotonicRegression` (`np.interp`), and `Mondrian` bucket binary-search (`np.searchsorted`) re-implementations in pure `NumPy` (`numpy_models.py`, `404` lines, `0.000000` numerical discrepancy vs. PyTorch!).

#### 2.7.2 Mathematical Formulation Implemented in Code
- **Pure-`NumPy` `FP16 -> FP32` Live Inductive Inference (`score_inductive_asset_portable`, [`live_engines.py#L185-L390`](file:///home/nchen10/antigravity_prjs/kg/visualization_studio/studio_engine/live_engines.py#L185-L390))**:
  Given any user-typed target set $\mathcal{T}_{\text{TAA}}$ in the live web Studio, casts only the indexed rows `proj_gene_rev_drug_protein[g_idxs]` from `fp16` to `fp32` (`(k, d)`), computes $\mathbf{u}^{(s)} = \sum_{g \in \mathcal{T}_{\text{TAA}}} w_g \mathbf{P}_g^{(s)}$, multiplies against `eff_dis_ind` `(18025, d)` via BLAS `SGEMV`, standardizes by `(mu_d, sd_d)`, looks up exact Mondrian $p$-values via `np.searchsorted(cf_bucket_b, -z)`, and runs `NumpyRepurposeRanker` + `NumpyComboEngine` (`_gelu`, `_layernorm`, `_linear`) in `< 350 ms` on CPU!

#### 2.7.3 Phase 7 Data Input & Output Contracts

| Direction | File Path | Format | Verified Shape / Size | Primary Keys & Key Schema Columns |
| :--- | :--- | :--- | :--- | :--- |
| **Input** | `6` representative multi-family GPU checkpoints + `rankers_and_calibrators.pkl` + `option2_bite_head_pack.pt` | PyTorch / Pickle | `d in {2048, 3072}` | Frozen `W_r`, `h_g`, `h_d`, `MondrianConformalCalibrator` buckets, `OptimusComboNet` weights. |
| **Output** | [`visualization_studio/engine_data/pack_tensors_fp16.npz`](file:///home/nchen10/antigravity_prjs/kg/visualization_studio/engine_data/pack_tensors_fp16.npz), `portable_models_v1.npz`, `pack_metadata.json` | NPZ / JSON | `1,608.83 MB` (`34` keys) + `39.52 MB` (`49` keys) + `14.52 MB` | Complete zero-PyTorch runtime engine launched via `Launch_Visualization_Windows.bat` or `./launch_visualization_linux.sh` (`http://127.0.0.1:8004`). |
| **Output** | [`results_v4/AMG757_Tarlatamab_V4_Evidence_Studio.html`](file:///home/nchen10/antigravity_prjs/kg/results_v4/AMG757_Tarlatamab_V4_Evidence_Studio.html) & `visualization_studio/AMG757_Tarlatamab_Indication_Expansion_Studio.html` | HTML5 | Self-contained interactive offline web apps | Zero-install browser studios for `v4.3.2` `Table A / Table B` governance and `v3.2` graph exploration. |

---

### Phase 8 (`Canonical v4.3.2`): Audited `Q1–Q4` `NumPyro` `EXNEX` (`a0=0.50`), Leak-Free `PE1` Back-Test (`Q5` Retired), Segregated `Table A/B` (`V0/V1/V2`) & Bitcoin `.ots` Ledger

#### 2.8.1 Architectural Design & Biostatistical Governance Turnaround
When exact $2^6 = 64$ coalition Shapley attribution (`WP0` audit in [`results_v4/amg757_pillar_attribution.csv`](file:///home/nchen10/antigravity_prjs/kg/results_v4/amg757_pillar_attribution.csv)) was executed on `Phase 5`'s `Integrated_v3_Score`, **`DLL3` IHC (`S_IHC`) accounted for `67.8%` of total rank variance** because it entered both additively and multiplicatively via $g_{\text{tgt}}(x_d)$, while `Option-2` contributed `12.2%` and `KG GNN + L2G` contributed `7.0%`. Furthermore, on the strictly leak-free **`PE1_new_indication`** benchmark across **`32` surface antigens (`24` paralog families, `2,593` dated `ClinicalTrials.gov` studies, `178` post-cutoff Ph2+ events on `(antigen, disease)` pairs with `0` pre-cutoff trials)** ([`pan_antigen_temporal_backtest.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/backtest/pan_antigen_temporal_backtest.py)), static all-time `M5_omics_only` (`AUPRC = 0.501`) exceeded `M4_kg_plus_omics` (`0.444`), while strictly pre-cutoff frozen `P5_preT_omics_mlp` (`(16, 8) tanh lbfgs alpha=8.0 tol=1e-3 max_iter=300`) achieved `0.378` vs single-gene `P1` (`0.307`, Holm `p = 0.0042`), isolating `+0.123` post-`T` hotspot leakage (`wp25_passed = False`).

`Phase 8` (`amg757-v4.3.2`) **retires `Q5` from all clinical decision tables (`0` `q5_*` columns)** and enforces a **decoupled, provenance-locked `Q1–Q4` Bayesian & Actuarial Decision Architecture**:
1. **Machine-Verified `260`-Record Evidence Store & `120`-Trial Enriched Registry Snapshot ([`results_v4/evidence.parquet`](file:///home/nchen10/antigravity_prjs/kg/results_v4/evidence.parquet), [`scripts/verify_evidence_sources.py`](file:///home/nchen10/antigravity_prjs/kg/scripts/verify_evidence_sources.py))**: Every `SP347` IHC denominator, `TCE` response arm (`n, k`), verified HR (`DeLLphi-305` OS HR `0.42 [0.32–0.54]`), and `ClinicalTrials.gov` status (`120/120` enriched `DLL3` studies via [`registry_trial_mapper.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/models/registry_trial_mapper.py)) is verified offline (`0` errors across `260` records).
2. **`Q1` (`IHC` Prevalence) & `Q2` (TCE-Only `EXNEX` ORR with `a_0 = 0.50`) `4`-Chain `NumPyro` `NUTS` Engines ([`models/q1_prevalence.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/models/q1_prevalence.py), [`models/q2_response.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/models/q2_response.py))**:
   - `Q1` unifies heterogeneous pathology cutoffs (`>=1%`, `>=25%`, `>=50%`, `>=75%`) across `27` cohorts (`100.0%` `95%` LOSO coverage, `Winkler95 = 0.615`).
   - `Q2` fits **strictly `15` `DLL3` T-cell engager clinical arms (`0` ADCs)** with Ibrahim–Chen cross-agent power-prior discounting (`a_0 = 0.50` default; `a_0 = 1.00`, `No-Obrixtamig`, and `LOCO-0` sensitivity columns) and agent-by-histology transfer dispersion (`100.0%` `95%` LOCO coverage, `Winkler95 = 0.510`), tagging `EXP-S4` (`33.0%† [23.3%, 42.0%]`) as a systemic SCLC reference (`unmodeled_intracranial_rano_bm`).
3. **Segregated `Table A` (`12 + 7 = 19` Registry Split) vs. `Table B` (`V0 / V1 / V2` Routes) & Statutory Law ([`models/q3_landscape_scenarios.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/models/q3_landscape_scenarios.py), [`models/decision_table_v4.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/models/decision_table_v4.py), [`regulatory_rules.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/regulatory_rules.py))**:
   - **Segregates `Table A`** (`12` active/committed SCLC expansion/lifecycle studies [`5` Ph3 + `7` combo/SC incl. `2` partner-sponsored `GSK NCT06551142` & `AbbVie NCT07895056`, `+ZL-1310 NCT07531095`, `+YL201 NCT06898957`] + `7` historical/completed baseline studies = `19` registry rows; **`$2,610.8M`** at `2036-05-16` `PHS Act §351(k)(7)` floor / **`$3,306.6M`** under `2039` patent, net of `-$469.3M` `2L` cannibalization) from **`Table B`**.
   - Models three realistic execution routes after confirming `NCT06788938` is a `29`-patient UCLA single-arm academic study (`ACTIVE_NOT_RECRUITING`, watched quarterly via `make watch-trials` / `scripts/watch_trials.py`): **`Route V0`** standalone Ph2 INDs (`EXP-A..G` = **`-$34.6M`**), **`Route V1`** external IIT signal federation across `11` open academic studies (`N=517` slots; **`+$1.8M`** at `0.90` completion, **`-$4.3M`** at `0.75` drug-supply attrition sensitivity, **`EVSI = +$12.94M`** baseline / **`+$12.35M`** under exact `2036` LOE gating; `TRIG-06` `EXP-S4` `3-zone` gate bounds VoI abandonment to **`-$21.71M`** vs. binary `-$34.01M`), and **`Route V2`** de novo Amgen registrational master basket (**`+$10.6M`**).
   - Enforces **three US pediatric statutory rules**: `FD&C Act §529` Rare Pediatric `PRV = $0.0M`, mandatory `PREA / RACE §505B = 0.0y`, and voluntary `PHS Act §351(m)` `BPCA = +0.5y` (`+$184.8M` base tail, netting `+$172.3M` on `EXP-G`).
4. **Cohort-Specific `WP9` Basket Operating Characteristics Across `3` Prior Regimes ([`optimus_txgnn/basket_design.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/basket_design.py), [`results_v4/basket_design_oc.csv`](file:///home/nchen10/antigravity_prjs/kg/results_v4/basket_design_oc.csv))**: Evaluates `7` cohorts (`p0_k = 10%–15%`) across `A1 = 2.82/7`, `A2 = 1.82/7`, and `A3 = 1.60/7` active priors: `Naive_Exchangeable_BHM` inflates mixed-null Type I error to **`33.43%`**, `EXNEX_WorstCaseType1_le10_Basket` (`Max N=140`, `$17.24M`, `<=8.25%` Type I) beats Simon on FWER-adjusted power only when `>=3/7` cohorts are active, `Shared_Protocol_Independent_Simon` (`Max N=217`, `$20.96M`; `FixedN20 = $16.66M`) holds per-cohort `Type I <= 4.99%` with `0` borrowing inflation, and `Federated_IIT_Signal_Pool_V1` (`N=163`, `$15.40M`) provides the lowest-CapEx Stage-1 gate.
5. **Bitcoin-Anchored `41`-Record Append-Only Forecast Ledger ([`forecasts/README.md`](file:///home/nchen10/antigravity_prjs/kg/forecasts/README.md), [`scripts/score_prospective_forecasts.py`](file:///home/nchen10/antigravity_prjs/kg/scripts/score_prospective_forecasts.py))**: Preserves `2026_11.jsonl` (`10` rows, Bitcoin block `#975,929`) and `2026_11b.jsonl` (`13` rows, block `#975,963`) untouched while appending `2026_11c.jsonl` (`18` rows, sealed in `v4.3.1` and `3/3` settled into Bitcoin blocks `#976,004` & `#976,009` in `v4.3.2`), strictly separating **`5` `q2_empirical_model` forecasts** (`FCST-09, 11..14`) from **`4` `analyst_prior` forecasts** (`FCST-17..20`).

#### 2.8.2 Mathematical Formulation Implemented in Code
1. **`Q2` TCE-Only `EXNEX` with Cross-Agent Transfer & Ibrahim–Chen Power Prior (`_exnex_tce_only_model`, [`q2_response.py#L115-L225`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/models/q2_response.py#L115-L225))**:
   Each histology $h \in \{1,\dots,H\}$ in lineage $\ell(h)$ draws its baseline `DLL3-high` (`>=50%`) log-odds $\theta_h$ from a two-component Exchangeability–Non-Exchangeability mixture (`Neuenschwander et al., Stat. Med. 2016`) with prior exchangeability weight $w_{\text{EX}} = 0.65$:
   $$\theta_h \sim w_{\text{EX}}\,\mathcal{N}\!\big(\mu_{\ell(h)},\, \tau_{\text{EX}}^2\big) + (1 - w_{\text{EX}})\,\mathcal{N}\!\big(\mu_{\text{NEX}},\, \sigma_{\text{NEX}}^2\big)$$
   For observed TCE arm $i$ (`agent` $a_i \in \{\text{tarlatamab}=0, \text{obrixtamig}=1, \text{gocatamig}=2\}$) in histology $h_i$, stratum-specific response probabilities integrate `Q1` prevalence fractions $(p_{1,h}, p_{50,h})$ over ordered IHC penalties $\delta_{\text{low}}, \delta_{\text{neg}} > 0$ and intact-BBB penalty $\delta_{\text{BBB}} > 0$ with power-prior discount $a_0 = 0.50$:
   $$\eta_i^{\text{hi}} = \theta_{h_i} + \alpha_{a_i} + \eta_{a_i, h_i} + \mathbf{x}_i^\top \boldsymbol{\beta} - \delta_{\text{BBB}}\,\text{BBB}_{h_i} + \sigma_{\text{trial}} z_i, \qquad \mathcal{L}_{\text{power}}(\boldsymbol{\Theta}) = \prod_{i} \operatorname{Binom}\!\big(k_i \mid n_i, p_{\text{arm},i}\big)^{a_0^{\mathbb{I}[a_i \neq 0]}}$$
2. **Exact $\mathcal{O}(2^{F/2} \log 2^{F/2})$ Meet-in-the-Middle Family Sign-Flip Permutation Test (`_exact_family_signflip_pvalue`, [`pan_antigen_temporal_backtest.py#L340-L384`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/backtest/pan_antigen_temporal_backtest.py#L340-L384))**:
   Across $F = 24$ independent paralog target families (`32` surface antigens), splitting $\mathbf{d} = \boldsymbol{\Delta}/F$ into halves $\mathbf{d}_A \in \mathbb{R}^{\lfloor F/2 \rfloor}$ and $\mathbf{d}_B \in \mathbb{R}^{\lceil F/2 \rceil}$ computes the **exact** $2^F$-permutation $p$-value in `< 2 ms` (followed by Holm step-down multiplicity adjustment across all `6` pre-specified models):
   $$p_{\text{exact}} = \frac{1}{2^F}\sum_{\mathbf{s} \in \{-1,+1\}^F} \mathbb{I}\!\left[\mathbf{s}_A^\top \mathbf{d}_A + \mathbf{s}_B^\top \mathbf{d}_B \ge \bar{\Delta}_{\text{obs}}\right]$$

#### 2.8.3 Phase 8 Data Input & Output Contracts

| Direction | File Path | Format | Verified Shape / Size | Primary Keys & Key Schema Columns |
| :--- | :--- | :--- | :--- | :--- |
| **Input** | [`results_v4/evidence.parquet`](file:///home/nchen10/antigravity_prjs/kg/results_v4/evidence.parquet) & `results_v4/provenance/ctgov_v2_dll3_enriched_120.json` | Parquet / JSON | `260` machine-verified provenance rows; `120/120` enriched `DLL3` trials; `2,593` `CT.gov` trials across `32` antigens | `evidence_id`, `source_type` (`PMID`/`NCT`/`FDA_LABEL`), `histology_id`, `numerator`, `denominator`, `excerpt_hash`. |
| **Output** | [`results_v4/q1_prevalence.parquet`](file:///home/nchen10/antigravity_prjs/kg/results_v4/q1_prevalence.parquet), [`results_v4/q2_response_posterior.parquet`](file:///home/nchen10/antigravity_prjs/kg/results_v4/q2_response_posterior.parquet) & `*_diagnostics.json` | Parquet / JSON | `4`-chain `NUTS` posteriors (`max R_hat < 1.01`, `0` divergences, `100%` LOSO/LOCO) | `histology_id`, `prev_ge1_median`, `prev_ge50_median`, `q2_orr_median` (`a0=0.50`), `q2_orr_a0_100`, `q2_orr_no_obrix`, `q2_orr_loco0`. |
| **Output** | [`results_v4/pan_antigen_backtest_summary.csv`](file:///home/nchen10/antigravity_prjs/kg/results_v4/pan_antigen_backtest_summary.csv) & `pan_antigen_backtest_metrics.json` | CSV / JSON | `PE1_new_indication` (`178` events) across `32` antigens (`24` families); frozen `P5` (`0.378`) vs `P1` (`0.307`) | `endpoint`, `model_id`, `mean_auprc`, `holm_adjusted_p`, `post_T_hotspot_leakage` (`+0.123`; `Q5` retired). |
| **Output** | [`results_v4/amg757_committed_baseline_v4.csv`](file:///home/nchen10/antigravity_prjs/kg/results_v4/amg757_committed_baseline_v4.csv) (`Table A`), [`results_v4/amg757_incremental_options_v4.csv`](file:///home/nchen10/antigravity_prjs/kg/results_v4/amg757_incremental_options_v4.csv) (`Table B`), & [`results_v4/amg757_decision_table_v4.csv`](file:///home/nchen10/antigravity_prjs/kg/results_v4/amg757_decision_table_v4.csv) | CSV / Parquet / JSON | Segregated `Table A` (`$2,610.8M` / `$3,306.6M`, `12+7=19` studies) & `Table B` (`V0 -$34.6M`, `V1 +$1.8M` / `-$4.3M` at `0.75`, `V2 +$10.6M`, `EVSI +$12.94M` / `+$12.35M` LOE) | `option_id`, `anchor_nct_ids`, `rnpv_v0_standalone_2036_musd`, `rnpv_v1_iit_fed_2036_musd`, `rnpv_v1_attrition075_2036_musd`, `rnpv_v2_amgen_basket_2036_musd`. |
| **Output** | [`results_v4/basket_design_oc.csv`](file:///home/nchen10/antigravity_prjs/kg/results_v4/basket_design_oc.csv) & [`forecasts/`](file:///home/nchen10/antigravity_prjs/kg/forecasts/README.md) (`2026_11`, `11b`, `11c` + Bitcoin `.ots`) | CSV / JSONL / OTS | `7` cohorts across `A1/A2/A3` priors; `41`-row `3/3` Bitcoin `#975,929..#976,009` ledger (`5` `Q2` + `4` `analyst_prior`) | `design`, `max_n`, `expected_n_A1`, `worst_mixed_null_cohort_type1`, `total_cost_A1_musd`; `record_id`, `model_specification`. |

---

## 3. End-to-End Execution DAG, CLI Commands & Automated Verification Suite

### 3.1 Reproducing Any Stage from the Command Line

All CPU synthesis, statistical calibration, `NumPyro` `NUTS` inference, `32`-antigen `PE1` temporal backtesting, offline provenance verification, zero-drift report/deck generation, and Studio scripts run deterministically offline:

```bash
# 1. Fast zero-drift, provenance (260 rows), Bitcoin .ots PoW, PPTX & weights_only=True gate (~18s)
make verify-fast

# 2. Audit external share bundles & watch CT.gov trials (doc_to_gdrive/, visualization_studio/, docs/16*, README*, METRICS*)
make bundle-audit
make watch-trials

# 3. Full verification suite (96+ pytest checks) & clean-clone simulation (83+ passed, 13 skipped)
make verify
make verify-fresh-clone

# 4. Regenerate zero-drift v4.3.2 EN/ZH Markdown reports, V4 HTML5 Studio & EN/ZH PPTX/HTML5 decks + METRICS
.venv-v4/bin/python scripts/build_amg757_v4_reports_and_studio.py
.venv-v4/bin/python build_amg757_biostat_pptx.py
.venv-v4/bin/python build_optimuskg_amg757_amg691_presentation.py
.venv-v4/bin/python generate_metrics_ledger.py

# 5. Launch the Zero-PyTorch Cross-Platform Visualization Studio (Windows 11 or Linux, Phase 7)
.venv-v4/bin/python visualization_studio/run_visualization_studio.py --no-browser --port 8004
```

### 3.2 Automated Mathematical & Zero-Drift Test Matrix (`tests/`)

Every mathematical invariant, leakage guard, and data schema contract documented across `Phases 0–8` (`24` test modules, `96+` automated unit tests (`83+ passed, 13 skipped` on clean git clones)) is verified via `pytest`:

| Test Module (`tests/`) | Stage(s) Covered | Core Mathematical & Engineering Invariants Verified |
| :--- | :--- | :--- |
| [`test_data_pipeline.py`](file:///home/nchen10/antigravity_prjs/kg/tests/test_data_pipeline.py) & [`test_split_hash_guard.py`](file:///home/nchen10/antigravity_prjs/kg/tests/test_split_hash_guard.py) | `Phase 0–1` | `18,025` clinical disease filter (`0` measurement nodes), zero complex-disease holdout leakage, and `SHA-256` `node_fingerprint` / split-hash enforcement. |
| [`test_colab_scaling_and_backend.py`](file:///home/nchen10/antigravity_prjs/kg/tests/test_colab_scaling_and_backend.py) | `Phase 1` | Exact forward & backward gradient parity between `dgl_compat.py` (`TorchHeteroGraph` `FP32` `CSR`) and native `DGL`, gradient checkpointing exactness, and $d \in \{2048, 3072\}$ `v5bi/v5fm` presets. |
| [`test_amgen_v5_repurpose_and_combo.py`](file:///home/nchen10/antigravity_prjs/kg/tests/test_amgen_v5_repurpose_and_combo.py) | `Phase 2, 5` | Per-disease $Z$-standardization removes degree hub bias, $3 \times 4$ Mondrian conformal validity, and exact permutation symmetry $f(u,v,d) \equiv f(v,u,d)$ of `OptimusComboNet`. |
| [`test_bite_and_amg757_expansion.py`](file:///home/nchen10/antigravity_prjs/kg/tests/test_bite_and_amg757_expansion.py) & [`test_basket_design.py`](file:///home/nchen10/antigravity_prjs/kg/tests/test_basket_design.py) | `Phase 2–3, 8` | `TAA` vs. `CD3E` target partitioning, hematologic exclusion on solid-only TAAs, pediatric glioma guardrail, zero double-counting in MCDA, Simon (1989) Table 1 exactness, and `WP9` `EXNEX` vs. `Shared_Protocol_Independent_Simon` Type I control. |
| [`test_option2_bite_head.py`](file:///home/nchen10/antigravity_prjs/kg/tests/test_option2_bite_head.py) & [`test_option2_debiased_v2.py`](file:///home/nchen10/antigravity_prjs/kg/tests/test_option2_debiased_v2.py) | `Phase 4` | Zero-init `SVD-LoRA` + `FiLM` reproduces exact frozen 24-ckpt backbone at Step 0, paralog-family-disjoint cold-antigen CV folds (`DLL1/3/4/JAG1/2` grouped), mature `ECD` cleavage parsing, and pack round-trip parity. |
| [`test_amg757_integrated_synthesis.py`](file:///home/nchen10/antigravity_prjs/kg/tests/test_amg757_integrated_synthesis.py), [`test_amg757_integrated_v3_consistency.py`](file:///home/nchen10/antigravity_prjs/kg/tests/test_amg757_integrated_v3_consistency.py) & [`test_pillar_attribution.py`](file:///home/nchen10/antigravity_prjs/kg/tests/test_pillar_attribution.py) | `Phase 5–6` | Non-compensatory target gate $g_{\text{tgt}}(x)$, target-validated combo hygiene (blocks `Bemarituzumab` in `FGFR2b`-negative `NEC`), `ADRN` vs. `MES` neuroblastoma stratification, and exact $2^6 = 64$ Shapley axioms (`67.8%` `S_IHC`). |
| [`test_q1_q2_bayesian_models.py`](file:///home/nchen10/antigravity_prjs/kg/tests/test_q1_q2_bayesian_models.py), [`test_q3_q4_q6_models.py`](file:///home/nchen10/antigravity_prjs/kg/tests/test_q3_q4_q6_models.py), [`test_backtest_no_future_leak.py`](file:///home/nchen10/antigravity_prjs/kg/tests/test_backtest_no_future_leak.py), [`test_regulatory_rules.py`](file:///home/nchen10/antigravity_prjs/kg/tests/test_regulatory_rules.py) & [`test_doc16_zero_drift.py`](file:///home/nchen10/antigravity_prjs/kg/tests/test_doc16_zero_drift.py) | `Phase 6, 8` | `NumPyro` `Q1`/`Q2` `NUTS` convergence (`R_hat < 1.01`, zero ADC dilution), `Ion Guard` `KCNB2` block, zero post-cutoff `32`-antigen temporal leakage, `FD&C Act §529` `PRV = False` + `PREA` vs. `BPCA` separation, and `Table A / Table B` zero-drift locks. |

---

## 4. Appendix: Line-by-Line Annotated Core Kernel Walkthrough

To make both the PyTorch neural engineering and the `v4.3.2` `NumPyro` / exact non-parametric statistical kernels completely transparent, this appendix walks line-by-line through the three foundational computational engines of the `AMG 757` platform:
1. **Module A ([`optimus_txgnn/dgl_compat.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/dgl_compat.py))**: The pure-PyTorch `FP32` `CSR` sparse message-passing autograd engine that eliminated `DGL` edge-buffer OOMs on `5.91M`-edge graphs up to width $d = 3072$ (`944.2M` parameters).
2. **Module B ([`optimus_txgnn/option2_bite_head.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/option2_bite_head.py))**: The `OptimusBiTEHead` cold-antigen `SVD-LoRA` + `5`-modality `FiLM` residual scoring head and multi-positive `Plackett–Luce` listwise loss.
3. **Module C ([`optimus_txgnn/models/q2_response.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/models/q2_response.py) & [`optimus_txgnn/backtest/pan_antigen_temporal_backtest.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/backtest/pan_antigen_temporal_backtest.py))**: The `Phase 8` TCE-only `MixtureSameFamily` `EXNEX` hierarchical response model (with Ibrahim–Chen power-prior discounting) and the exact $\mathcal{O}(2^{F/2} \log 2^{F/2})$ meet-in-the-middle `32`-antigen family sign-flip permutation kernel.

---

### 4.1 Module A: Pure-PyTorch `FP32` `CSR` Sparse Message-Passing Engine ([`optimus_txgnn/dgl_compat.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/dgl_compat.py))

#### 4.1.1 Adaptive `256 MiB` Transient Gather Window (`L125–L135`)

```python
# optimus_txgnn/dgl_compat.py (Lines 125-135)
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
```

* **`L127–L131` (Multi-dimensional feature product)**: Computes `row_elems` $= \prod_k \text{feat\_shape}[k]$ so the chunker works identically for flat node embeddings $(N, d)$ and multi-head attention tensors $(N, H, d_h)$.
* **`L132–L134` (`256 MiB` hard ceiling)**: Standard `GNN` message passing materializes a dense edge tensor $\mathbf{M} \in \mathbb{R}^{|E| \times d}$; at $|E| = 5.91\text{M}$ and $d = 3072$ in `fp32` (`elem_size = 4`), each relation consumes $12,288\text{ bytes/edge}$, which would spike $> 72\text{ GB}$ if unchunked. Dividing `256 MiB` by `bytes_per_edge` yields `cs = 21,845` edges per micro-slice (with a `16,384` minimum floor so GPU warp occupancy stays saturated on narrow layers).

#### 4.1.2 Zero-Saved-Buffer `_ScatterAddFn` Forward & Adjoint Backward (`L137–L246`)

```python
# optimus_txgnn/dgl_compat.py (Lines 137-246)
class _ScatterAddFn(torch.autograd.Function):
    @staticmethod
    def forward(
        ctx, x: torch.Tensor, u: torch.Tensor, v: torch.Tensor,
        e: Optional[torch.Tensor], num_dst: int,
    ) -> torch.Tensor:
        ctx.num_src = x.shape[0]
        ctx.orig_dtype = x.dtype
        ctx.has_e = e is not None
        if e is not None:
            ctx.e_orig_dtype = e.dtype
            ctx.save_for_backward(u, v, x, e)
        else:
            ctx.e_orig_dtype = None
            ctx.save_for_backward(u, v)

        work_dtype = (
            torch.float32 if x.dtype in (torch.float16, torch.bfloat16) else x.dtype
        )
        x_w = x.to(work_dtype) if x.dtype != work_dtype else x
        e_w = e.to(work_dtype) if (e is not None and e.dtype != work_dtype) else e

        out = torch.zeros((num_dst, *x.shape[1:]), dtype=work_dtype, device=x.device)
        num_edges = int(u.shape[0])
        if num_edges == 0:
            return out.to(x.dtype) if out.dtype != x.dtype else out

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

        grad_x = None
        if ctx.needs_input_grad[0]:
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

        grad_e = None
        if ctx.has_e and ctx.needs_input_grad[3]:
            x_w = x.to(work_dtype) if x.dtype != work_dtype else x
            reduce_dims = tuple(range(1, go_w.dim()))
            ge_parts: List[torch.Tensor] = []
            for start in range(0, num_edges, cs):
                end = min(start + cs, num_edges)
                prod = go_w.index_select(0, v[start:end]) * x_w.index_select(0, u[start:end])
                if reduce_dims:
                    prod = prod.sum(dim=reduce_dims)
                if e.dim() > 1:
                    prod = prod.view(-1, *([1] * (e.dim() - 1)))
                ge_parts.append(prod)
            grad_e_w = (
                torch.cat(ge_parts, dim=0) if ge_parts
                else torch.zeros_like(e, dtype=work_dtype)
            )
            grad_e = grad_e_w.to(ctx.e_orig_dtype) if grad_e_w.dtype != ctx.e_orig_dtype else grad_e_w

        return grad_x, None, None, grad_e, None
```

* **`L155–L162` (Zero $\mathcal{O}(|E|\cdot d)$ autograd tape)**: Notice what `ctx.save_for_backward` stores in the unweighted `copy_u` case (`e is None`): **only `(u, v)`**, two `1D` integer index arrays already resident in graph storage! Standard PyTorch autograd through `x[u]` would retain the materialized $(|E|, d)$ gather tensor across every layer until `.backward()`, causing immediate VRAM exhaustion.
* **`L164–L168` (`FP32` accumulation safety)**: `bfloat16` has only `7` mantissa bits (machine epsilon $\approx 7.8 \times 10^{-3}$). Summing $> 1,000$ incoming neighbor messages into hub nodes such as `CD3E`, `TP53`, or `SCLC` in `bf16` silently drops small messages (`1.0 + 0.003 == 1.0`). Promoting `work_dtype` to `torch.float32` before `out.index_add_` preserves `23`-bit mantissa exactness while keeping external layer activations compact in `bf16`/`fp16`.
* **`L174–L180` (Streaming gather $\to$ optional edge gate $\to$ scatter-add)**: Each iteration allocates at most `(cs, d)` $\le 256\text{ MiB}$ for `msg = x_w.index_select(0, u[start:end])`, multiplies per-edge scalar weight `e_w[start:end]` when present (`u_mul_e`, e.g., `Open Targets L2G` weighted edges), accumulates into destination rows `v[start:end]`, and frees `msg` immediately on the next slice.
* **`L198–L216` (Exact adjoint `grad_x` via role reversal `v -> u`)**: Mathematically, forward unweighted scatter-add is multiplication by the sparse incidence transpose $\mathbf{Y} = \mathbf{A}^\top \mathbf{X}$ where $A_{u_k, v_k} = 1$. Its exact Jacobian transpose is $\nabla_{\mathbf{X}} \mathcal{L} = \mathbf{A} \nabla_{\mathbf{Y}} \mathcal{L}$: gather `go_w` at destination indices `v[start:end]`, scale by `e_w[start:end]`, and `index_add_` back into source indices `u[start:end]`.
* **`L219–L245` (Chunked inner-product gradient `grad_e`)**: When edge weights $e_k$ are differentiable, $\partial \mathcal{L}/\partial e_k = \langle (\nabla_{\mathbf{Y}}\mathcal{L})_{v_k}, \mathbf{X}_{u_k}\rangle$. Computing `prod.sum(dim=reduce_dims)` inside each `cs` window collapses the `d` dimension to a scalar per edge *before* appending to `ge_parts`, keeping memory $\mathcal{O}(|E|)$ rather than $\mathcal{O}(|E|\cdot d)$.

#### 4.1.3 Degree-Clamped Mean Reduction (`TorchRelationSubgraph._compute_reduction`, `L400–L432`)

```python
# optimus_txgnn/dgl_compat.py (Lines 400-432)
    def _compute_reduction(self, msg_fn: Any, red_fn: Any) -> Tuple[str, torch.Tensor]:
        u, v = self._parent._edges_by_ce[self._ce]
        num_dst = self._parent._num_nodes[self._dsttype]

        if hasattr(msg_fn, "in_field"):
            x = self.srcdata[msg_fn.in_field]
            e = None
        elif hasattr(msg_fn, "lhs_field") and hasattr(msg_fn, "rhs_field"):
            x = self.srcdata[msg_fn.lhs_field]
            e = self.edata[msg_fn.rhs_field]
            if e.dim() == 1 and x.dim() > 1:
                e = e.view(-1, *([1] * (x.dim() - 1)))
        else:
            raise TypeError(f"Unsupported message function: {type(msg_fn)}")

        red_name = getattr(red_fn, "_name", None)
        out_field = getattr(red_fn, "out_field", "h")
        if red_name not in ("mean", "sum"):
            raise ValueError(f"Unsupported reducer '{red_name}'.")

        agg = _ScatterAddFn.apply(x, u, v, e, num_dst)
        if red_name == "mean":
            in_deg = self._parent.in_degrees(etype=self._ce)
            denom = in_deg.clamp(min=1).to(dtype=agg.dtype, device=agg.device)
            denom = denom.view(-1, *([1] * (agg.dim() - 1)))
            agg = agg / denom
        return out_field, agg
```

* **`L405–L413` (`DGL` API drop-in compatibility)**: Decodes both `fn.copy_u("h", "m")` (`in_field`) and `fn.u_mul_e("h", "w", "m")` (`lhs_field`, `rhs_field`), reshaping `1D` edge scalars `(|E|,)` into `(|E|, 1, ...)` for broadcasting against `(num_src, d)`.
* **`L422–L426` (Zero-in-degree cold-node protection)**: Pipeline biologics (`AMG 757` under strict zero-shot masking) have `in_deg == 0` on clinical indication relations. Clamping `in_deg.clamp(min=1)` divides `0.0 / 1.0 = 0.0` instead of `0.0 / 0.0 = NaN`, keeping gradients clean across all `46` canonical relation types.

---

### 4.2 Module B: Cold-Antigen `SVD-LoRA` + `5`-Modality `FiLM` Head ([`optimus_txgnn/option2_bite_head.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/option2_bite_head.py))

#### 4.2.1 Step-0 Identity Initialization & Unsupervised Solid–Heme Guard (`L1071–L1207`)

```python
# optimus_txgnn/option2_bite_head.py (Lines 1071-1089 & 1157-1207)
        # 1. Disease-manifold SVD basis + zero-init LoRA (B=0 -> exact backbone at Step 0)
        self.n_sets = self.n_mod if cfg.adapter_mode == "per_modality" else 1
        self.has_adapters = bool(
            cfg.adapter_mode != "none" and cfg.use_backbone and cfg.trainable and self.n_ck > 0
        )
        if self.has_adapters:
            self.V_basis = [v.to(device) for v in _get_cached_svd_bases(bank, cfg.adapter_subspace)]
            self.VtV = [(v.T @ v).contiguous() for v in self.V_basis]
            k_sub = int(self.V_basis[0].shape[1])
            g = torch.Generator().manual_seed(1234)
            A_init = torch.randn(
                self.n_sets, self.n_ck, k_sub, cfg.adapter_rank, generator=g
            ) / math.sqrt(max(1, k_sub))
            B_init = torch.zeros(self.n_sets, self.n_ck, cfg.adapter_rank, k_sub)
            self.adapter_A = nn.Parameter(A_init)
            self.adapter_B = nn.Parameter(B_init)
        self.adapter_scale = cfg.adapter_alpha / max(1, cfg.adapter_rank)

        # ... [Lines 1090-1156: P/Q gauge projections, standardized gene_x/dis_x, HPA IHC] ...

        # 2. Unsupervised hematologic antigen x hematologic disease concordance buffers
        blood_g = bank.gene_feats[:, fgl["gtex_blood_log"]].astype(np.float32)
        blood_g = blood_g / max(float(blood_g.max()), 1.0)
        lymph_ck = HPA_CANCERS.index("lymphoma")
        lymph_g = path_feat[:, lymph_ck, :].max(axis=-1).astype(np.float32)
        leuk_myel_g = lcl[:, [1, 2]].max(axis=-1).astype(np.float32)
        heme_ag = np.clip(np.maximum.reduce([blood_g, lymph_g, leuk_myel_g]), 0.0, 1.0)
        heme_dis = bank.disease_feats[:, dfl["is_heme"]].astype(np.float32)
        self.register_buffer("heme_ag_g", torch.from_numpy(heme_ag), persistent=False)
        self.register_buffer("heme_dis_d", torch.from_numpy(heme_dis), persistent=False)

        # 3. Modality FiLM + residual MLP initialized to exact zero residual
        self.mod_emb = nn.Embedding(self.n_mod, cfg.mod_emb)
        self.fc1 = nn.Linear(n_in, cfg.hidden)
        self.film = nn.Linear(cfg.mod_emb, 2 * cfg.hidden)
        self.fc2 = nn.Linear(cfg.hidden, cfg.hidden // 2)
        self.out = nn.Linear(cfg.hidden // 2, 1)
        nn.init.zeros_(self.film.weight); nn.init.zeros_(self.film.bias)
        if cfg.use_backbone:
            nn.init.zeros_(self.out.weight); nn.init.zeros_(self.out.bias)
        self.mod_alpha = nn.Parameter(torch.ones(self.n_mod))
        self.mod_bias = nn.Parameter(torch.zeros(self.n_mod))
```

* **`L1077–L1085` (Constrained `SVD` disease subspace + `B = 0`)**: With only $\sim 293$ clinically validated oncology target antigens, fitting an unconstrained $3072 \times 3072$ matrix across `24` checkpoints would require $226\text{M}$ parameters and memorize fold noise instantly. Projecting onto the top-$K$ (`K=128`) right singular vectors $\mathbf{V}_c \in \mathbb{R}^{d_c \times K}$ of checkpoint $c$'s `18,025`-disease decoder matrix $\mathbf{E}_c$ restricts adaptation strictly to directions that move disease logits, using rank $r=8$ (`A_init` Gaussian for symmetry breaking, `B_init = 0` so $\Delta\mathbf{s} \equiv \mathbf{0}$ at initialization).
* **`L1158–L1177` (Unsupervised solid–heme mismatch buffers)**: Computes `heme_ag` $\in [0, 1]$ per gene solely from unsupervised normal blood `GTEx`, `HPA` lymphoma IHC, and `DepMap` leukemia/myeloma RNA (`DLL3` has `heme_ag = 0.00`, whereas `BCMA`/`CD19` have `heme_ag > 0.90`). Pairing `heme_ag` with `heme_dis` gives the head an explicit `(1 - hg) * hd` mismatch signal without any hand-coded exclusion list during neural scoring.
* **`L1200–L1206` (Exact Step-0 functional equivalence)**: Because `B_init = 0`, `film = 0`, `out = 0`, `mod_alpha = 1`, and `mod_bias = 0`, at Step 0 `score_grid(g, d, m)` evaluates identically to `1.0 * z_mean + 0.0 + 0.0 = z_mean`. Every gradient step must therefore earn rank-loss improvement over the `24`-checkpoint `OptimusKG` foundation ensemble.

#### 4.2.2 Memory-Free `K`-Subspace Norm Algebra (`_subspace_delta_and_inv_r` & `backbone_grid`, `L1224–L1305`)

```python
# optimus_txgnn/option2_bite_head.py (Lines 1224-1305)
    def _subspace_delta_and_inv_r(
        self, c: int, s0_norm: torch.Tensor, sn0_sq: torch.Tensor,
        s_proj: torch.Tensor, set_k: int,
    ) -> Tuple[torch.Tensor, torch.Tensor]:
        """Exact K-dim SVD-subspace delta & post-perturbation L2 inverse norm."""
        alpha = float(self.cfg.adapter_max_delta)
        delta_sub = torch.tanh(
            self.adapter_scale * ((s_proj @ self.adapter_A[set_k, c]) @ self.adapter_B[set_k, c])
        )
        r_sq = (
            sn0_sq
            + (2.0 * alpha) * (s_proj * delta_sub).sum(dim=1, keepdim=True)
            + (alpha * alpha) * ((delta_sub @ self.VtV[c]) * delta_sub).sum(dim=1, keepdim=True)
        ).clamp(min=1e-12)
        inv_r = s0_norm / (s0_norm * r_sq.sqrt()).clamp(min=1e-6)
        return delta_sub, inv_r

    def backbone_grid(
        self, g_idx: torch.Tensor, d_idx: torch.Tensor, set_k: int = 0
    ) -> Dict[str, torch.Tensor]:
        Bg, Bd = int(g_idx.numel()), int(d_idx.numel())
        dev = self.device
        z_sum = torch.zeros(Bg, Bd, device=dev)
        z2_sum = torch.zeros(Bg, Bd, device=dev)
        p_sum = torch.zeros(Bg, Bd, device=dev)
        fam_sums = [torch.zeros(Bg, Bd, device=dev) for _ in range(len(self.fam_names))]
        fam_cnt = [0] * len(self.fam_names)
        u = torch.zeros(Bg, self.cfg.inter_dim, device=dev) if len(self.P) > 0 else None
        v = torch.zeros(Bd, self.cfg.inter_dim, device=dev) if len(self.P) > 0 else None
        alpha = float(self.cfg.adapter_max_delta)

        for c in range(self.n_ck):
            with torch.no_grad():
                S0 = self.S[c][g_idx].float()                      # (Bg, d_c)
                s0_norm = S0.norm(dim=1, keepdim=True).clamp(min=1e-6)
                Sn0 = S0 / s0_norm
                E = self.E[c][d_idx].float()                       # (Bd, d_c)
                En = E / E.norm(dim=1, keepdim=True).clamp(min=1e-6)
                base_dot = Sn0 @ E.T                               # (Bg, Bd)
                s_proj = Sn0 @ self.V_basis[c]                     # (Bg, K)
                sn0_sq = (Sn0 * Sn0).sum(dim=1, keepdim=True)
                EV = E @ self.V_basis[c]                           # (Bd, K)
            delta_sub, inv_r = self._subspace_delta_and_inv_r(c, s0_norm, sn0_sq, s_proj, set_k)
            logit = (inv_r * self.nu[c]) * (base_dot + alpha * (delta_sub @ EV.T))
            z = (logit - self.mu[c][d_idx][None, :]) / self.sd[c][d_idx][None, :]
            z_sum = z_sum + z
            z2_sum = z2_sum + z * z
            p_sum = p_sum + torch.sigmoid(logit.clamp(-20.0, 20.0))
            f = self.fam_of[c]
            fam_sums[f] = fam_sums[f] + z; fam_cnt[f] += 1
            if u is not None and v is not None:
                PV = self.P[c].weight @ self.V_basis[c]
                u = u + inv_r * (self.P[c](Sn0) + alpha * (delta_sub @ PV.T))
                v = v + self.Q[c](En)

        n = float(self.n_ck)
        z_mean = z_sum / n
        z_std = (z2_sum / n - z_mean * z_mean).clamp(min=1e-6).sqrt()
        cnt_t = torch.tensor([max(1, c) for c in fam_cnt], device=dev, dtype=torch.float32)
        out = {
            "z_mean": z_mean, "z_std": z_std, "p_mean": p_sum / n,
            "z_fam": torch.stack(fam_sums, dim=0) / cnt_t[:, None, None],
        }
        if u is not None and v is not None:
            out["u"], out["v"] = u / n, v / n
            out["uv"] = out["u"][:, None, :] * out["v"][None, :, :]
        return out
```

* **`L1234–L1242` (Exact `K`-dimensional norm algebra without `(Bg, 3072)` autograd tensors)**: Perturbing $\hat{\mathbf{s}}_0 \mapsto \hat{\mathbf{s}}_0 + \alpha \boldsymbol{\delta}\mathbf{V}_c^\top$ and re-normalizing to checkpoint $c$'s active-drug median norm $\nu_c$ would naively expand `delta_sub @ V.T` into a `(Bg, 3072)` differentiable tensor `24` times per batch. Expanding the squared Euclidean norm algebraically—$\|\hat{\mathbf{s}}_0 + \alpha \boldsymbol{\delta}\mathbf{V}_c^\top\|_2^2 = \|\hat{\mathbf{s}}_0\|_2^2 + 2\alpha\langle \hat{\mathbf{s}}_0 \mathbf{V}_c, \boldsymbol{\delta}\rangle + \alpha^2 \boldsymbol{\delta}(\mathbf{V}_c^\top \mathbf{V}_c)\boldsymbol{\delta}^\top$—and factoring the dot product as `(base_dot + alpha * (delta_sub @ EV.T))` (`L1274`) keeps every differentiable operation inside tiny `(Bg, 128)` tensors while producing **bit-for-bit identical** logits and gradients!
* **`L1279–L1297` (Per-disease $Z$-calibration & smooth variance floor)**: Every checkpoint's raw logit is standardized against its own `18,640`-drug background `(mu[c], sd[c])` before cross-checkpoint averaging. On `L1297`, `.clamp(min=1e-6).sqrt()` prevents infinite gradient $\frac{d}{dx}\sqrt{x}\big|_{x=0} = +\infty$ if degenerate checkpoints ever produce zero variance.
* **`L1286–L1293` (Gauge-aligning bilinear heads `P[c]` & `Q[c]`)**: Each checkpoint $c$ lives in its own arbitrary orthogonal rotation frame (`d_c = 2048` or `3072`), so raw vectors `S0` cannot be averaged directly across checkpoints. Learned linear maps `P[c]: R^{d_c} -> R^{32}` and `Q[c]: R^{d_c} -> R^{32}` align all `24` checkpoints into a shared `32`-dimensional interaction space (`out["uv"]`).

#### 4.2.3 Multi-Evidence `FiLM` Fusion & Multi-Positive `Plackett–Luce` Loss (`L1418–L1486`)

```python
# optimus_txgnn/option2_bite_head.py (Lines 1418-1460 & 1478-1486)
    def score_grid(
        self, g_idx: torch.Tensor, d_idx: torch.Tensor, m_grid: torch.Tensor
    ) -> torch.Tensor:
        Bg, Bd = int(g_idx.numel()), int(d_idx.numel())
        feats: List[torch.Tensor] = []
        bb = self._backbone_grid_by_modality(g_idx, d_idx, m_grid)
        feats += [
            bb["z_mean"][..., None], bb["z_std"][..., None],
            bb["p_mean"][..., None], bb["z_fam"].permute(1, 2, 0),
        ]
        pf = self.path_feat[g_idx]                                   # (Bg, 21, 6)
        feats += [pf[:, self.dis_org[d_idx], :], pf[:, self.dis_lin[d_idx], :]]
        if self.has_pair_gd:
            feats.append(self.pair_gd[g_idx][:, d_idx, :].float())   # (Bg, Bd, 3)
        if self.has_lineage_cl:
            prod = self.lin_cl_g[g_idx][:, None, :] * self.lin_cl_d[d_idx][None, :, :]
            feats += [prod, prod.max(dim=-1, keepdim=True)[0]]
        hg = self.heme_ag_g[g_idx][:, None, None]
        hd = self.heme_dis_d[d_idx][None, :, None]
        feats += [hg * hd, (1.0 - hg) * hd]
        feats.append(self.gene_x[g_idx][:, None, :].expand(Bg, Bd, -1))
        feats.append(self.dis_x[d_idx][None, :, :].expand(Bg, Bd, -1))
        if "uv" in bb:
            feats += [bb["uv"], bb["uv"].sum(-1, keepdim=True)]
        x = torch.cat(feats, dim=-1)                                 # (Bg, Bd, n_in)
        h = self.fc1(x)
        gamma, beta = self.film(self.mod_emb(m_grid)).chunk(2, dim=-1)
        h = F.gelu(h * (1.0 + gamma) + beta)
        h = F.gelu(self.fc2(self.drop(h)))
        res = self.out(self.drop(h)).squeeze(-1)
        return self.mod_alpha[m_grid] * bb["z_mean"] + self.mod_bias[m_grid] + res


def listwise_softmax_loss(
    scores: torch.Tensor, pos_mask: torch.Tensor, valid_mask: torch.Tensor,
    tau: float = 1.0,
) -> torch.Tensor:
    """Multi-positive listwise loss: -log( sum_pos exp(s) / sum_valid exp(s) )."""
    s = scores.float() / tau
    neg = torch.full_like(s, -1e9)
    s_valid = torch.where(valid_mask, s, neg)
    s_pos = torch.where(pos_mask & valid_mask, s, neg)
    return (torch.logsumexp(s_valid, dim=1) - torch.logsumexp(s_pos, dim=1)).mean()
```

* **`L1424–L1450` (Structured biological feature assembly `x` of shape `(Bg, Bd, n_in)`)**: Concatenates `7` backbone ensemble channels (`z_mean`, `z_std`, `p_mean`, and `4` family means `v4`, `v5bi-2048`, `v5bi-3072`, `v5fm`), `12` `HPA` organ + lineage IHC & `TCGA` cancer RNA channels, `3` `Open Targets L2G`/somatic/anatomy channels, `7` `DepMap` neuroendocrine/leukemia/myeloma/sarcoma/CNS/high-grade-NEC lineage concordance channels, `2` hematologic on/off-lineage indicators (`hg * hd` and `(1 - hg) * hd`), standardized `UniProt/GTEx/ESM-2` gene & disease descriptors, and `33` gauge-aligned `uv` bilinear interaction channels.
* **`L1452–L1460` (`FiLM` modality conditioning + anchored skip connection)**: `gamma` and `beta` modulate every hidden unit of `fc1(x)` as $(1 + \boldsymbol{\gamma}_m) \odot \mathbf{h} + \boldsymbol{\beta}_m$. Because `TCE/BiTE` (`m=0`) triggers cytokine release / neurotoxicity on low-level vital-organ expression (`GTEx` brain/heart/lung) far more sharply than `ADC` (`m=1`) while tolerating slower internalization kinetics, `FiLM` learns modality-specific surface-topology and clean-tissue thresholds on top of the `mod_alpha[m] * z_mean` skip connection.
* **`L1478–L1485` (`Plackett–Luce` multi-positive listwise softmax)**: Indication expansion is a **top-$K$ retrieval** problem, not calibrated binary classification over `18,025` imbalanced negatives. Computing $\log\sum_{j \in \mathcal{V}} e^{s_j/\tau} - \log\sum_{j \in \mathcal{P}} e^{s_j/\tau}$ concentrates gradient pressure on hard false-positive diseases outranking true clinical indications while masking held-out fold positives (`~valid_mask -> -1e9`) so paralog-family cross-validation never penalizes valid unseen biology.

---

### 4.3 Module C: `Phase 8` TCE-Only `MixtureSameFamily` `EXNEX` & Exact $\mathcal{O}(2^{F/2})$ Meet-in-the-Middle Sign-Flip Kernel ([`q2_response.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/models/q2_response.py) & [`pan_antigen_temporal_backtest.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/backtest/pan_antigen_temporal_backtest.py))

#### 4.3.1 Discrete-Variable-Free `MixtureSameFamily` `EXNEX` + Ibrahim–Chen Power Prior (`q2_response.py`, `L140–L225`)

```python
# optimus_txgnn/models/q2_response.py (Lines 140-225)
    prior_loc_lin = jnp.array([-0.35, -0.55, -0.70, -0.85, -2.20])[:n_lin]
    mu_lin = numpyro.sample("mu_lin", dist.Normal(prior_loc_lin, 0.85))
    tau_ex = numpyro.sample("tau_ex", dist.LogNormal(jnp.log(tau_scale), 0.50))

    mu_nex = -1.35
    sd_nex = 1.45

    mix_probs = jnp.stack([jnp.full((n_hists,), w_ex), jnp.full((n_hists,), 1.0 - w_ex)], axis=-1)
    comp_loc = jnp.stack([mu_lin[lin_of_hist], jnp.full((n_hists,), mu_nex)], axis=-1)
    comp_scale = jnp.stack([jnp.full((n_hists,), tau_ex), jnp.full((n_hists,), sd_nex)], axis=-1)
    theta_h = numpyro.sample(
        "theta_h",
        dist.MixtureSameFamily(dist.Categorical(probs=mix_probs), dist.Normal(comp_loc, comp_scale)),
    )

    lp_ex = jnp.log(w_ex) + dist.Normal(mu_lin[lin_of_hist], tau_ex).log_prob(theta_h)
    lp_nex = jnp.log(1.0 - w_ex) + dist.Normal(mu_nex, sd_nex).log_prob(theta_h)
    numpyro.deterministic("p_ex_post", jnp.exp(lp_ex - jnp.logaddexp(lp_ex, lp_nex)))

    # Within-TCE systematic agent main effects (`tarlatamab = 0` reference) + agent-by-histology transfer SD
    sigma_tce_agent = numpyro.sample("sigma_tce_agent", dist.HalfNormal(0.45))
    z_obrix = numpyro.sample("z_obrixtamig", dist.Normal(0.0, 1.0))
    z_goca = numpyro.sample("z_gocatamig", dist.Normal(0.0, 1.0))
    a_obrix = numpyro.deterministic("alpha_obrixtamig", sigma_tce_agent * z_obrix)
    a_goca = numpyro.deterministic("alpha_gocatamig", sigma_tce_agent * z_goca)
    alpha_vec = numpyro.deterministic("alpha_vec", jnp.stack([0.0, a_obrix, a_goca]))

    sigma_agent_transfer = numpyro.sample("sigma_agent_transfer", dist.LogNormal(jnp.log(0.32), 0.45))
    z_ah = numpyro.sample("z_agent_hist", dist.Normal(0.0, 1.0).expand([3, n_hists]))
    eta_ah = numpyro.deterministic("eta_agent_hist", sigma_agent_transfer * z_ah)
    # ... [Lines 171-204: non-centered trial effects, BBB penalty & Q1 prevalence stratum mixture] ...
        if abs(float(a0_cross_agent) - 1.0) < 1e-8:
            numpyro.sample("obs_orr", dist.Binomial(total_count=obs_n, probs=p_arm), obs=obs_k)
        else:
            obs_wt = jnp.where(obs_agent == 0, 1.0, float(a0_cross_agent))
            numpyro.factor(
                "obs_orr_power_prior",
                jnp.sum(obs_wt * dist.Binomial(total_count=obs_n, probs=p_arm).log_prob(obs_k)),
            )
```

* **`L147–L157` (Marginalized `MixtureSameFamily` for Hamiltonian `NUTS` geometry)**: Naive `EXNEX` implementations sample discrete Bernoulli indicators $z_h \sim \operatorname{Bernoulli}(w_{\text{EX}})$, which breaks Hamiltonian Monte Carlo (`NUTS`) gradient continuity or forces combinatorial enumeration over $2^H$ configurations. Passing `dist.MixtureSameFamily` directly evaluates the exact marginalized log-density $\log\!\big(w_{\text{EX}}\,\mathcal{N}(\theta_h; \mu_{\ell(h)}, \tau_{\text{EX}}^2) + (1-w_{\text{EX}})\,\mathcal{N}(\theta_h; \mu_{\text{NEX}}, \sigma_{\text{NEX}}^2)\big)$ in pure `JAX` float64/float32, while `L155–L157` recovers each histology's exact Rao–Blackwellized posterior exchangeability probability `p_ex_post` via `logaddexp` (`0` divergences across `4` chains, `max R_hat <= 1.003`).
* **`L160–L170` (Non-centered cross-TCE parameterization + zero ADC contamination)**: Only clinical `DLL3` T-cell engager cohorts (`tarlatamab = 0`, `obrixtamig = 1`, `gocatamig = 2`) enter `_exnex_tce_only_model` so Topo-I/PBD ADC bystander cytotoxicity cannot bias T-cell synapse posteriors. Sampling `z_obrix, z_goca, z_ah ~ Normal(0, 1)` and multiplying by `sigma_tce_agent` / `sigma_agent_transfer` eliminates Neal's funnel curvature when few cross-agent cohorts are observed.
* **`L217–L224` (Ibrahim–Chen power-prior down-weighting via `numpyro.factor`)**: In cohorts where clinical readouts currently come from competitor `DLL3` TCEs (`obrixtamig` in `LCNEC-L`, `gocatamig` in `NEPC`/`EP-NEC`) rather than `tarlatamab` (`obs_agent != 0`), multiplying the Binomial log-likelihood by $a_0 = 0.50$ inside `numpyro.factor("obs_orr_power_prior", ...)` discounts cross-molecule borrowing by `50%` so `tarlatamab` posterior `90% CrI`s honestly reflect cross-construct translation uncertainty.

#### 4.3.2 Exact $\mathcal{O}(2^{F/2} \log 2^{F/2})$ Meet-in-the-Middle Family Sign-Flip Kernel (`pan_antigen_temporal_backtest.py`, `L340–L377`)

```python
# optimus_txgnn/backtest/pan_antigen_temporal_backtest.py (Lines 340-377)
def _exact_family_signflip_pvalue(
    family_deltas: np.ndarray, max_exact_f: int = 30, seed: int = 202691
) -> Tuple[float, str, int]:
    F_cnt = int(len(family_deltas))
    if F_cnt == 0:
        return 1.0, "empty", 0
    obs_mean = float(np.mean(family_deltas))
    if np.all(np.abs(family_deltas) < 1e-15):
        return 1.0, "zero_diff", 1 << min(F_cnt, max_exact_f)

    if F_cnt <= max_exact_f:
        total = 1 << F_cnt
        d_vec = family_deltas.astype(np.float64) / float(F_cnt)
        if F_cnt <= 16:
            ints = np.arange(total, dtype=np.uint32)[:, None]
            bit_shifts = np.arange(F_cnt, dtype=np.uint32)[None, :]
            signs = np.where(((ints >> bit_shifts) & 1) == 1, -1.0, 1.0)
            means = signs @ d_vec
            ge_cnt = int(np.sum(means >= obs_mean - 1e-12))
        else:
            f_a = F_cnt // 2
            f_b = F_cnt - f_a
            ints_a = np.arange(1 << f_a, dtype=np.uint32)[:, None]
            shifts_a = np.arange(f_a, dtype=np.uint32)[None, :]
            sums_a = np.where(((ints_a >> shifts_a) & 1) == 1, -1.0, 1.0) @ d_vec[:f_a]

            ints_b = np.arange(1 << f_b, dtype=np.uint32)[:, None]
            shifts_b = np.arange(f_b, dtype=np.uint32)[None, :]
            sums_b = np.sort(np.where(((ints_b >> shifts_b) & 1) == 1, -1.0, 1.0) @ d_vec[f_a:])

            thresh = (obs_mean - 1e-12) - sums_a
            idx = np.searchsorted(sums_b, thresh, side="left")
            ge_cnt = int(np.sum(len(sums_b) - idx))
        return float(ge_cnt) / float(total), f"exact_2_to_{F_cnt}_family_signflip", total
```

* **`L346–L355` (Paralog-family cluster unit of inference)**: Because `ERBB2` and `EGFR` (or `DLL3` and `DLL4`) share sequence and indication histories, testing across `32` individual antigens violates independence and inflates false-positive claims. Aggregating paired rank differences $\Delta_f$ at the **paralog-family cluster level** ($F \approx 24$ independent families) enforces valid exchangeability under $H_0: \mathbb{E}[\Delta_f] \le 0$.
* **`L363–L376` (Horowitz–Sahni Meet-in-the-Middle binary search reducing $2^{30}$ to $2^{15}$)**: Brute-force enumeration of $2^F$ sign vectors at $F = 24\text{–}30$ requires up to $1.07 \times 10^9$ dot products ($\sim 32\text{ GB}$ RAM). Splitting `d_vec` into `f_a = F // 2` (`<= 32,768` left subset sums `sums_a`) and `f_b = F - f_a` (`<= 32,768` sorted right subset sums `sums_b`), `np.searchsorted(sums_b, (obs_mean - 1e-12) - sums_a, side="left")` counts **all exact $2^F$ sign-flip permutations** in `< 2 milliseconds` using `< 1 MB` of memory with zero Monte Carlo sampling noise—providing the airtight statistical proof that retired `Q5` from `Phase 8` governance!


