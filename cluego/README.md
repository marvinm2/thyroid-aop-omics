# ClueGO Pathway Enrichment Analysis

ClueGO analysis was performed entirely in the Cytoscape GUI. This document provides the settings used for reproducibility.

## Cytoscape Session

The complete ClueGO analysis is saved in `cluego_analysis.cys`. To explore:

1. Open Cytoscape (v3.9+)
2. Install ClueGO plugin if not already installed
3. Open `cluego_analysis.cys`
4. Navigate to the ClueGO results panel

## Software Versions

- **Cytoscape**: 3.9+
- **ClueGO**: v2.5.10
- **Ontology**: GO_BiologicalProcess-EBI-UniProt-GOA (16.03.2025)

## Two-Cluster Analysis Design

The analysis uses a two-cluster comparison:

| Cluster | Description | Genes |
|---------|-------------|-------|
| Cluster #1 | Upregulated genes in hypothyroid condition | 1,497 uploaded / 1,427 recognized |
| Cluster #2 | Downregulated genes in hypothyroid condition | 1,497 uploaded / 1,459 recognized |

## ClueGO Settings

Settings extracted from `hypothyroid/ClueGOResults1_2cluster ClueGOLog.txt`:

### Statistical Parameters
| Parameter | Value |
|-----------|-------|
| Statistical Test | Two-sided hypergeometric (Enrichment/Depletion) |
| P-value Cutoff | 0.05 |
| Correction Method | Bonferroni step down |

### GO Term Selection
| Parameter | Value |
|-----------|-------|
| Organism | Homo Sapiens [9606] |
| Identifier Type | SymbolID |
| Evidence Codes | All |
| Min GO Level | 6 |
| Max GO Level | 15 |
| Min Percentage | 4.0% |
| Min Genes per Term | 3 |

### Grouping Parameters
| Parameter | Value |
|-----------|-------|
| GO Fusion | Enabled |
| GO Group | Enabled |
| Kappa Score Threshold | 0.4 |
| Group by Kappa Statistics | Yes |
| Initial Group Size | 1 |
| Sharing Group Percentage | 50.0% |

### Cluster Specificity
| Parameter | Value |
|-----------|-------|
| Combine Clusters With | OR |
| Cluster Specificity Threshold | 60.0% |
| Overview Term Selection | Smallest P-Value |

## Results Summary

From the ClueGO log:

- **Reference set**: 17,991 genes in GO_BiologicalProcess
- **Annotated genes**: 2,577 unique genes (89.29%)
- **Terms after significance selection**: 108
- **Final KappaScore groups**: 25
- **Cluster #1 specific terms**: 28
- **Cluster #2 specific terms**: 25
- **Non-specific terms**: 55

## Output Files

The `hypothyroid/` directory contains:

| File | Description |
|------|-------------|
| `ClueGOLog.txt` | Complete analysis log with all settings |
| `Functional_Groups_With_Genes.txt` | GO terms grouped by function |
| `BinaryGeneTermMatrix.txt` | Gene-term association matrix |
| `NodeAttributeTable.txt` | Network node attributes |
| `EdgeAttributeTable.txt` | Network edge attributes |
| `*Cluster #1*.png/svg` | Cluster 1-specific visualizations |
| `*Cluster #2*.png/svg` | Cluster 2-specific visualizations |
| `Results for Analysis1-*.png/svg` | Combined network views |
| `piechart-cluster*.png` | Pie chart summaries |

## Main Manuscript Figure

The main ClueGO figure in the manuscript is:
- `../figures/main/ClueGO_analysis.png` (and `.svg`)

This figure combines the grouped network visualization with per-cluster pie charts and bar plots.
