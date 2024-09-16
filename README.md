# PROPARA (Prepare PROtein PARAmeter)

Quickly obtain protein parameters and pre-prepared `.gb` files from plasmid DNA sequences.

> [!IMPORTANT]
> **Disclaimer**
> This project is a draft and has not been extensively tested on a wide range of constructs. Please double-check all outputs. I take no responsibility for any time or financial losses that may arise from using this program. Feedback is welcomed.

## Overview

> This Python script is designed to analyze protein sequences derived from a plate map CSV file, typically from TWIST BIOSCIENCE. The script determines open reading frames (ORFs) for target proteins and calculates various biochemical properties, including hydrophobicity, molecular weight, isoelectric point (pI), and extinction coefficients for both reduced and oxidized cysteines. Additionally, the script detects TEV protease cleavage sites within the protein sequences and can output `.gb` files with annotated features.

> [!NOTE]
> **Compatibility**
> This script is currently tailored for use with plasmids pET28 and pET29. It may not be compatible with other plasmid constructs without modifications.

## How It Works

### 1. Input Files

- The script automatically searches for CSV files in the current directory that follow the naming pattern `platemap*.csv`.
- Each CSV file should contain columns including the protein construct sequence and other relevant metadata.
- Alternatively, a FASTA file containing the sequences of plasmids can be used as input. The path to this file must be specified in the script.

### 2. Processing Steps

- **Reindexing the Construct Sequence**: The script identifies the ribosome binding site (RBS) within the sequence and reorders the sequence to start at the RBS.
- **Translation**: The script searches for start codons within a specified region following the RBS and translates the sequence into its corresponding protein sequence.
- **Biochemical Property Calculation**: The script calculates several properties for the translated protein, including:
  - Molecular weight
  - Isoelectric point (pI)
  - Hydrophobicity score (GRAVY score)
  - Extinction coefficients for both reduced and oxidized cysteines
- **TEV Protease Cleavage**: The script identifies potential TEV protease cleavage sites within the translated protein and calculates properties for the cleaved sequence.
- **Common Features Detection**: The script searches for common genetic features (like RBS, 6xHis tag, TEV site, T7 terminator, etc.) and adds them as annotated features in the `.gb` output files.

### 3. Output Files

The script generates the following outputs, organized in directories based on the Plate ID:

- **Full Output**: (`OUTPUT_{input_file_name}.csv`) - Contains all the calculated properties for each protein sequence.
- **Condensed Output**: (`PROPA_{input_file_name}.csv`) - A simplified version containing only key properties for easy interpretation.
- **Annotated GenBank Files**: Each plasmid's annotated GenBank file is saved in a subfolder `PLASMIDS_{Plate_ID}` within a main folder named `PROPARA_{Plate_ID}`.
- **FASTA Files**: Translated protein sequences and TEVp-cleaved sequences are saved as FASTA files in a subfolder `FASTA_{Plate_ID}` within the `PROPARA_{Plate_ID}` folder.

## Directory Structure

The output files are organized as follows:

PROPARA_{Plate_ID}/
│
├── PLASMIDS_{Plate_ID}/
│   └── {Plasmid_name}.gb
│
├── FASTA_{Plate_ID}/
│   ├── output_FL.fasta
│   └── output_TEVp.fasta
│
├── OUTPUT_{input_file_name}.csv
└── PROPA_{input_file_name}.csv


## Usage

1. **Preparation**: Place your `platemap*.csv` file(s) in the same directory as this script. If using a FASTA file as input, specify the path in the script.
2. **Execution**: Run the script in a Python environment. The script will process all matching CSV files and generate output files in the specified directory structure.
3. **Review**: Examine the generated output files (`OUTPUT_*.csv` and `PROPARA_*.csv`) to analyze the calculated properties of your protein sequences. Annotated `.gb` files and translated FASTA files can be found in the appropriate subfolders.

## Customization

The script is highly customizable, allowing users to include or exclude specific analyses like SoluProt score, hydrophobicity, cellular localization, and disulfide bond prediction. Adjust these settings at the beginning of the script to suit your specific needs.

## Compatibility

Currently, this script is optimized for plasmids pET28 and pET29. For other plasmid constructs, modifications to the script may be required to ensure compatibility.
