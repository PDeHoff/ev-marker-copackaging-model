# EV Tetraspanin Co-packaging Analysis

Statistical modeling of tetraspanin (CD9, CD81, CD63) co-packaging on extracellular vesicles (EVs) from Single Molecule Flow Cytometry (SMFC) data.

## Overview

This repository contains two Jupyter notebooks that implement a complete analysis pipeline: a model-fitting engine that processes raw SMFC data and a visualization layer that generates publication figures from the results.

The analysis quantifies whether tetraspanins are independently distributed across individual EVs or co-packaged beyond what marginal frequencies alone would predict, using linkage disequilibrium coefficients (D) fitted within a probabilistic finite mixture model framework.

---

## Repository Structure

```
├── EV_Tetraspanin_Analysis_Rewrite.ipynb   # Model fitting and statistics → Tables.xlsx
├── EV_Phenotype_Charts_v24c.ipynb          # Figure generation → SVG/PNG/PDF outputs
├── Phenotype.csv                            # Raw EV phenotype counts (input)
├── CD9_CNV.csv                             # CD9 copy number per EV (input)
├── CD81_CNV.csv                            # CD81 copy number per EV (input)
├── CD63_CNV.csv                            # CD63 copy number per EV (input)
└── Figures/
    └── Tables.xlsx                          # Analysis output (input to Charts notebook)
```

---

## Workflow

Run the notebooks in order:

**Step 1 — Analysis** (`EV_Tetraspanin_Analysis_Rewrite.ipynb`)
Reads raw CSV data → fits all six models → writes results to `Tables.xlsx`

**Step 2 — Figures** (`EV_Phenotype_Charts_v24c.ipynb`)
Reads `Tables.xlsx` → generates all publication figures

If only figure appearance needs to change, only Step 2 needs to be re-run.

---

## Analysis Notebook — Cell Structure

| Cell | Description |
|------|-------------|
| 1.00 | Imports |
| 1.01 | Configuration — file paths, model parameters, regularization settings |
| 1.02 | Copy number (CNV) data loading and subpopulation fraction estimation |
| 1.03 | Phenotype data loading and validation |
| 1.04 | Independence analysis — pairwise and three-way chi-square, Cramér's V, odds ratios, D coefficients |
| 1.05 | Model engine — direct multinomial probability model functions |
| 1.06 | Independent assortment models (Options 1A, 2A, 3A) — MLE fitting |
| 1.07 | Linked assortment models (Options 1B, 2B, 3B) — MLE fitting, bootstrap CIs, profile likelihood CIs |
| 1.08 | Replicate robustness analysis — LOO cross-validation, CV of mixing fractions |

---

## Models

Six candidate models evaluated across two axes:

| Model | Class structure | Assortment | Free parameters (k) |
|-------|----------------|------------|---------------------|
| 1A | 1 class | Independent | 3 |
| 1B | 1 class | Linked | 5 |
| 2A | 2 classes (background + CD63-high) | Independent | 7 |
| 2B | 2 classes (background + CD63-high) | Linked | 10 |
| 3A | 3 classes (background + CD63-high + CD9-high) | Independent | 11 |
| 3B | 3 classes (background + CD63-high + CD9-high) | Linked | 15 |

Linked models fit a linkage disequilibrium coefficient D for each tetraspanin marker pair within each class. Model selection uses BIC as the primary criterion (N = 18,007; BIC penalty = ln(N) ≈ 9.8 NLL units per parameter). **Model 2B is the biologically preferred model.**

---

## Input Files

All input files are located in:
```
C:\Users\pdeho\OneDrive - University of California, San Diego Health\Projects\Exosomes\CopyPatterns_DiFI\
You'll need to change this on your own.
Everything is under WSL.
```

| File | Description |
|------|-------------|
| `Phenotype.csv` | Binary phenotype counts for all 8 CD9/CD81/CD63 combinations across 5 replicates (N = 18,007 EVs) |
| `CD9_CNV.csv` | CD9 copy number distribution per EV per replicate |
| `CD81_CNV.csv` | CD81 copy number distribution per EV per replicate |
| `CD63_CNV.csv` | CD63 copy number distribution per EV per replicate |

---

## Outputs

The analysis notebook writes intermediate and final results to `outputs/` as timestamped `.xlsx` files. The final consolidated output is `Tables.xlsx` (Supplementary Tables S7A–S7L), which is the input to the figures notebook.

The figures notebook writes to `Figures/Charts/`:
- `FigureA` — Observed EV phenotype frequencies
- `FigureB/C/D` — CD9, CD81, CD63 copy number distributions
- `FigureE` — Linkage disequilibrium coefficients D with 95% CIs
- `FigureF` — Observed vs predicted phenotype frequencies, all six models
- `Figure_Combined` — All panels as a single composite figure (SVG, PNG, PDF)

---

## Requirements

```
python >= 3.12
numpy
scipy
pandas
matplotlib
openpyxl
cairosvg    # optional — PNG export of composite figure (requires Cairo DLL on Windows)
svglib      # optional — PDF export of composite figure
reportlab   # optional — PDF export of composite figure
```

Install dependencies:
```bash
pip install numpy scipy pandas matplotlib openpyxl svglib reportlab
```

---

## Data

Source: Single Molecule Flow Cytometry of DiFi colorectal cancer cell line-conditioned medium (5 technical replicates). Each EV classified as positive or negative for CD9 (PE), CD81 (PE-Cy7), and CD63 (AF647) using ANEPPS membrane dye gating.
