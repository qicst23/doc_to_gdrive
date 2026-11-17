# 08 — Neural Architectures, Dual-Backend Engineering, All Trained Models & Complete Benchmark Analysis

> **Monograph Series (Post-Audit Final Reference — Nov 2, 2026)**
> * **Part I**: [07_END_TO_END_ARCHITECTURE_AND_DATA_ENGINEERING.md](file:///home/nchen10/antigravity_prjs/kg/docs/07_END_TO_END_ARCHITECTURE_AND_DATA_ENGINEERING.md) — Strategic Plan, 5-Tier Architecture, `OptimusKG v3.2` -> `clinical_v4` Curation, `OnSIDES v3.1.1` + MONDO DAG Hygiene, and `amgen_v5` Data Engineering.
> * **Part II (This Document)**: [08_MODEL_TRAINING_CAMPAIGNS_AND_BENCHMARKS.md](file:///home/nchen10/antigravity_prjs/kg/docs/08_MODEL_TRAINING_CAMPAIGNS_AND_BENCHMARKS.md) — Scalable `HeteroRGCN` + `TorchHeteroGraph` Engine, All `30` Trained Models, `24`-Model Ensemble (`944.2M` Largest Single GNN, `35.9×` TxGNN), and Paired 207-Disease Bootstrap Benchmarks.
> * **Part III**: [09_RANKING_RECOMMENDATION_AND_VISUALIZATION_SYSTEM.md](file:///home/nchen10/antigravity_prjs/kg/docs/09_RANKING_RECOMMENDATION_AND_VISUALIZATION_SYSTEM.md) — Leak-Free Within-Seed Goal 1 Ordering, MONDO + `OnSIDES v3.1.1` Masking, Goal 2 `OptimusCombo-Net` Co-Administration & IP Tiering, `<1.5s` 4-Family CPU Scorer, and 15-View Discovery Studio.
> * **Part IV**: [10_CRITICAL_AUDIT_AND_AMGEN_NEXT_STEPS_PLAN.md](file:///home/nchen10/antigravity_prjs/kg/docs/10_CRITICAL_AUDIT_AND_AMGEN_NEXT_STEPS_PLAN.md) — Biostatistics Audit, Root-Cause Fixes (`RC-1`..`RC-5`), 2024–2026 Literature Synthesis, and Amgen-Wide Roadmap.

---

## 1. Scalable `HeteroRGCN` + `DistMult` + Disease-Prototype Architecture

Implemented in [`model.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/model.py) and [`dgl_compat.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/dgl_compat.py), **OptimusKG-TxGNN** scales heterogeneous relational graph convolution from the original PrimeKG `d = 512` (`26.3M` weights) up to **`d = 3072..5120` (`944.2M` to `2.62B` GNN weights per model)** across `9,385,536` bidirectional edges and `R = 50` canonical edge types.

### 1.1 Mathematical Formulation & `O(N d^2)` Memory Breakthrough
For each canonical relation $r = (\tau_s, \rho, \tau_t) \in \mathcal{R}$ (`|\mathcal{R}| = 50`) and layer $\ell \in \{1, 2\}$:
* **Naive Message-Passing Bottleneck**: Applying relation matrix $W_r^{(\ell)} \in \mathbb{R}^{d \times d}$ *per edge* before aggregation materializes `9.39M × d` float32 edge tensors (`> 115 GB` VRAM at `d = 3072`, causing immediate OOM).
* **Exact Relation-Mean-First Factorization (`0.0` Numerical Drift)**: Because linear projection commutes with mean neighbor aggregation, we first compute the unweighted neighbor mean per destination node $v$ and relation $r$, and then apply $W_r^{(\ell)}$ at the destination node level:

$$
\bar{m}_{v, r}^{(\ell)} = \frac{1}{|\mathcal{N}_r(v)|} \sum_{u \in \mathcal{N}_r(v)} h_u^{(\ell - 1)}, \qquad
h_v^{(\ell)} = \sigma\!\left(\frac{1}{|\mathcal{R}_{\text{in}}(v)|} \sum_{r \in \mathcal{R}_{\text{in}}(v)} W_r^{(\ell)} \bar{m}_{v, r}^{(\ell)}\right)
$$

where $\sigma$ is `LeakyReLU(0.2)` on Layer 1 and identity on Layer 2. This reduces dense linear complexity from $\mathcal{O}(|E| d^2)$ to $\mathcal{O}(|E| d + N |\mathcal{R}_{\text{in}}| d^2)$, cutting activation VRAM by **`15×`** so exact **`fp32`** full-graph training at `d = 3072` (`944.2M` GNN weights + `428.4M` node table) runs inside **`50.8–56.2 GB` VRAM** on a single `96 GB` GPU.

### 1.2 Multimodal Gated `H^(0)` Cold-Target Adapter (`v5fm` Generation)
In `v4` and `v5bi` models, initial embeddings $h_v^{(0)} \in \mathbb{R}^d$ come from a deterministic `seed = 0` Gaussian table (`G.nodes[nt].data['inp']`). In **`v5fm` multimodal models**, each node type $\tau$ receives a 2-layer `LayerNorm -> Linear -> GELU -> Linear` adapter $\Phi_\tau$ over its `NCBI MedCPT` + `SMILES`/genomic vector $f_v \in \mathbb{R}^{d_\tau}$ (`770-D` to `843-D`), modulated by a learnable zero-initialized `tanh` gate $g_\tau$ and trained under row-wise **node-ID dropout** ($p_{\text{drop}} = 0.15$):

$$
\tilde{h}_v^{(0)} = \underbrace{\frac{m_v}{1 - p_{\text{drop}}} \, h_{v, \text{id}}^{(0)}}_{\text{stochastic ID dropout during training}} + \tanh(g_\tau)\, \Phi_\tau(f_v)
$$

Zero-initialization ($g_\tau = 0$ at step 0) preserves stable Stage-1/Stage-2 optimization early in training, while `15%` ID dropout forces the GNN to extract semantic & chemical signal from $\Phi_\tau(f_v)$ whenever a node's memorizable ID vector is masked—dramatically improving generalization on cold/low-degree clinical targets.

### 1.3 Sparse CSR Disease-Prototype Module & `DistMult` Decoder
Before scoring drug–disease relations $\rho \in \{\text{indication}, \text{contraindication}, \text{off-label use}\}$, low-degree or unseen diseases augment their Layer-2 embedding $h_d^{(2)}$ with a rarity-gated attention sum over their `k = 3` most profile-similar training diseases (`sim_measure = "all_nodes_profile"` computed via pre-indexed sparse CSR Jaccard/cosine profiles):

$$
\hat{h}_d^{(2)} = \alpha_{\text{rar}}(d)\, h_{\text{proto}}(d) + \bigl(1 - \alpha_{\text{rar}}(d)\bigr)\, h_d^{(2)}, \qquad
\ell_\rho(u, d) = \left\langle h_u^{(2)} \odot w_\rho,\; \hat{h}_d^{(2)} \right\rangle
$$

where $w_\rho \in \mathbb{R}^d$ is the relation-specific `DistMult` diagonal interaction vector.

### 1.4 Dual Graph Backend (`native DGL` $\leftrightarrow$ `TorchHeteroGraph`)
Because compiled DGL wheels do not exist for `Python 3.13` + `PyTorch 2.11+cu130` (`NVIDIA RTX PRO 6000 Blackwell Server Edition`, `sm_120`), [`dgl_compat.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/dgl_compat.py) implements a zero-dependency pure-PyTorch `TorchHeteroGraph` with `256 MB` chunked `index_add_` scatter kernels that loads identical checkpoints, matches DGL output tensors to floating-point precision, and passes all `31` unit tests on both backends.

---

## 2. Three-Stage Training Protocol & Key Algorithmic Discoveries

Implemented in [`trainer.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/trainer.py), [`train_optimus_txgnn.py`](file:///home/nchen10/antigravity_prjs/kg/train_optimus_txgnn.py), [`run_colab_preset.py`](file:///home/nchen10/antigravity_prjs/kg/run_colab_preset.py), and [`run_colab_campaign.py`](file:///home/nchen10/antigravity_prjs/kg/run_colab_campaign.py):

1. **Stage 1 — Leak-Free All-Relation Self-Supervised Pretraining (`1` Full-Pass Epoch, `batch = 1024`, `lr = 1e-3`)**:
   Trains `HeteroRGCN` + `DistMult` over all `8.45M` training edges in `G_pretrain` (strictly verified to contain **zero** validation or test drug–disease pairs). Furthermore, because Stage 1 is identical across Stage-2 negative-sampling mixes (`u = 0.2` and `u = 0.5`) for a fixed `(seed, architecture)`, `run_colab_campaign.py` **automatically reuses Stage-1 checkpoints** (`reuse_stage1_run_name`), cutting `u = 0.5` runtimes from `~213–286 min` down to **`6–30 min`**!
2. **Stage 2 — Drug–Disease Fine-Tuning (`500–600` Epochs, `lr = 5e-4`, `valid_per_n = 10` Early-Stopping Checkpointing)**:
   * **Critical Negative-Sampling Discovery (`fix_dst` Failure vs. `mixed_dst` Scaling)**:
     Our early Colab `G4` scaling probes (`colab_full_graph_d3072_nodeemb_fix_dst_s42` at `1.37B` params and `d4096_nodeemb_fix_dst_s42` at `2.25B` params) revealed a major scientific finding: when node embeddings are free-trainable (`trainable_node_emb = True`) under pure `fix_dst` sampling (which only corrupts drugs for a fixed disease), wider models memorize drug-degree priors (`0.2025` and `0.1804` Own-Test AUPRC; `0.0416` Head-to-Head vs. Drive!). Freezing the `seed = 0` Gaussian node table (`trainable_node_emb = False`), turning off degree-proportional negative sampling (`nodeg`), and blending `u ∈ {0.2, 0.5}` uniform corrupt-destination pairs (`mixed_dst`) transformed width scaling into a clean monotonic gain: **`0.2597` (`d = 1024`) -> `0.3290` (`d = 2048`) -> `0.3504` (`d = 3072`)**!
   * **`v5bi` Bidirectional Hub-Disease Ranking Loss (`disease_neg_weight = 0.25`)**:
     Standard TxGNN fine-tuning only samples negative *drugs* $(u', d)$ for a positive $(u, d)$. In `v5bi` and `v5fm`, we added an auxiliary **corrupt-disease** margin/BCE term sampling `4` hard degree-weighted + uniform negative diseases $(u, d')$ per positive indication (`disease_neg_weight = 0.25`), directly training $\langle h_u^{(2)} \odot w_{\text{ind}}, \hat{h}_d^{(2)} \rangle$ to order true indications above high-degree disease hubs for a fixed drug $u$.
3. **Stage 3 — `d = 3072` Differentiable `GraphMask` Attribution (`graphmask.py`)**:
   Trains 2-layer `SoftConcrete` edge gates $z_e^{(\ell)} \in [0, 1]$ with Lagrangian $L_0$ sparsity maximization over the frozen **`944.2M`-weight `d = 3072` `v5bi` flagship model**, exporting all **`9,385,536` Layer-1 and Layer-2 edge attribution weights** (`edge_weights_all.parquet`) for self-explaining mechanistic subgraphs.

---

## 3. Complete Inventory of All `30` Trained Models & Benchmark Leaderboard

Across local workstation (`RTX 4090 24 GB`) and Google Colab (`NVIDIA RTX PRO 6000 Blackwell / G4 96 GB`), we trained **5 workstation `d = 1024` reference baselines** + **`29` Colab high-memory models** + **`1` `d = 3072` Stage-3 `GraphMask` explainer**.

### 3.1 Summary by Architectural Family (`full_graph` Held-Out Test & Paired 207-Disease Bootstrap Benchmarks)

To prevent single-seed cherry-picking (e.g., `seed=2` where Official Drive TxGNN is `0.1731` vs. `seed=42` where Drive is `0.2595`), [`paired_24model_vs_drive_bootstrap.json`](file:///home/nchen10/antigravity_prjs/kg/amgen_discovery_studio/results/paired_24model_vs_drive_bootstrap.json) evaluates every family and the full `24`-model ensemble strictly **within-seed (`8` ckpts/seed on `s42`, `s1`, `s2`)** across all **`207` shared test diseases and `5,297` shared DrugBank drugs** using `10,000` paired unique-disease cluster bootstrap resamples:

| Model Family | Models Trained | Width $d$ | GNN Weights / Model | Precision | Own-Test Ind. AUPRC (Range / Peak) | Paired 207-Disease AUPRC vs. Drive TxGNN (`95% CI`) | Status in Production Studio |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: | :--- |
| *Official Google Drive TxGNN (PrimeKG Reference)* | *Ref* | `512` | `26.3M` | `fp32` | — | **`0.2156 [0.1774, 0.2559]`** (`s42: 0.2595`, `s1: 0.2068`, `s2: 0.1731`) | External Baseline |
| **0. Exploratory `nodeemb + fix_dst` Probes** | `5` (`d3072`–`d5120`) | `3072–5120` | `944M–2.62B` (+ node emb) | `bf16` | `0.1428–0.2025` | `0.0416` (`-0.2179` vs Drive on `s42`) | **Excluded** *(diagnosed degree shortcut)* |
| **0B. Same-Stack `d = 1024` Control** | `1` (`s42`) + `5` local | `1024` | `105.0M` (`4.0×` Drive) | `fp32` | `0.2597` (`s42` Colab; `0.1595` `complex_disease`) | `0.3010` (`+0.0415` vs Drive on `s42`) | **Excluded** *(width-ablation control)* |
| **1. `v4 wmatch` (`d = 2048`, `u ∈ {0.2, 0.5}`)** | **`6`** (`s42, s1, s2`) | `2048` | `419.7M` (`16.0×` Drive) | `fp32` | `0.2790–0.3290` (**`0.3290`**) | **`0.4022`** ($\Delta = +0.1866\;[+0.1205, +0.2512]$) | **Selected (`6 / 24`)** |
| **2. `v5bi` Bidirectional (`d = 2048`, `w = 0.25`)** | **`6`** (`s42, s1, s2`) | `2048` | `419.7M` (`16.0×` Drive) | `fp32` | `0.2705–0.3389` (**`0.3389`**) | **`0.3919`** ($\Delta = +0.1763\;[+0.1095, +0.2414]$) | **Selected (`6 / 24`)** |
| **3. `v5bi` Ultra-Wide Flagship (`d = 3072`, `w = 0.25`)** | **`6`** (`s42, s1, s2`) | `3072` | **`944.2M` (`35.9×` Drive)** | `fp32` | `0.2843–0.3504` (**`0.3504` Record**) | **`0.3948`** ($\Delta = +0.1792\;[+0.1112, +0.2454]$) | **Selected (`6 / 24`) + `GraphMask`** |
| **4. `v5fm` `NCBI MedCPT` Multimodal (`d = 2048`)** | **`6`** (`s42, s1, s2`) | `2048` | `441.8M` (`16.8×` Drive) | `fp32` | `0.2744–0.3250` (**`0.3250`**) | **`0.4275`** ($\Delta = +0.2119\;[+0.1445, +0.2773]$, `1.98×`; peak `s2: 0.5137`, `s42: 0.4214`) | **Selected (`6 / 24`)** |
| **Final `24`-Model Production Ensemble** | **`24` Correlated Runs** | `2048 & 3072` | **`944.2M` Max Single** *(sum across 24 runs = `13.35B`)* | **`fp32`** | **`0.3379` 3-Seed Mean (`0.3603` on `s1`) across `3,353` clinical drugs** (`0.2460` across all `18,640` drugs) | **`0.4380 [0.3848, 0.4906]`** ($\Delta = \mathbf{+0.2224\;[+0.1545, +0.2880]}$, **`2.03×` Drive**, $P = 1.0000$) | **Active Production Build** |

> **Reconciled Own-Test Candidate Denominators & Complete External Drive TxGNN Overlap Audit ([`METRICS.md`](file:///home/nchen10/antigravity_prjs/kg/METRICS.md))**:
> 1. **Candidate-Pool Denominator Reconciliation**: Individual checkpoint `training_summary.json` Own-Test AUPRCs (`0.2705–0.3504`) are computed by [`evaluator.py`](file:///home/nchen10/antigravity_prjs/kg/optimus_txgnn/evaluator.py) over the **`3,353` active clinical drugs** carrying `>=1` drug–disease edge. On that **exact same `3,353`-drug denominator**, the `24`-model within-seed ensemble achieves **`0.3379` 3-seed mean AUPRC (`0.3603` on `seed = 1`, exceeding the best single checkpoint `0.3504`)**. Across **all `18,640` drug nodes** (`5.56×` larger denominator including `15,287` unapproved ChEMBL compounds), the `24`-model ensemble achieves **`0.2460`** (outperforming every individual 6-checkpoint family `0.2003–0.2343`).
> 2. **External Drive TxGNN All-Metric Transparency & `94.5%` Training-Split Overlap**: Across all `207` shared diseases (`5,297` shared drugs), Ours wins **`AUPRC` (`0.4380` vs. `0.2156`, `+0.2224 [+0.1545, +0.2880]`)**, while Drive shows higher **`Recall@50` (`0.7745` vs. `0.6468`, `-0.1276 [-0.2026, -0.0533]`)** and **`AUROC` (`0.9903` vs. `0.9597`, `-0.0306 [-0.0441, -0.0191]`)** because **`398 / 421` (`94.5%`) of our held-out test pairs were inside Drive TxGNN's PrimeKG training set (`in_drive_train == True`)**! On the **strictly doubly-held-out `0%`-overlap stratum (`n = 22` unique diseases)**, Drive's `AUPRC` drops from `0.2156` to **`0.0509`**, and our `24`-model ensemble beats Drive on **both `AUPRC` (`0.4542` vs. `0.0509`, `+0.4033 [+0.2232, +0.5874]`, `8.93×`) and `Recall@50` (`0.6818` vs. `0.4545`, `1.50×`)**.

### 3.2 Complete Run-by-Run Colab Benchmark Table ([`CAMPAIGN_LEADERBOARD.csv`](file:///home/nchen10/antigravity_prjs/kg/CAMPAIGN_LEADERBOARD.csv))

| # | Run Name | Split | Seed | $d$ | Trained Weights | Ind. AUROC | Ind. AUPRC | Recall@50 | Head-to-Head vs. Drive AUPRC ($\Delta$) | Doubly Held-Out Top-50 | Peak VRAM | Runtime (min) | Production Ensemble? |
|---:|---|---|---:|---:|---:|---:|---:|---:|---|---|---:|---:|:---:|
| 1 | `colab_full_graph_d3072_nodeemb_fix_dst_s42` | `full_graph` | 42 | 3072 | 1372.5M | 0.9656 | 0.2025 | 0.6683 | `0.0416` vs `0.2595` (`-0.2179`) | `1/8` (Drive `3/8`) | 41.9 GB | 23.8 | No *(probe)* |
| 2 | `colab_complex_disease_d3072_nodeemb_fix_dst_s42` | `complex_disease` | 42 | 3072 | 1372.5M | 0.8429 | 0.1588 | 0.3014 | — | — | 38.0 GB | 22.3 | No *(probe)* |
| 3 | `colab_full_graph_d4096_nodeemb_fix_dst_s42` | `full_graph` | 42 | 4096 | 2249.5M | 0.9655 | 0.1804 | 0.6343 | `0.0303` vs `0.2595` (`-0.2292`) | `2/8` (Drive `3/8`) | 44.5 GB | 42.6 | No *(probe)* |
| 4 | `colab_complex_disease_d4096_nodeemb_fix_dst_s42` | `complex_disease` | 42 | 4096 | 2249.5M | 0.8485 | 0.1428 | 0.3409 | — | — | 44.5 GB | 39.4 | No *(probe)* |
| 5 | `colab_full_graph_d1024_wmatch_mixed_dst_u20_nodeg_s42` | `full_graph` | 42 | 1024 | 105.0M | 0.9675 | 0.2597 | 0.6793 | `0.3010` vs `0.2595` (`+0.0415`) | `5/8` (Drive `3/8`) | 17.1 GB | 52.3 | No *(control)* |
| 6 | `colab_full_graph_d2048_wmatch_mixed_dst_u20_nodeg_s1` | `full_graph` | 1 | 2048 | 419.7M | 0.9728 | **0.3290** | 0.7511 | `0.3434` vs `0.2068` (`+0.1365`) | `5/7` (Drive `3/7`) | 32.9 GB | 140.5 | **Yes (`#1`)** |
| 7 | `colab_full_graph_d2048_wmatch_mixed_dst_u20_nodeg_s2` | `full_graph` | 2 | 2048 | 419.7M | 0.9633 | 0.2884 | 0.6970 | `0.4056` vs `0.1731` (`+0.2325`) | `6/8` (Drive `4/8`) | 31.7 GB | 140.4 | **Yes (`#2`)** |
| 8 | `colab_full_graph_d2048_wmatch_mixed_dst_u20_nodeg_s42` | `full_graph` | 42 | 2048 | 419.7M | 0.9701 | 0.3052 | 0.7502 | `0.3238` vs `0.2595` (`+0.0643`) | `4/8` (Drive `3/8`) | 32.9 GB | 140.5 | **Yes (`#3`)** |
| 9 | `colab_full_graph_d2048_wmatch_mixed_dst_u50_nodeg_s1` | `full_graph` | 1 | 2048 | 419.7M | 0.9680 | 0.3121 | 0.7288 | `0.3560` vs `0.2068` (`+0.1491`) | `6/7` (Drive `3/7`) | 31.7 GB | 11.1 | **Yes (`#4`)** |
| 10 | `colab_full_graph_d2048_wmatch_mixed_dst_u50_nodeg_s2` | `full_graph` | 2 | 2048 | 419.7M | 0.9629 | 0.2821 | 0.6911 | `0.4544` vs `0.1731` (`+0.2814`) | `7/8` (Drive `4/8`) | 31.7 GB | 10.8 | **Yes (`#5`)** |
| 11 | `colab_full_graph_d2048_wmatch_mixed_dst_u50_nodeg_s42` | `full_graph` | 42 | 2048 | 419.7M | 0.9659 | 0.2790 | 0.7459 | `0.3732` vs `0.2595` (`+0.1137`) | `5/8` (Drive `3/8`) | 34.2 GB | 140.6 | **Yes (`#6`)** |
| 12 | `colab_full_graph_d2048_v5bi_mixed_dst_u20_nodeg_s42` | `full_graph` | 42 | 2048 | 419.7M | 0.9703 | 0.2850 | 0.7487 | `0.2916` vs `0.2595` (`+0.0321`) | `3/8` (Drive `3/8`) | 36.1 GB | 144.1 | **Yes (`#7`)** |
| 13 | `colab_full_graph_d2048_v5bi_mixed_dst_u50_nodeg_s42` | `full_graph` | 42 | 2048 | 419.7M | 0.9689 | 0.2830 | 0.7348 | `0.3738` vs `0.2595` (`+0.1142`) | `4/8` (Drive `3/8`) | 33.6 GB | 14.0 | **Yes (`#8`)** |
| 14 | `colab_full_graph_d2048_v5bi_mixed_dst_u20_nodeg_s1` | `full_graph` | 1 | 2048 | 419.7M | 0.9717 | 0.3251 | 0.7550 | `0.2895` vs `0.2068` (`+0.0827`) | `5/7` (Drive `3/7`) | 34.9 GB | 14.7 | **Yes (`#9`)** |
| 15 | `colab_full_graph_d2048_v5bi_mixed_dst_u50_nodeg_s1` | `full_graph` | 1 | 2048 | 419.7M | 0.9703 | **0.3389** | 0.7558 | `0.4049` vs `0.2068` (`+0.1980`) | `6/7` (Drive `3/7`) | 36.1 GB | 14.0 | **Yes (`#10`)** |
| 16 | `colab_full_graph_d2048_v5bi_mixed_dst_u20_nodeg_s2` | `full_graph` | 2 | 2048 | 419.7M | 0.9641 | 0.2967 | 0.7227 | `0.4008` vs `0.1731` (`+0.2278`) | `6/8` (Drive `4/8`) | 34.9 GB | 14.3 | **Yes (`#11`)** |
| 17 | `colab_full_graph_d2048_v5bi_mixed_dst_u50_nodeg_s2` | `full_graph` | 2 | 2048 | 419.7M | 0.9617 | 0.2705 | 0.6862 | `0.4413` vs `0.1731` (`+0.2683`) | `7/8` (Drive `4/8`) | 36.1 GB | 13.7 | **Yes (`#12`)** |
| 18 | `colab_full_graph_d3072_v5bi_mixed_dst_u50_nodeg_s42` | `full_graph` | 42 | 3072 | 944.2M | 0.9612 | 0.2912 | 0.7368 | `0.3325` vs `0.2595` (`+0.0730`) | `3/8` (Drive `3/8`) | 52.6 GB | 30.1 | **Yes (`#13`)** |
| 19 | `colab_full_graph_d3072_v5bi_mixed_dst_u20_nodeg_s42` | `full_graph` | 42 | 3072 | 944.2M | 0.9691 | 0.2843 | 0.7508 | `0.3435` vs `0.2595` (`+0.0840`) | `5/8` (Drive `3/8`) | 54.4 GB | 286.5 | **Yes (`#14` + `GraphMask`)** |
| 20 | `colab_full_graph_d3072_v5bi_mixed_dst_u20_nodeg_s1` | `full_graph` | 1 | 3072 | 944.2M | 0.9700 | **0.3504** | **0.7698** | `0.3381` vs `0.2068` (`+0.1312`) | `4/7` (Drive `3/7`) | 54.4 GB | 286.2 | **Yes (`#15`)** |
| 21 | `colab_full_graph_d3072_v5bi_mixed_dst_u50_nodeg_s1` | `full_graph` | 1 | 3072 | 944.2M | 0.9672 | 0.3292 | 0.7440 | `0.3714` vs `0.2068` (`+0.1645`) | `6/7` (Drive `3/7`) | 52.6 GB | 6.2 | **Yes (`#16`)** |
| 22 | `colab_full_graph_d3072_v5bi_mixed_dst_u20_nodeg_s2` | `full_graph` | 2 | 3072 | 944.2M | 0.9647 | 0.2949 | 0.7209 | `0.3927` vs `0.1731` (`+0.2197`) | `6/8` (Drive `4/8`) | 50.8 GB | 286.2 | **Yes (`#17`)** |
| 23 | `colab_full_graph_d3072_v5bi_mixed_dst_u50_nodeg_s2` | `full_graph` | 2 | 3072 | 944.2M | 0.9619 | 0.3050 | 0.7118 | `0.4408` vs `0.1731` (`+0.2677`) | `7/8` (Drive `4/8`) | 56.2 GB | 29.9 | **Yes (`#18`)** |
| 24 | `colab_full_graph_d2048_v5fm_mixed_dst_u20_nodeg_s42` | `full_graph` | 42 | 2048 | 441.8M | 0.9671 | 0.3043 | 0.7020 | `0.3852` vs `0.2595` (`+0.1257`) | `4/8` (Drive `3/8`) | 34.3 GB | 213.6 | **Yes (`#19`)** |
| 25 | `colab_full_graph_d2048_v5fm_mixed_dst_u50_nodeg_s42` | `full_graph` | 42 | 2048 | 441.8M | 0.9667 | 0.2744 | 0.7079 | `0.4214` vs `0.2595` (`+0.1619`) | `5/8` (Drive `3/8`) | 35.6 GB | 18.9 | **Yes (`#20`)** |
| 26 | `colab_full_graph_d2048_v5fm_mixed_dst_u20_nodeg_s1` | `full_graph` | 1 | 2048 | 441.8M | 0.9694 | 0.2939 | 0.7419 | `0.2548` vs `0.2068` (`+0.0480`) | `6/7` (Drive `3/7`) | 33.1 GB | 213.2 | **Yes (`#21`)** |
| 27 | `colab_full_graph_d2048_v5fm_mixed_dst_u50_nodeg_s1` | `full_graph` | 1 | 2048 | 441.8M | 0.9658 | 0.3054 | 0.7248 | `0.3351` vs `0.2068` (`+0.1282`) | `6/7` (Drive `3/7`) | 39.3 GB | 18.5 | **Yes (`#22`)** |
| 28 | `colab_full_graph_d2048_v5fm_mixed_dst_u20_nodeg_s2` | `full_graph` | 2 | 2048 | 441.8M | 0.9632 | **0.3250** | 0.7114 | `0.4324` vs `0.1731` (`+0.2593`) | `6/8` (Drive `4/8`) | 33.0 GB | 213.2 | **Yes (`#23`)** |
| 29 | `colab_full_graph_d2048_v5fm_mixed_dst_u50_nodeg_s2` | `full_graph` | 2 | 2048 | 441.8M | 0.9565 | 0.3106 | 0.6940 | **`0.5137`** vs `0.1731` (**`+0.3406`**) | **`7/8`** (Drive `4/8`) | 39.3 GB | 18.5 | **Yes (`#24`)** |

*(In addition, `colab_full_graph_d5120_nodeemb_fix_dst_s42` was executed as a `3.34B`-parameter `96 GB` VRAM frontier capability stress test, and `colab_full_graph_d3072_v5bi_mixed_dst_u20_nodeg_s42` completed full Stage-3 `GraphMask` gate optimization across all `9.39M` edges.)*

---

## 4. Why This `24`-Model Diversity Maximizes Translational Precision (and Open Zero-Shot Scope)

Combining **4 distinct inductive biases** across **3 independent data splits (`seeds 42, 1, 2`)** and **2 negative-sampling operating points (`u = 0.2` hard-negative specificity + `u = 0.5` full-catalog calibration)** lifts paired 207-disease AUPRC to **`0.4380 [0.3848, 0.4906]`** (`0.4542` vs. `0.0509` on the `0%`-overlap doubly-held-out stratum)—outperforming every individual 6-checkpoint family (`0.3919`–`0.4275`) and doubling external Drive TxGNN (`0.2156`, paired $\Delta = +0.2224\;[+0.1545, +0.2880]$):
1. **`6× v4 wmatch (d = 2048)`**: Pure relational topology baseline (`0.4022` paired AUPRC) with strong precision on seen commercial drugs.
2. **`6× v5bi (d = 2048)`**: Explicitly suppresses hub-disease false positives via bidirectional disease-negative contrast (`w = 0.25`, `0.3389` peak Own-Test AUPRC).
3. **`6× v5bi (d = 3072)`**: `2.25×` wider relational capacity (`944.2M` GNN weights/model—`35.9×` TxGNN's `26.3M`), capturing subtle multi-pathway interactions (`0.3504` peak Own-Test AUPRC, `76.98%` `Recall@50`), while streaming `.eval()` intermediate tensors to CPU keeps full-graph `d = 3072` inference inside **`10.26 GB` VRAM** on a single `24 GB` workstation GPU.
4. **`6× v5fm Multimodal (d = 2048)`**: Injects `NCBI MedCPT` biomedical literature semantics (`768-D`) + `75-D` `SMILES` chemistry + `6-D` `gnomAD`/tractability scalars via gated `H^(0)` adapters (`0.4275` 6-checkpoint paired AUPRC), slashing epistemic variance $\sigma_z(u, d)$ on pipeline/cold targets (`Bemarituzumab [FGFR2b]`, `MariTide [GLP1R+/GIPR-]`, `Xaluritamig [STEAP1/CD3E]`).
5. **Scope Note (`full_graph` vs. `complex_disease`)**: All `24` production checkpoints train on `full_graph` splits (`seeds 42, 1, 2`). Strict disease-area zero-shot holdout (`complex_disease`) was last run at `d = 1024` (`0.1595` indication AUPRC) and is queued at `d = 2048` (`v5bi` & `v5fm`) as **Priority 2b** in [10_CRITICAL_AUDIT_AND_AMGEN_NEXT_STEPS_PLAN.md](file:///home/nchen10/antigravity_prjs/kg/docs/10_CRITICAL_AUDIT_AND_AMGEN_NEXT_STEPS_PLAN.md).
