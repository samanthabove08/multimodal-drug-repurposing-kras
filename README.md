# Multimodal Molecular Representation Framework for Drug Repurposing
### Application to KRAS G12C Inhibitor Identification

This repository contains the code for the paper:

> **A Multimodal Molecular Representation Framework for Robust Similarity-Based Drug Repurposing: Application to KRAS G12C Inhibitor Identification**
> Samantha Bove et al. — *submitted to Bioinformatics*

---

## Overview

This framework identifies candidate drug molecules by integrating three complementary molecular representations,  fingerprint-based, image-based, and graph-based, within a unified pipeline combining dimensionality reduction, unsupervised clustering, cross-representation integration, and Reciprocal Rank Fusion (RRF)-based similarity ranking.

The pipeline was applied to KRAS G12C-targeted drug repurposing using Adagrasib as the reference inhibitor, and validated on an independent dataset of 15358 structurally diverse compounds.

---

## Repository structure

```
├── notebooks/
│   ├── 01_fingerprint_embeddings.ipynb       # Morgan fingerprint computation
│   ├── 02_image_embeddings.ipynb             # 2D image generation + P4-ResNet50 embeddings
│   ├── 03_graph_embeddings.ipynb             # SDF → PyG graphs + GNN embedding extraction
│   ├── 04_gnn_training.ipynb                 # Self-supervised GNN training (SimCLR)
│   ├── 05_nmf_clustering.ipynb               # NMF + GMM clustering
│   ├── 06_svd_clustering.ipynb               # SVD + GMM clustering
│   ├── 07_cross_representation_integration.ipynb  # Cross-modal intersection + candidate extraction
│   └── 08_ranking_rrf.ipynb                  # Multimodal cosine similarity + RRF ranking
├── data/                                     # Input data (see Data section below)
└── README.md
```

---

## Pipeline

```
SDF file
   │
   ├── 01 → fingerprint_embeddings.csv
   ├── 02 → image_embeddings.csv
   └── 03 → graph_embeddings.csv
              │
              ├── 05 (NMF) ─┐
              └── 06 (SVD) ─┴─→ 07 (integration) → candidate_molecules.csv
                                                          │
                                                          └── 08 (RRF) → molecular_ranking_rrf.csv
```

Each of notebooks 05 and 06 must be run **three times** (once per representation: fingerprint, image, graph) by changing the `EMBEDDING_CSV`, `REFERENCE_CSV`, and `OUTPUT_DIR` variables in the configuration cell.

---

## Requirements

```bash
pip install rdkit torch torchvision torch_geometric \
            numpy pandas scikit-learn matplotlib seaborn \
            matplotlib-venn tqdm pillow
```

Python 3.10+ recommended. A GPU is required for notebook 04 (GNN training) and recommended for notebook 03 (graph embedding extraction on large datasets).

---

## Data

### Input data
All molecular datasets used in this work are provided directly in this repository and on Zenodo to ensure exact reproducibility of the results.

| File | Description | Molecules |
|---|---|---|
| `data/molecules_271.sdf` | Primary dataset — Adagrasib-similar compounds | 271 |
| `data/molecules_validation.sdf` | Validation dataset | 15,358 |
| `data/adagrasib.sdf` | Reference molecule (Adagrasib, PubChem CID 2296643) | 1 |

The primary dataset was originally retrieved from [PubChem](https://pubchem.ncbi.nlm.nih.gov/) via structural similarity search (2D Tanimoto ≥ 0.90) using Adagrasib as the query. The validation dataset was retrieved from the [PubChem](https://pubchem.ncbi.nlm.nih.gov/) BioAssay database. Both datasets are redistributed here unchanged for reproducibility purposes under the PubChem open data policy.

The GNN training set (~5M molecules from ZINC22) is available on Zenodo together with the other datasets and model weights at the listed below.

### Pre-trained model weights
The trained GNN encoder weights (`gnn_encoder.pt`) are available on Zenodo:

> **DOI:** [to be assigned upon publication]

Download `gnn_encoder.pt` and place it in the `data/` directory before running notebooks 03.

---

## Reproducing the results

### Full pipeline (from scratch)

Run the notebooks in order:

```
01 → 02 → 03 → (04, optional) → 05 × 3 → 06 × 3 → 07 → 08
```

Notebook 04 (GNN training) can be skipped if you use the pre-trained weights from Zenodo.

### Starting from pre-computed embeddings

If you download the pre-trained GNN weights from Zenodo, you can skip notebook 04 and run notebook 03 directly for embedding extraction.

### Quick reproduction (candidates and ranking only)

If the embedding CSVs are available, you can run notebooks 05–08 directly.

---

## Citation

If you use this code, please cite:

```
@article{bove2025multimodal,
  title   = {A Multimodal Molecular Representation Framework for Robust
             Similarity-Based Drug Repurposing: Application to KRAS G12C
             Inhibitor Identification},
  author  = {Bove, Samantha and others},
  journal = {Bioinformatics},
  year    = {2025},
  note    = {submitted}
}
```

---

## License

MIT License — see `LICENSE` for details.
