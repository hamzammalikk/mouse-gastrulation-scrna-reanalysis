# Mouse gastrulation scRNA-seq: re-analysis of E6.5, E7.0 and E7.5

A step-by-step re-analysis of a subset of the Pijuan-Sala et al. (2019) mouse gastrulation atlas using Scanpy and scVI. This is a learning project: the aim was to practise a careful single-cell workflow and to document its pitfalls, **not** to report new biology.

Author: Muhammad Hamza Malik. Built with guidance from Claude (Anthropic) acting as a mentor; every step was run and inspected by the author.

## Data

- Source: Pijuan-Sala et al. 2019, *A single-cell molecular map of mouse gastrulation and early organogenesis*, Nature 566, 490-495. Accession E-MTAB-6967 (10x Genomics, mouse).
- ArrayExpress holds the raw sequencing files. This project uses the authors' processed count matrix and metadata, which the notebook downloads from their server: `https://content.cruk.cam.ac.uk/jmlab/atlas_data/`.
- Data are **not** included in this repository.
- Subset analysed: E6.5, E7.0 and E7.5 = 15 samples, 33,144 cells (the full file has 139,331 cells, of which 116,312 are the labelled published atlas).

## What the notebook does

1. Download metadata, inspect design (samples, pools, batches), hold out the authors' labels and QC flags.
2. Stream the 1.5 GB matrix in chunks, keep the chosen cells, preserve raw counts.
3. QC metrics, per-sample flags (nothing removed), Scrublet doublet scores.
4. Normalisation, HVG-2000 PCA, neighbours, UMAP, Leiden at four resolutions.
5. HVG-6000 comparison and scVI integration.
6. Independent marker-based annotation, frozen before the authors' labels are opened.
7. PAGA and diffusion pseudotime on embryonic groups, with sensitivity runs.
8. Comparison with the authors' annotation and QC flags.

## Main observations

- Three low-depth samples (14, 15, 18) form their own clusters in HVG-PCA, HVG-6000 and scVI. Stage, pool and sample are confounded, so the cause could not be isolated.
- 11.8% of the subset (1,832 stripped nuclei and 2,085 doublets by the authors' flags) were still present in the main analyses. Our own QC missed most of them.
- At about 17 clusters, agreement with the authors' labels (ARI / NMI) was similar for HVG-2000 (about 0.55 / 0.71) and scVI (0.58 / 0.69). Resolution mattered more than representation.
- Pseudotime ordered cell types as epiblast, posterior epiblast/streak, streak, mesendoderm, mesoderm in every run. Within-stage ordering was less reliable.
- Errors found and corrected along the way (an invalid 6,000-gene run, wrong group labels) are recorded in the decision log.

## Limitations

Replication is at pool level only (the number of embryos per pool is unknown). Data were pre-filtered by the authors. Definitive endoderm is under-sampled, one group labelled posterior epiblast/streak was 40% rostral neurectoderm in the authors' labels, and all clustering parameters are conventions, not optimised. The authors' labels are an independent annotation, not ground truth. Pseudotime roots use stage information, so agreement with real time is partly circular.

## How to run

Use Google Colab with a T4 GPU runtime (only scVI needs the GPU). Upload `project2_gastrulation_scanpy.ipynb` and run the cells in order. Outputs are written to `MyDrive/gastrulation_project/nb_run/`. The notebook contains `assert` checks that stop the run if a verified number differs. Cluster numbers can vary slightly across library versions, so check the label cell against the marker dot plots before trusting it. Library versions are printed at the start and logged to `analysis_log_nb.md`.

## Files

- `project2_gastrulation_scanpy.ipynb`: the notebook
- `figure_composite.png`: stage, annotation, pseudotime and PAGA panels
- `decision_log_and_limitations.pdf`: every analytical decision and its limits
