# Raw Data

Raw data is available from EBI BioStudies as an RO-Crate:

**Accession**: S-VHPS20
**URL**: https://www.ebi.ac.uk/biostudies/vhp4safety/studies/S-VHPS20

## RO-Crate Contents

| File | Description |
|------|-------------|
| `*.fastq` | Raw sequencing reads (32 files) |
| `CountTable.txt` | Raw gene counts per sample |
| `seq_template_RNAseq_molAOP.xlsx` | Sample metadata |
| R script (TBD) | Differential expression analysis pipeline |

## Processing Pipeline

The RO-Crate contains:
1. **CountTable.txt** - Raw counts with Ensembl gene IDs per sample
2. **R script** - Processes raw counts into differential expression results

The R script performs:
- Normalization and log2 transformation
- Differential expression analysis (DESeq2/edgeR)
- Gene annotation (Ensembl to GeneSymbol via Biomart)
- Significance calling

## Output

Running the R script on `CountTable.txt` produces the processed data file needed for `01_T3dataHandling.ipynb`, containing:
- Log2 expression values per condition (NPC, CTR, T3L, T3H)
- Gene symbols and Biomart annotations
- Significance flags for each comparison
