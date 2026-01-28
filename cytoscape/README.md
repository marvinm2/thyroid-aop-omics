# Cytoscape Network Analysis

This directory contains Cytoscape session files for the MolAOP network visualization.

## Files

- `MolAOP.cys` - Cytoscape session with MolAOP (Molecular AOP) network

## Requirements

- Cytoscape 3.9+
- py4cytoscape (for programmatic access from notebooks)

## Usage

### GUI Usage

1. Open Cytoscape
2. File > Open > Select `MolAOP.cys`
3. Explore the network visualization

### Programmatic Access

The `04_MolAOPcalc.ipynb` notebook uses py4cytoscape to interact with Cytoscape:

```python
import py4cytoscape as p4c

# Ensure Cytoscape is running
p4c.cytoscape_ping()

# Load the session
p4c.open_session('cytoscape/MolAOP.cys')
```

**Note**: Cytoscape must be running before executing the notebook cells that use py4cytoscape.

## Network Description

The MolAOP network visualizes Key Events (KEs) from the thyroid hormone AOP and their associated genes from the differential expression analysis.
