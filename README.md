# GTVelo: Cell Fate Prediction with Graph Attention

Final group project for a deep learning in genomics course, Brown University (2025).

GTVelo predicts what a cell will become during development. It builds a graph of single-cell RNA-seq data, weights the edges using RNA velocity, and trains a Graph Transformer to classify the cell types that appear at later developmental timepoints.

Materials: [`paper`](./GTVelo__DL_in_Genomics_Research_Paper.pdf) and [`slides`](./'GTVelo (1).pdf')

## Overview

Single-cell sequencing gives snapshots of cells but no direct view of time. Most existing trajectory methods reconstruct developmental paths after the fact, so they are tuned to explain data they have already seen. We instead frame fate prediction as a forward-looking task: train only on cells from earlier timepoints, then test on cells from later ones, so the model cannot simply memorize outcomes.

## Approach

- **Data:** the Mouse Gastrulation Atlas (erythroid lineage, 9,815 cells) and the Mouse Organogenesis Cell Atlas (neuron lineage, 141,576 cells; blood lineage, 53,268 cells).
- **Preprocessing:** quality filtering, normalization, selection of 2,000 highly variable genes, and PCA to 50 dimensions, with RNA velocity estimated using scVelo.
- **Graph construction:** each cell is a node connected to its 15 nearest neighbors in PCA space. Edge weights combine cosine similarity of expression with alignment to the RNA velocity direction (weighted 0.7 toward velocity).
- **Model:** a 3-layer Graph Transformer with 4-head attention, residual connections, and layer normalization, trained with class-weighted cross-entropy and early stopping.
- **Evaluation:** temporal holdout (train on earlier timepoints, test on later ones), compared against logistic regression, random forest, kNN, and a GCN using accuracy, macro F1, and weighted F1.

## Key Findings

- GTVelo beat the baselines on the erythroid dataset (accuracy 0.773, weighted F1 0.784) and on the blood dataset (highest weighted F1), and matched logistic regression on the neuron dataset (weighted F1 0.755).
- It also held up on rare cell subtypes where kNN and random forest often failed completely.
- Ablations showed RNA velocity weighting did not consistently help. Results were mixed across datasets, which may point to noisy velocity estimates or a poorly tuned weighting parameter.
- Early interpretability work found that attention weights correlated with velocity alignment in some cases and only weakly in others, so how the model uses velocity is still an open question.

## Limitations and Future Work

Limitations include no comparison against state-of-the-art fate tools, mouse data only, and a separate model per dataset. Next steps are tuning how velocity is combined with similarity, supervising with CellRank-derived labels, trying deeper or wider architectures, and scaling to more of MOCA and other whole-organism datasets.

## My Contribution

- Proposed the project idea
- Found and prepared the datasets, including cleaning and preprocessing
- Developed the experiments and the GTVelo model
- Carried out the interpretability analysis (attention weights and neighbor saliency)

## Repository Contents

- [`GTVelo__DL_in_Genomics_Research_Paper.pdf`](./GTVelo__DL_in_Genomics_Research_Paper.pdf): research paper
- [`GTVelo(1).pdf`]('./GTVelo (1).pdf'): presentation slides

The code is not included in this repository.

## Team

- Angelic McPherson ([@Angelic-Work](https://github.com/Angelic-Work))
- Stephen Yang
- Erica Shivers
- Hanting Li
