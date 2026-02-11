# Data Format Specification

This document specifies the expected format for `T3_expression_data.csv`, the input file for notebook `01_T3dataHandling.ipynb`.

## File Location

- **Path**: `data/raw/T3_expression_data.csv`
- **Source**: Converted from `Processed data_RNAseq_T3.xlsx` (available in BioStudies S-VHPS20)
- **Git status**: Excluded from version control (see `.gitignore`)

## File Properties

| Property | Value |
|----------|-------|
| Format | CSV (Comma-Separated Values) |
| Encoding | UTF-8 |
| Separator | Comma (`,`) |
| Line endings | Unix (LF) or Windows (CRLF) |
| Header row | Yes (row 1) |
| Data rows | 37,424 (genes) |
| Total rows | 37,425 (header + data) |
| Columns | 28 |
| File size | ~10-12 MB |

## Column Specifications

### 1. Gene Identifiers

| Column Name | Data Type | Description | Example | Missing Values |
|-------------|-----------|-------------|---------|----------------|
| `Expression(log2)` | string | Ensembl gene ID with version | `ENSG00000223972.5` | No |
| `GeneSymbol` | string | HGNC gene symbol | `DDX11L1` | Rare |
| `Gene ID (Biomart)` | string | Ensembl gene ID from Biomart | `ENSG00000223972` | Rare |
| `Gene symbol (Biomart)` | string | Alternative gene symbol | `DDX11L1` | Some |

### 2. Expression Values (Normalized Log2)

All expression values are **variance-stabilized log2 transformed** values from DESeq2.

| Column Name | Data Type | Description | Range | Missing Values |
|-------------|-----------|-------------|-------|----------------|
| `NPC` | float | Expression in NPC condition | ~4-15 | No |
| `CTR` | float | Expression in control (CTR) condition | ~4-15 | No |
| `T3L` | float | Expression in low T3 (T3L) condition | ~4-15 | No |
| `T3H` | float | Expression in high T3 (T3H) condition | ~4-15 | No |

### 3. Fold Change Values

Log2 fold changes between conditions.

| Column Name | Data Type | Description | Range |
|-------------|-----------|-------------|-------|
| `CTR - NPC` | float | log2FC (CTR vs NPC) | -5 to 5 typical |
| `T3L-CTR` | float | log2FC (T3L vs CTR) | -5 to 5 typical |
| `T3H-CTR` | float | log2FC (T3H vs CTR) | -5 to 5 typical |

### 4. Significance Flags

Binary flags (0 or 1) indicating statistical significance.

| Column Name | Data Type | Description | Values |
|-------------|-----------|-------------|--------|
| `CTR-T3L` | integer | Significant in CTR vs T3L comparison | 0 or 1 |
| `CTR-T3H` | integer | Significant in CTR vs T3H comparison | 0 or 1 |
| `T3L-T3H` | integer | Significant in T3L vs T3H comparison | 0 or 1 |
| `CTR-T3L-T3H FC 0.5` | integer | Significant in 3-group LRT (\|log2FC\| ≥ 0.5) | 0 or 1 |
| `CTR-T3L-T3H FC 0` | integer | Significant in 3-group LRT (any FC) | 0 or 1 |
| `NPC-CTR FC 0.5` | integer | Significant in NPC-CTR (\|log2FC\| ≥ 0.5) | 0 or 1 |
| `NPC-CTR FC 0` | integer | Significant in NPC-CTR (any FC) | 0 or 1 |

**Significance criteria**: p-value < 0.001 AND adjusted p-value (FDR) < 0.05

### 5. Genomic Annotations

| Column Name | Data Type | Description | Example |
|-------------|-----------|-------------|---------|
| `Chromosome` | string | Chromosome number or name | `1`, `X`, `MT` |
| `Start` | integer | Genomic start position (bp) | `11869` |
| `Stop` | integer | Genomic end position (bp) | `14409` |
| `Strand` | integer | Strand orientation | `1` (plus) or `-1` (minus) |
| `GeneType` | string | Gene biotype | `protein_coding`, `lncRNA`, etc. |

### 6. Transcript Information

| Column Name | Data Type | Description |
|-------------|-----------|-------------|
| `transcriptIDs` | string | Semicolon-separated Ensembl transcript IDs |
| `transcriptStart` | string | Semicolon-separated transcript start positions |
| `transcriptStop` | string | Semicolon-separated transcript end positions |

### 7. Additional Columns

| Column Name | Data Type | Description |
|-------------|-----------|-------------|
| `Ratios` | string | Internal processing metadata |
| `Venn (DEGs)` | string | Internal Venn diagram grouping |

## Example Rows

### Header Row
```
Expression(log2),NPC,CTR,T3L,T3H,Ratios,CTR - NPC,T3L-CTR,T3H-CTR,Chromosome,Start,Stop,Strand,GeneSymbol,GeneType,transcriptIDs,transcriptStart,transcriptStop,Gene ID (Biomart),Gene symbol (Biomart),Venn (DEGs),CTR-T3L-T3H FC 0.5,CTR-T3L-T3H FC 0,NPC-CTR FC 0.5,NPC-CTR FC 0,CTR-T3L,CTR-T3H,T3L-T3H
```

### Sample Data Rows
```
ENSG00000223972.5,5.242123,5.209101,5.198456,5.167234,,,-0.010645,-0.031222,-0.041867,1,11869,14409,1,DDX11L1,processed_transcript,ENST00000456328.2;ENST00000450305.2,11869;12010,14409;13670,ENSG00000223972,DDX11L1,,0,0,0,0,0,0,0
ENSG00000227232.5,5.532526,5.496411,5.489234,5.445123,,,0.036115,-0.007177,-0.044111,1,14404,29570,-1,WASH7P,unprocessed_pseudogene,ENST00000488147.1,14404,29570,ENSG00000227232,WASH7P,,0,0,0,0,0,0,0
```

