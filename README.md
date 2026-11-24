# Do complex models beat simple baselines at labeling immune cells?

No. On 700 human immune cells, logistic regression scored best (70.0% balanced accuracy), a simple mean-profile classifier was close behind (68.8%), and a random forest and a neural network did worse. The remaining errors sit in CD4 T-cell subtypes that the data itself cannot separate well.

## Data

700 human peripheral blood mononuclear cells (PBMCs) x 765 genes, 10 cell types, from 10x Genomics (Zheng et al., 2017), loaded with `scanpy.datasets.pbmc68k_reduced()`. No download needed.

Classes are imbalanced: dendritic cells are 34% of cells, naive CD4 T cells only 1%. So models are scored with balanced accuracy and macro-F1, which weight every cell type equally, not just accuracy.

## Results

5-fold stratified cross-validation, mean ± standard deviation across folds.

| Model | Accuracy | Balanced accuracy | Macro-F1 |
| --- | --- | --- | --- |
| Most-common-class baseline | 34.3% ± 0.0% | 10.0% ± 0.0% | 0.05 ± 0.00 |
| Mean profile (nearest centroid) | 80.4% ± 3.6% | 68.8% ± 3.7% | 0.69 ± 0.03 |
| **Logistic regression** | **83.7% ± 2.5%** | **70.0% ± 2.4%** | **0.70 ± 0.03** |
| Random forest | 80.9% ± 2.4% | 67.4% ± 2.0% | 0.65 ± 0.02 |
| Neural network (MLP) | 79.7% ± 2.7% | 65.1% ± 3.7% | 0.64 ± 0.04 |

## Findings

1. **Simple models matched or beat complex ones.** Logistic regression's lead over the mean-profile baseline is smaller than the fold-to-fold variation.
2. **Every model fails on the CD4 T-cell subtypes** (memory, naive, regulatory). Unsupervised Leiden clustering also mixes them (adjusted Rand index 0.40), and marker genes don't separate them. The genes that would (FOXP3, IL2RA) aren't in this 765-gene dataset, so the limit is the data, not the models.
3. **The models learned real biology.** Their top genes include known markers such as CD79A and MS4A1 (B cells), NKG7 and GNLY (NK cells), and CST3 and LYZ (myeloid cells).

## Notebooks

| Notebook | What it does |
| --- | --- |
| `01_explore.ipynb` | Load the data and run quality control |
| `02_classify.ipynb` | First classifier on a single train/test split |
| `03_compare_models.ipynb` | Five models compared with 5-fold cross-validation |
| `04_clusters_markers.ipynb` | UMAP, Leiden clustering and marker genes |
| `05_errors_genes.ipynb` | Most common mistakes and most important genes |

## Run it

```bash
git clone https://github.com/ngalatas42/pbmc-celltype-ml.git
cd pbmc-celltype-ml
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
jupyter lab
```

Then open the notebooks in order and choose Kernel > Restart Kernel and Run All Cells.

## Tools

Python, Scanpy (AnnData), scikit-learn, pandas, matplotlib, leidenalg, Jupyter, git

## Reference

Zheng, G. X. Y. et al. (2017). Massively parallel digital transcriptional profiling of single cells. *Nature Communications*, 8, 14049.
