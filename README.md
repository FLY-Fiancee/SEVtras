## SEVtras delineates small extracellular vesicles at droplet resolution from single-cell transcriptomes
SEVtras stands for <ins>sEV</ins>-con<ins>t</ins>aining d<ins>r</ins>oplet identific<ins>a</ins>tion in <ins>s</ins>cRNA-seq data.

You can freely use SEVtras to explore sEV heterogeneity at single droplet, characterize cell type dynamics in light of sEV activity and unlock diagnostic potential of sEVs in concert with cells.

<p align="center">
  <img src='./docs/SEVtras_overview.png'>
</p>
<p align="center">
  Overview of SEVtras.
</p>

### Prerequisites
    "numpy", "pandas", "scipy",
    "scanpy", "statsmodels", "gseapy"

### Installation
```bash
pip install SEVtras
```
We also suggest to use a separate conda environment for installing SEVtras.
```bash
conda create -y -n SEVtras_env python=3.7
conda activate SEVtras_env
pip install SEVtras
```

### Basic Usage
Input for SEVtras is a cell-by-gene matrix. In the case of scRNA-seq dataset using 10X Genomics, we used `raw_feature_bc_matrix` directory generated by Cell Ranger as input. Output of SEVtras consists of the score of sEV signals and classification for each droplet. Such sEV information will be used for downstream analysis and as basis for the construction of the sEV secretion activity index (ESAI) for different cell types.

We implemented four functions for sEV recognizing and functional analyses. In SEVtras, `sEV_recognizer` recognizes sEV-containing droplets in the raw scRNA-seq data; `ESAI` calculates sEV secretion activity for samples; `ESAI_celltype` deconvolves these droplets to their original cell type and estimited corresponding sEV secretion activity; and `cellfree_simulator` simulates transcriptional profile of cell free droplets in scRNA-seq.

### Examples
For a study with multiple scRNA-seq samples, we support two input ways.
```bash
### Optional1:    if samples locate in one directory
SEVtras.sEV_recognizer(input_path='directory_path', sample_file='sample_name', out_path='output_path', species='Homo')
### Optional2:   if samples locate in differet directories
SEVtras.sEV_recognizer(sample_file='sample_path', out_path='output_path', species='Homo')
```
In the first way, `directory_path` is the path for the directory that contains all sample; and `sample_name` is the file that list each sample in th directory row by row. Every sample should contain directory structure like 'sample/outs/raw_feature_bc_matrix/', and SEVtras would automatically recognize for each sample. The second way supports one sample file with abosulte path for each sample, if they are not in one directory.

And `out_path` defines the output of SEVtras that is one h5ad file, named 'raw_SEVtras.h5ad', with SEVtras score and sEV classification in the 'obs' for all droplets, and one named 'sEV_SEVtras.h5ad' with only SEVtras-containing droplets.

Then, we could deconvolve these recoginzed sEVs to original cell type and calculate the sEV secretion activity for certain cell types.
```bash
celltype_sEV_number, adata_sEV, adata_combined = SEVtras.deconvolver(adata_sEV='sEV_SEVtras.h5ad', adata_cell='cells.h5ad', OBSsample='batch', OBScelltype='celltype')

sEV_activity_dat = SEVtras.ESAI_celltype(adata_sEV='sEV_SEVtras.h5ad', adata_cell='cells.h5ad', OBSsample='batch', OBScelltype='celltype')
```
In the parameters, the two h5ad files represent the sEV- and cell- anndata files. And `batch` and `celltype` are the indexes of sample names and cell types in the anndata of cells. The output of `source_tracker` is in the 'obsm' of 'adata_sEV' indexed as 'source'.

Further tutorials please refer to  https://SEVtras.readthedocs.io/.

