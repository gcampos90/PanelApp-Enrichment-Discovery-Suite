# PanelApp Enrichment Suite

A Shiny application for analysing gene lists against Genomics England PanelApp Green genes and generating discovery panels from user-supplied genes, Human Phenotype Ontology (HPO) terms, or Gene Ontology (GO) annotations.

---

## Overview

The PanelApp Green-Gene Analysis Suite provides two complementary tools:

### 1. Panel Enrichment Tool

Performs enrichment testing to answer the question: 
- Which disease categories (panels) are most enriched for given gene list ?

Features:

- Automatic retrieval of all Green genes from PanelApp
- Local caching of PanelApp data for faster subsequent analyses
- Fisher's Exact Test enrichment analysis
- Benjamini-Hochberg FDR correction
- Enrichment bar plots
- Gene-panel membership heatmaps
- Export of results as CSV, Excel, and PNG

### 2. Discovery Panel Tool

Builds candidate disease gene panels based on:

- Uploaded gene lists
- Human Phenotype Ontology (HPO) terms or IDs
- Gene Ontology (GO) keywords

Results are intersected with all Green PanelApp panels to identify known disease-relevant genes and associated phenotypes.

---

## Features

### PanelApp Integration

- Retrieves live panel information through the Genomics England PanelApp API
- Uses Green genes only (confidence level 3)
- Automatically caches datasets locally
- Supports manual cache refresh

### Gene List Support

Accepted file formats:

- CSV (`.csv`)
- TSV (`.tsv`)
- TXT (`.txt`)
- Excel (`.xlsx`)
- Excel (`.xls`)

The application automatically attempts to identify the gene symbol column.

### HPO Discovery

Supports:

- HPO identifiers (`HP:0001250`)
- HPO terms (`Seizures`, `Retinal dystrophy`, etc.)

Gene annotations are obtained from the official Human Phenotype Ontology phenotype-to-gene annotation database.

### GO Discovery

Supports Gene Ontology searches in:

- Biological Process (BP)
- Molecular Function (MF)
- Cellular Component (CC)

Genes are retrieved using:

- GO.db
- org.Hs.eg.db

---

## Workflow

### Panel Enrichment Tool

1. Upload a gene list.
2. Select the gene-symbol column.
3. Run enrichment analysis.
4. Review:
   - Enriched panels
   - FDR-adjusted significance
   - Overlapping genes
   - Membership heatmap
5. Export results.

### Discovery Panel Tool

1. Choose an input type:
   - Uploaded gene list
   - HPO terms/IDs
   - GO keywords
2. Generate the discovery panel.
3. Review:
   - Green PanelApp overlap
   - Associated panels
   - Disease phenotypes
4. Export the final panel.

---

## Statistical Method

Panel enrichment is calculated using:

- Fisher's Exact Test (one-sided)
- Background universe consisting of all Green PanelApp genes
- Benjamini-Hochberg False Discovery Rate (FDR) correction

For each panel the application reports:

- Odds ratio
- P-value
- FDR-adjusted q-value
- Number of overlapping genes
- Overlapping gene symbols

---

## Installation

### CRAN Packages

```r
install.packages(c(
  "shiny",
  "DT",
  "httr",
  "jsonlite",
  "dplyr",
  "tibble",
  "purrr",
  "stringr",
  "readxl",
  "readr",
  "ggplot2",
  "pheatmap",
  "scales",
  "writexl"
))
```

### Bioconductor Packages

```r
if (!requireNamespace("BiocManager", quietly = TRUE))
  install.packages("BiocManager")

BiocManager::install(c(
  "AnnotationDbi",
  "GO.db",
  "org.Hs.eg.db"
))
```

---

## Running the Application

```r
shiny::runApp("app.R")
```

or simply:

```r
source("app.R")
```

if running interactively from RStudio.

---

## Output Files

### Panel Enrichment

- Enrichment results (CSV)
- Enrichment results (Excel)
- Bar plot (PNG)
- Membership heatmap (PNG)

### Discovery Tool

- Discovery panel (CSV)
- Discovery panel (Excel)

Excel exports include supplementary worksheets containing:

- Input genes
- HPO matches (when applicable)
- GO matches (when applicable)
- Gene-to-term associations

---

## Data Sources

### Genomics England PanelApp

https://panelapp.genomicsengland.co.uk/

### Human Phenotype Ontology

https://hpo.jax.org/

### Gene Ontology

http://geneontology.org/

### Bioconductor Annotation Packages

- GO.db
- org.Hs.eg.db
- AnnotationDbi

---

## Notes

- Only Green PanelApp genes (confidence level 3) are used.
- Initial cache generation may take several minutes because all PanelApp panels are downloaded.
- Subsequent analyses are significantly faster due to local caching.
- The cache can be manually refreshed from the application interface.

---

## Intended Use

This tool is intended for:

Research support only and should not be used as the sole basis for clinical decision-making.

---

## Author

Guilherme Fernandes Campos
Developed for genomic panel exploration, enrichment analysis, and discovery panel generation using Genomics England PanelApp resources.
