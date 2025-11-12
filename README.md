# Chemically Interpretable ML for HER on Au-Based Alloys — Reproducible Notebooks

This repository accompanies the paper _“Chemically Interpretable Machine Learning for Predicting HER Activity in Au‑Based Alloys”_ and provides a reproducible set of Jupyter notebooks to obtain the HER subset from Mamun _et al._, build physically meaningful features and train and evaluate an Extra Trees model.
  
> **Reproducibility note:** this workflow is **validated on Python 3.11** with **`mendeleev==1.1.0`**. Elemental property values (e.g., Pauling electronegativity, valence electron counts) can change across `mendeleev` versions. Pinning `mendeleev==1.1.0` and using Python 3.11 ensures descriptor values (WEN, WIE, SA/SB, Ψ) match those used in the paper.

---

## Repository layout
```
.
├── data_pre_processing.ipynb
├── build_features_mamun.ipynb
├── extra_trees.ipynb
├── data_raw/                    # put mamun.csv here (not included)
├── data_processed/              # outputs (created by notebooks)
├── mp_element_vol_cache.json
├── requirements.txt
└── README.md
```

## Environment (strict)
- **Python:** 3.11 (required for exact reproducibility of element properties)

### Install (fresh venv)
```bash
# 1) create and activate a Python 3.11 virtual environment
python3.11 -m venv .venv # Windows: py -3.11 -m venv .venv
source .venv/bin/activate # Windows: .venv\Scripts\activate

# 2) install dependencies
pip install -r requirements.txt

## Data
This workflow expects the alloy adsorption dataset of **Mamun _et al._ (Scientific Data, 2019)**. Place the raw CSV as:
```
data_raw/mamun.csv

The notebooks do **not** download data.

Original dataset: Mamun, O.; Winther, K. T.; Boes, J. R.; Bligaard, T. **High‑throughput calculations of catalytic properties of bimetallic alloy surfaces.** _Sci. Data_ **6**, 76 (2019). DOI: 10.1038/s41597-019-0080-z.  

## Quick start
Run the notebooks **in order**. You can run them in JupyterLab/VS Code or from the CLI with `jupyter nbconvert --to notebook --execute`.

1) **Obtain the HER subset**  
`./data_pre_processing.ipynb`, writes `data_processed/mamun_HER_processed.csv`

2) **Build features** (Vegard volume/atom, GCN mapping, WAR, WIE, WEN, SA/SB, Ψ)  
`./build_features_mamun.ipynb`, reads processed CSV, writes `data_processed/mamun_HER_features.csv`  
Uses cached per‑element volumes from `mp_element_vol_cache.json` for full reproducibility (no API key required).

3) **Train & evaluate model** (Extra Trees)  
`./extra_trees.ipynb`, prints test metrics, also includes per‑fold report on the training split.

## Reproducibility & versioning details
- **Fixed seed:** `RND = 42` for all splits and models.
- **Group‑aware data handling:** outer **GroupShuffleSplit** by system (composition–facet–site), and **StratifiedGroupKFold** within the training set using the `is_Au` flag.
- **Element properties:** computed with **`mendeleev==1.1.0`**; values (e.g., `element(sym).en_pauling`, `element(sym).nvalence()`) are version-dependent.
- **Unit volumes:** pulled from the cached `mp_element_vol_cache.json`, included here to ensure reproducibility.

## Attributions & references
- **Dataset** — Mamun, O.; Winther, K. T.; Boes, J. R.; Bligaard, T. _Sci. Data_ **6**, 76 (2019). DOI: 10.1038/s41597-019-0080-z.  
- **Feature mappings** — Martínez‑Alonso, C. _et al._ **Application of machine learning to discover new intermetallic catalysts for the hydrogen evolution and the oxygen reduction reactions.** _Catalysis Science & Technology_ (2024). DOI: 10.1039/D4CY00491D.  
- **Periodic table data** — `mendeleev` library (Mentel, Ł.), MIT‑licensed.  

## License
Code in this repository is released under the **MIT License** (see `LICENSE`).  
_No third‑party datasets are redistributed here._