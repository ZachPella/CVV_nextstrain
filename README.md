# Cache Valley Virus (CVV) Nextstrain Build

This repository contains a Nextstrain phylogenetic analysis pipeline for Cache Valley virus (CVV) L segment sequences. The pipeline processes genomic sequences through filtering, alignment, tree building, and visualization steps to generate an interactive phylogenetic tree visualization.

<img width="1299" height="910" alt="Screenshot 2025-12-15 105508" src="https://github.com/user-attachments/assets/4e29443c-4df1-4886-a28e-33d1d6ac20b1" />
<img width="1298" height="833" alt="Screenshot 2025-12-15 105520" src="https://github.com/user-attachments/assets/cdd71178-99aa-4220-a9ff-ed9b49f21ab6" />

## Overview

Cache Valley virus is an orthobunyavirus in the family Peribunyaviridae. This build focuses on the L (large) segment, which encodes the viral RNA-dependent RNA polymerase. The analysis includes sequences from multiple geographic locations spanning from 1956 to 2025.

## Repository Structure
```
CVV_nextstrain/
├── data/
│   ├── sequences.fasta       # Input genomic sequences
│   └── metadata.tsv          # Sample metadata (strain, date, location)
├── config/
│   ├── reference.fasta       # Reference sequence for alignment
│   ├── cache_valley_L_segment.gb  # GenBank annotation for translation
│   ├── colors.tsv           # Color scheme for geographic regions
│   ├── lat_longs.tsv        # Coordinates for map visualization
│   └── auspice_config.json  # Nextstrain visualization settings
├── results/                 # Intermediate analysis files (generated)
├── auspice/                # Final visualization output (generated)
├── Snakefile               # Workflow definition
└── README.md
```

## Requirements

- [Nextstrain](https://docs.nextstrain.org/en/latest/install.html) components:
  - Augur (bioinformatics toolkit)
  - Auspice (visualization)
- Snakemake workflow manager
- Python 3.x with required packages

## Installation

1. Install Nextstrain following the [official installation guide](https://docs.nextstrain.org/en/latest/install.html)

2. Clone this repository:
```bash
git clone https://github.com/ZachPella/CVV_nextstrain.git
cd CVV_nextstrain
```

## Usage

### Running the complete pipeline
```bash
snakemake --cores 8
```

### Running specific steps
```bash
# Filter sequences
snakemake results/filtered.fasta --cores 1

# Build phylogenetic tree
snakemake results/tree.nwk --cores 8

# Generate final visualization
snakemake auspice/cache_valley.json --cores 1
```

### Cleaning outputs
```bash
snakemake clean
```

## Pipeline Steps

1. **Index Sequences**: Create composition index for filtering
2. **Filter**: Select representative sequences (10 per country/year)
3. **Align**: Align sequences to reference using MAFFT
4. **Build Tree**: Construct phylogenetic tree using IQ-TREE
5. **Refine**: Time-calibrate tree and estimate divergence dates
6. **Ancestral Reconstruction**: Infer ancestral sequences
7. **Translate**: Generate amino acid mutations
8. **Traits**: Reconstruct ancestral geographic locations
9. **Export**: Generate Auspice-compatible JSON for visualization

## Key Parameters

- **Filtering**: 10 sequences per country/year, minimum date 1950
- **Tree building**: Uses 14 threads (adjustable via `--cores`)
- **Time tree**: Uses optimal coalescent timescale with marginal date inference
- **Root**: NC_043618.1 (1956 Utah isolate, oldest sequence)

## Visualization

After running the pipeline, visualize results using Nextstrain:
```bash
nextstrain view auspice/
```

Or view at: `https://auspice.us` (drag and drop the JSON file)

## Input Data Format

### sequences.fasta
FASTA format with sequence headers matching strain names in metadata

### metadata.tsv
Tab-separated values with columns:
- `strain`: Unique identifier (must match FASTA headers)
- `date`: Collection date (YYYY-MM-DD format, XX for unknown)
- `country`: Country of origin
- `state`: State/province/region

## Customization

### Modify filtering parameters
Edit `Snakefile` lines 48-50:
```python
params:
    group_by = "country year",
    sequences_per_group = 10,
    min_date = 1950
```

### Update geographic colors
Edit `config/colors.tsv` to customize region colors

### Adjust visualization settings
Modify `config/auspice_config.json` for display preferences

## Output Files

- `auspice/cache_valley.json`: Interactive visualization file
- `results/tree.nwk`: Time-calibrated phylogenetic tree (Newick format)
- `results/aligned.fasta`: Multiple sequence alignment
- `results/aa_muts.json`: Amino acid mutations
- `results/nt_muts.json`: Nucleotide mutations
- `results/traits.json`: Geographic trait reconstructions

## Sample Dataset

The repository includes 12 CVV L segment sequences:
- **Geographic range**: USA (Nebraska, Illinois, Virginia, North Dakota, Missouri, Utah, New York), Canada (Saskatchewan), Mexico, United Kingdom
- **Temporal range**: 1956-2025
- **Reference**: NC_043618.1 (Cache Valley virus strain 6V633, 1956)

## Troubleshooting

### Common issues:

1. **Alignment fails**: Ensure reference sequence is complete and properly formatted
2. **Tree building stalls**: Reduce thread count or check sequence quality
3. **Empty filtered output**: Adjust filtering parameters to be less stringent
4. **Date parsing errors**: Verify metadata dates follow YYYY-MM-DD format

## Citation

If you use this pipeline, please cite:
- Nextstrain: Hadfield et al., Bioinformatics (2018)
- Augur: https://docs.nextstrain.org/projects/augur/
- IQ-TREE: Nguyen et al., Molecular Biology and Evolution (2015)

## References

- Cache Valley virus reference: Campbell et al., Journal of General Virology (2006)
- Nextstrain documentation: https://docs.nextstrain.org/

## Author

Zachary Pella  
APHL-CDC Bioinformatics Fellow  
University of Nebraska Medical Center - Nebraska Public Health Lab

## License

MIT License

## Contributing

Issues and pull requests welcome! Please follow standard GitHub workflow.

## Acknowledgments

This pipeline is adapted from the [Nextstrain Zika tutorial](https://nextstrain.org/docs/getting-started/zika-tutorial) and customized for Cache Valley virus analysis.
