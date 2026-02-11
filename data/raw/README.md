# Raw Data

Raw data is available from EBI BioStudies as an RO-Crate:

**Accession**: S-VHPS20
**URL**: https://www.ebi.ac.uk/biostudies/vhp4safety/studies/S-VHPS20

See [`DOWNLOAD_INSTRUCTIONS.md`](DOWNLOAD_INSTRUCTIONS.md) for detailed download steps.

## RO-Crate Contents

The complete RO-Crate archive (~31 GB) contains:

| File | Size | Description |
|------|------|-------------|
| **Processed Data** | | |
| `Processed data_RNAseq_T3.xlsx` | 11 MB | Pre-processed differential expression results (ready to use) |
| **Raw Data** | | |
| `*.fastq` (32 files) | ~30 GB | Raw sequencing reads from Illumina |
| `CountTable.txt` | 6 MB | Raw gene counts per sample (output from read alignment) |
| `Annotation.txt` | 12 MB | Gene annotation data (Ensembl IDs, symbols, coordinates) |
| `Design.txt` | <1 KB | Experimental design and sample metadata |
| **Processing Scripts** | | |
| `Rscript analyses SI G&G.r` | 37 KB | Differential expression analysis pipeline (CountTable → Processed data) |
| `seq_template_RNAseq_molAOP.xlsx` | 370 KB | Sample metadata template and protocol documentation |
| **Metadata** | | |
| `README.txt` | <1 KB | Brief file descriptions |
| `ro-crate-metadata.json` | 19 KB | RO-Crate metadata |
| `md5 sums.txt` | <1 KB | MD5 checksums for FASTQ files |

## Data Processing Pipeline

### Full Pipeline (from raw reads to processed data)

```
FASTQ files (32 samples)
    ↓ [Alignment & Quantification - external pipeline]
CountTable.txt (raw counts)
    ↓ [Rscript analyses SI G&G.r]
    ├─ Variance Stabilizing Transformation (VST)
    ├─ DESeq2 differential expression analysis
    ├─ Multiple comparisons: CTR-T3L, CTR-T3H, T3L-T3H, NPC-CTR
    ├─ Statistical filtering (p < 0.001, padj < 0.05, |log2FC| ≥ 0.5)
    └─ Gene annotation (Biomart)
Processed data_RNAseq_T3.xlsx
    ↓ [Convert to CSV]
T3_expression_data.csv (required for notebooks)
```

### R Script Details

`Rscript analyses SI G&G.r` is the differential expression analysis pipeline that processes `CountTable.txt` into `Processed data_RNAseq_T3.xlsx`.

**Processing**: DESeq2 variance stabilizing transformation, differential expression testing for multiple comparisons (CTR-T3L, CTR-T3H, T3L-T3H, NPC-CTR), statistical filtering (p < 0.001, padj < 0.05), and gene annotation.

**Note**: This script was used to generate the processed data. Users do not need to run it unless reproducing from raw counts.

## Processed Data Format

`Processed data_RNAseq_T3.xlsx` contains **37,424 rows** (genes) and **28 columns**:

### Expression Values
- `NPC`, `CTR`, `T3L`, `T3H`: Normalized log2 expression values per condition

### Fold Changes
- `CTR - NPC`: log2 fold change (CTR vs NPC)
- `T3L-CTR`: log2 fold change (T3L vs CTR)
- `T3H-CTR`: log2 fold change (T3H vs CTR)

### Significance Flags (binary 0/1)
- `CTR-T3L`: Significant in hypothyroid comparison
- `CTR-T3H`: Significant in hyperthyroid comparison
- `T3L-T3H`: Significant in dose comparison
- `CTR-T3L-T3H FC 0.5`: Significant in 3-group comparison (|log2FC| ≥ 0.5)
- `CTR-T3L-T3H FC 0`: Significant in 3-group comparison (any FC)
- `NPC-CTR FC 0.5`: Significant in NPC-CTR (|log2FC| ≥ 0.5)
- `NPC-CTR FC 0`: Significant in NPC-CTR (any FC)

### Gene Annotations
- `GeneSymbol`: HGNC gene symbol
- `Gene ID (Biomart)`: Ensembl gene ID
- `Gene symbol (Biomart)`: Alternative gene symbol from Biomart
- `Chromosome`, `Start`, `Stop`, `Strand`: Genomic coordinates
- `GeneType`: Gene biotype (protein_coding, lncRNA, etc.)
- `transcriptIDs`, `transcriptStart`, `transcriptStop`: Transcript information

### Metadata
- `Expression(log2)`: Ensembl gene ID (index column)
- `Ratios`, `Venn (DEGs)`: Internal processing flags

## Usage in Analysis Notebooks

The first notebook (`01_T3dataHandling.ipynb`) expects:

**Input**: `T3_expression_data.csv` (CSV version of the Excel file)

**Location**: `data/raw/T3_expression_data.csv` (git-ignored, download required)

**Columns used**:
- `GeneSymbol`: For gene identification
- `Gene ID (Biomart)`: For cross-referencing
- `NPC`, `CTR`, `T3L`, `T3H`: Expression values
- `CTR-T3L`, `T3L-T3H`: Significance flags

The notebook:
1. Calculates manual log2 fold changes (CTR - T3L, T3H - T3L)
2. Filters by significance flags
3. Exports condition-specific files to `data/processed/`

## Downloading the Data

**Quick Start**: Download only the processed data file (~11 MB):

```bash
# Extract from RO-Crate
unzip S-VHPS20_ro-crate_*.zip "Processed data_RNAseq_T3.xlsx" -d data/raw/

# Convert to CSV
python3 -c "import pandas as pd; \
df = pd.read_excel('data/raw/Processed data_RNAseq_T3.xlsx'); \
df.to_csv('data/raw/T3_expression_data.csv', index=False)"
```

**Full Instructions**: See [`DOWNLOAD_INSTRUCTIONS.md`](DOWNLOAD_INSTRUCTIONS.md)

## Reproducibility Notes

- The FASTQ files in the RO-Crate allow complete reproduction from raw reads
- The R script documents the exact DESeq2 analysis parameters used
- The processed Excel file can be independently verified by re-running the R script
- All processing was performed in R 4.x with DESeq2 version documented in the manuscript

## Questions or Issues

For questions about:
- **Data content**: Contact the manuscript corresponding authors
- **BioStudies access**: Contact EBI BioStudies support
- **Analysis code**: Open an issue in this GitHub repository
