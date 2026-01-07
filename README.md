# PanelApp-Enrichment-Discovery-Suite (Shiny)

A multi-tab **R Shiny** application for:
- **Panel enrichment** of a user gene list against **Genomics England PanelApp** panels (Fisher’s exact test + FDR),
- **Discovery panel building** from **HPO terms/IDs**, **gene lists**, or **GO keyword searches**,
- **Network exploration** of shared **HPO**, **GO**, and **PanelApp phenotype** relationships across genes,
- Optional **GSEA** (fgsea) using **MSigDB** collections via msigdbr.

This app is designed for interactive gene list interpretation and rapid iteration. It includes a **universal caching layer** (memory + disk) to avoid repeatedly downloading panel content from PanelApp.

---

## Features

### Tab 2 — Gene Enrichment Tool
Upload a gene table (CSV/TSV/TXT/XLSX/XLS) with a gene symbol column and optionally numeric columns.

**Outputs**
- **Enrichment bar plot** of top enriched panels
- **Enrichment results table** with hits, p-values, q-values (BH/FDR)
- **Volcano plot** (odds ratio vs -log10 p/q) with label options
- **Heatmap** of panel membership across input genes (top panels)
- **Panel similarity heatmap** (Jaccard similarity computed over overlaps with the input gene set)

**Enrichment approach**
For each PanelApp panel:
- Compute overlap between input genes and panel genes.
- Run **Fisher’s exact test** (greater) against a configurable background universe size (`N_BG`, default 20,000).
- Adjust p-values using **Benjamini–Hochberg** to get **q-values (FDR)**.

**Green-only vs Green+Amber**
- Toggle between **Green-only** (confidence level CL=3) and **Green+Amber** (CL≥2) panel gene sets.

---

### Tab 2 — GSEA (optional)
Run gene set enrichment on a ranked gene list using:
- `fgsea` for enrichment
- `msigdbr` for MSigDB gene sets

Collections supported:
- Hallmark (H)
- C2 Canonical Pathways (CP)
- C5 GO Biological Process (BP)

---

### Tab 5 — Discovery Panel Tool
Build and annotate candidate panels using three modes:

#### (1) HPO-based discovery panel
Paste:
- HPO IDs (e.g. `HP:0001250`) and/or
- HPO text terms (e.g. "epilepsy", "developmental delay")

The app maps HPO → genes using **HPO genes_to_phenotype** (`genes_to_phenotype.txt`), then annotates resulting genes with:
- PanelApp panels (Green + Amber and Green-only indexing)
- PanelApp phenotype terms (where available from PanelApp endpoints)

#### (2) Gene-list discovery panel
Upload a gene list and annotate genes with:
- HPO IDs and HPO names
- PanelApp panels (Green vs Amber-only)
- PanelApp phenotypes

#### (3) GO-based discovery panel (local Bioconductor)
Enter GO keyword(s) (e.g. “circadian rhythm”, “chronotype”) and choose GO aspect:
- Biological process (BP)
- Molecular function (MF)
- Cellular component (CC)

This uses local Bioconductor annotation databases:
- `GO.db`
- `org.Hs.eg.db`
- `AnnotationDbi`

and filters to evidence codes (configurable; defaults include EXP/IDA/IMP/etc).

**Downloads**
Tab 5 can export all discovery results to a single Excel file.

---

### Tab 6 — Network Explorer Tool
Paste gene symbols to generate networks of **shared annotations** (default: terms shared by ≥2 genes):
- Gene–HPO network (shared HPO IDs)
- Gene–Phenotype network (PanelApp phenotype terms shared)
- Gene–GO network (shared GO terms)

Outputs:
- Interactive `visNetwork` plots
- Per-gene summary table (HPOs, PanelApp phenotypes, GO terms)
- Graph-level metrics (nodes/edges, clustering, communities via igraph)

---

## Data sources

### PanelApp (Genomics England)
The app pulls panel and gene information from the public PanelApp API:
- Base: `https://panelapp.genomicsengland.co.uk/api/v1`
- Endpoints used include:
  - `/panels/` (panel list)
  - `/panels/{id}/genes/` (panel genes; filtered by confidence)

The app includes retry logic for:
- HTTP 429 (rate limit)
- 5xx server errors
- transient network errors

### HPO genes_to_phenotype
The app uses `genes_to_phenotype.txt`:
- Local file supported (`./genes_to_phenotype.txt` or `./data/genes_to_phenotype.txt`)
- If not found locally, it can download from the HPO GitHub releases:
  - `https://github.com/obophenotype/human-phenotype-ontology/releases/latest/download/genes_to_phenotype.txt`

### GO annotations (local)
GO discovery uses Bioconductor databases:
- `GO.db`
- `org.Hs.eg.db`
- `AnnotationDbi`

### MSigDB gene sets (optional)
GSEA uses:
- `msigdbr` to access MSigDB collections
- `fgsea` to run enrichment

---

## Installation

### 1) Clone the repository
```bash
git clone https://github.com/<your-org-or-user>/<repo-name>.git
cd <repo-name>