### Gene with Significance Flag
```
ENSG00000141510.16,8.234567,9.123456,7.987654,10.234567,,,0.888889,-1.135802,1.111111,7,55019032,55211628,-1,TP53,protein_coding,ENST00000269305.8,55019032,55211628,ENSG00000141510,TP53,DEG,1,1,0,0,1,1,1
```

## Data Validation Checks

### Basic Checks

```python
import pandas as pd

df = pd.read_csv('data/raw/T3_expression_data.csv')

# Check dimensions
assert df.shape == (37424, 28), f"Expected (37424, 28), got {df.shape}"

# Check required columns exist
required_cols = ['GeneSymbol', 'Gene ID (Biomart)', 'NPC', 'CTR', 'T3L', 'T3H',
                 'CTR-T3L', 'CTR-T3H', 'T3L-T3H']
assert all(col in df.columns for col in required_cols), "Missing required columns"

# Check expression values are numeric
assert df['CTR'].dtype in ['float64', 'float32'], "CTR should be float"
assert df['T3L'].dtype in ['float64', 'float32'], "T3L should be float"
assert df['T3H'].dtype in ['float64', 'float32'], "T3H should be float"

# Check significance flags are binary
assert set(df['CTR-T3L'].dropna().unique()).issubset({0, 1}), "CTR-T3L should be 0 or 1"
assert set(df['CTR-T3H'].dropna().unique()).issubset({0, 1}), "CTR-T3H should be 0 or 1"

print("✓ All validation checks passed")
```

### Advanced Checks

```python
# Check for missing gene symbols
missing_symbols = df['GeneSymbol'].isna().sum()
print(f"Genes without symbols: {missing_symbols} ({missing_symbols/len(df)*100:.1f}%)")

# Check expression value ranges
print(f"Expression ranges:")
print(f"  NPC: {df['NPC'].min():.2f} - {df['NPC'].max():.2f}")
print(f"  CTR: {df['CTR'].min():.2f} - {df['CTR'].max():.2f}")
print(f"  T3L: {df['T3L'].min():.2f} - {df['T3L'].max():.2f}")
print(f"  T3H: {df['T3H'].min():.2f} - {df['T3H'].max():.2f}")

# Count significant genes
sig_hypo = (df['CTR-T3L'] == 1).sum()
sig_hyper = (df['CTR-T3H'] == 1).sum()
sig_both = ((df['CTR-T3L'] == 1) & (df['CTR-T3H'] == 1)).sum()

print(f"\nSignificant genes:")
print(f"  Hypothyroid (CTR-T3L): {sig_hypo}")
print(f"  Hyperthyroid (CTR-T3H): {sig_hyper}")
print(f"  Both conditions: {sig_both}")
```

## Expected Output Statistics

When the file is loaded correctly, you should see approximately:

- **Total genes**: 37,424
- **Genes with symbols**: ~37,000 (>98%)
- **Significant in CTR-T3L**: ~2,000-3,000 genes
- **Significant in CTR-T3H**: ~2,000-3,000 genes
- **Significant in both**: ~30-50 genes (the overlapping DEGs)

## Common Issues

### Issue: Extra index column

**Symptom**: First column is unnamed numbers (0, 1, 2, ...)
**Cause**: Excel was converted with `index=True`
**Fix**: Re-convert with `index=False`

```python
df = pd.read_excel('Processed data_RNAseq_T3.xlsx')
df.to_csv('T3_expression_data.csv', index=False)  # Note: index=False
```

### Issue: Wrong separator

**Symptom**: All data in one column, or columns split incorrectly
**Cause**: File uses tab or semicolon instead of comma
**Fix**: Check separator when reading

```python
# Try different separators
df = pd.read_csv('T3_expression_data.csv', sep=',')  # Standard
# or
df = pd.read_csv('T3_expression_data.csv', sep='\t')  # Tab-separated
```

### Issue: Encoding errors

**Symptom**: Strange characters in gene names
**Cause**: Wrong encoding
**Fix**: Specify UTF-8

```python
df = pd.read_csv('T3_expression_data.csv', encoding='utf-8')
```

### Issue: Quote characters in data

**Symptom**: Extra quote marks around values
**Cause**: Excel conversion added quotes
**Fix**: Remove during conversion

```python
df.to_csv('T3_expression_data.csv', index=False, quoting=csv.QUOTE_MINIMAL)
```

## Notebook Usage

The first notebook (`01_T3dataHandling.ipynb`) loads this file as follows:

```python
import pandas as pd

# Load dataset
df = pd.read_csv('T3_expression_data.csv', sep=',')

# Expected columns used:
# - GeneSymbol: For gene identification
# - Gene ID (Biomart): For annotation
# - CTR, T3L, T3H: Expression values
# - CTR-T3L, T3L-T3H: Significance flags

# Calculate log2 fold changes
df['log2FC_hypothyroid'] = df['CTR'] - df['T3L']
df['log2FC_hyperthyroid'] = df['T3H'] - df['T3L']
```

## Version History

- **2025-01**: Initial processed data from DESeq2 analysis (JP & ND)
- **2026-01**: Added to BioStudies S-VHPS20 as RO-Crate
- **Current**: Documented format specification

## References

- **Processing script**: `Rscript analyses SI G&G.r` (in BioStudies)
- **Source data**: CountTable.txt (in BioStudies)
- **Method**: DESeq2 VST normalization + differential expression
- **Significance thresholds**: p < 0.001, padj < 0.05, |log2FC| ≥ 0.5 (varies by comparison)
