# Downloading Data from BioStudies

This guide provides step-by-step instructions for downloading the required data files from EBI BioStudies to run the analysis notebooks.

## BioStudies Entry

**Study Accession**: S-VHPS20
**URL**: https://www.ebi.ac.uk/biostudies/vhp4safety/studies/S-VHPS20
**Title**: RNA-seq data for T3 effects in human neural progenitor cells

## Required File for Analysis

To run the analysis notebooks in this repository, you need the **processed expression data file**:

### Option 1: Download Pre-Processed Data (Recommended)

**File**: `Processed data_RNAseq_T3.xlsx`
**Size**: ~11 MB
**Description**: Pre-processed differential expression results with normalized log2 values and significance flags

This file contains:
- 37,424 genes (rows)
- Normalized log2 expression values for NPC, CTR, T3L, T3H conditions
- Gene symbols and Biomart annotations
- Significance flags for CTR-T3L, CTR-T3H, and T3L-T3H comparisons

### Option 2: Download Raw Data (Advanced)

If you want to reproduce the entire processing pipeline from raw counts:

**Files needed**:
- `CountTable.txt` (~6 MB) - Raw gene counts per sample
- `Annotation.txt` (~12 MB) - Gene annotation data
- `Design.txt` (<1 KB) - Experimental design
- `Rscript analyses SI G&G.r` (~37 KB) - R processing script

## Download Steps

### Step 1: Access BioStudies

1. Visit https://www.ebi.ac.uk/biostudies/vhp4safety/studies/S-VHPS20
2. You will see the study details and files section

### Step 2: Download the RO-Crate

The complete dataset is available as an RO-Crate (Research Object archive):

1. Look for the "Files" section or "Download" button
2. Download `S-VHPS20_ro-crate_YYYYMMDD.zip` (~31 GB, includes all files)
   - **Note**: The archive is large because it contains 32 FASTQ files (~30 GB)
   - If you only need the processed data, see Step 3 for selective extraction

### Step 3: Extract Required Files

After downloading the RO-Crate zip file, extract only the files you need:

#### For standard analysis (recommended):

```bash
# Extract just the processed data file
unzip S-VHPS20_ro-crate_*.zip "Processed data_RNAseq_T3.xlsx" -d data/raw/
```

#### For full pipeline reproduction:

```bash
# Extract all processing-related files
unzip S-VHPS20_ro-crate_*.zip \
  "CountTable.txt" \
  "Annotation.txt" \
  "Design.txt" \
  "Rscript analyses SI G&G.r" \
  "seq_template_RNAseq_molAOP.xlsx" \
  -d data/raw/
```

### Step 4: Convert Excel to CSV

The Python notebooks expect CSV format. Convert the Excel file:

```bash
# Using Python/pandas
python3 -c "import pandas as pd; df = pd.read_excel('data/raw/Processed data_RNAseq_T3.xlsx'); df.to_csv('data/raw/T3_expression_data.csv', index=False)"
```

Or use the conversion script in this repository:

```bash
# If available
python scripts/convert_excel_to_csv.py
```

## Verification

After completing the download and conversion, verify you have:

- [ ] File `data/raw/T3_expression_data.csv` exists
- [ ] File size is approximately 10-12 MB
- [ ] File contains 37,424 rows (genes) + 1 header row
- [ ] File has 28 columns including: GeneSymbol, NPC, CTR, T3L, T3H, CTR-T3L, CTR-T3H, T3L-T3H

You can verify the structure with:

```bash
# Check dimensions
wc -l data/raw/T3_expression_data.csv  # Should show ~37,425 lines
head -1 data/raw/T3_expression_data.csv | tr ',' '\n' | nl  # List columns
```

## Alternative: Direct File Download (If Available)

Some browsers may allow direct download of individual files from the BioStudies Files tab:

1. Navigate to the Files section on the study page
2. Right-click on `Processed data_RNAseq_T3.xlsx`
3. Select "Save Link As..." or "Download Linked File"
4. Save to `data/raw/` directory
5. Convert to CSV as shown in Step 4

## Processing Raw Data from Scratch (Optional)

If you downloaded the raw data files and want to reproduce the processing:

### Requirements

- R (≥4.0)
- R packages: DESeq2, limma, gplots, viridis, rgl

### Processing Steps

1. Ensure you have extracted:
   - CountTable.txt
   - Annotation.txt
   - Design.txt
   - Rscript analyses SI G&G.r

2. Run the R script:

```r
# In R
setwd("data/raw/")
source("Rscript analyses SI G&G.r")
```

The script performs:
- Variance stabilizing transformation (VST) normalization
- DESeq2 differential expression analysis (multiple comparisons)
- Gene filtering (p-value < 0.001, padj < 0.05, |log2FC| ≥ 0.5)
- Annotation with gene symbols

**Output**: Produces the same data as `Processed data_RNAseq_T3.xlsx`

## Troubleshooting

### Large Download Size

The complete RO-Crate is ~31 GB because it includes all 32 FASTQ files. These are only needed if you want to re-run the full sequencing analysis pipeline (alignment, quantification). For the analyses in this repository, you only need the processed data (~11 MB).

### Excel File Not Opening

If Excel fails to open the file, use Python/pandas or LibreOffice Calc. The file is standard XLSX format.

### Missing Columns After Conversion

Ensure you're using `index=False` when converting to CSV to avoid creating an extra index column.

### File Encoding Issues

The data uses UTF-8 encoding. If you encounter encoding errors, specify:

```python
df.to_csv('data/raw/T3_expression_data.csv', index=False, encoding='utf-8')
```

## Data Format Specification

The expected format for `T3_expression_data.csv`:

- **Separator**: Comma (`,`)
- **Encoding**: UTF-8
- **Rows**: 37,424 genes + 1 header
- **Key columns**:
  - `GeneSymbol` (str): HGNC gene symbol
  - `Gene ID (Biomart)` (str): Ensembl gene ID
  - `NPC`, `CTR`, `T3L`, `T3H` (float): Normalized log2 expression values
  - `CTR-T3L`, `CTR-T3H`, `T3L-T3H` (int): Binary significance flags (0 or 1)

## Support

For issues with:
- **BioStudies access**: Contact EBI BioStudies support
- **Analysis code**: Open an issue in this repository
- **Data content**: See the manuscript or contact the corresponding authors

## Citation

When using this data, please cite:

- **BioStudies entry**: S-VHPS20, https://www.ebi.ac.uk/biostudies/vhp4safety/studies/S-VHPS20
- **This repository**: See main README.md for Zenodo citation
- **Manuscript**: Martens et al. (in preparation)
