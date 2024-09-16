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


---

# Tips for Interpreting Protein Sequence Analysis Results

## Hydrophobicity Score

- **What It Is**: The hydrophobicity score, or GRAVY score, indicates the overall hydrophobic or hydrophilic nature of the protein. It is calculated as the average of the hydrophobicity values for each amino acid in the sequence.
  
- **How to Interpret**:
  - **Positive Scores**: Suggest the protein is hydrophobic, which may indicate membrane association or poor solubility in aqueous environments.
  - **Negative Scores**: Suggest the protein is hydrophilic and likely to be soluble in water, which is typically desirable for soluble protein expression.

- **Considerations**:
  - **Membrane Proteins**: Often have positive GRAVY scores due to their hydrophobic regions interacting with the lipid bilayer.
  - **Soluble Proteins**: Negative GRAVY scores are typically more desirable for proteins intended to be soluble in aqueous environments.

## Extinction Coefficients

- **What They Are**: Extinction coefficients indicate how much light a protein absorbs at a specific wavelength (usually 280 nm), which is useful for estimating protein concentration using UV absorbance.

- **Reduced vs. Oxidized**:
  - **Reduced Extinction Coefficient**: Assumes all cysteine residues are in their reduced thiol form (`-SH`), meaning no disulfide bonds are present.
  - **Oxidized Extinction Coefficient**: Assumes cysteine residues form disulfide bonds (`-S-S-`), which can slightly alter the absorption characteristics.

- **How to Choose**:
  - **Reduced Form**: Use this value if you expect your protein to have free cysteines (no disulfide bonds).
  - **Oxidized Form**: Use this if you know your protein forms disulfide bonds, which is common in proteins expressed in the oxidative environment of the endoplasmic reticulum or outside the cell.

## Molecular Weight

- **What It Is**: The molecular weight of the protein is calculated based on the amino acid sequence and is expressed in Daltons (Da).

- **How to Interpret**:
  - Useful for verifying the size of the expressed protein by comparing with SDS-PAGE or mass spectrometry data.
  - Discrepancies between the calculated and observed molecular weight may indicate post-translational modifications or other processing events.

## Isoelectric Point (pI)

- **What It Is**: The isoelectric point is the pH at which the protein carries no net electrical charge.

- **How to Use**:
  - The pI is important for designing purification protocols, particularly in ion exchange chromatography where the protein’s charge affects its interaction with the resin.
  - Proteins are least soluble at their pI, so avoiding this pH during purification can prevent precipitation.

## TEV Protease Cleavage

- **What It Is**: TEV protease is often used to remove fusion tags from proteins. The script identifies potential TEV cleavage sites and provides the properties of the cleaved protein.

- **How to Use**:
  - Verify that TEV cleavage produces the expected protein fragment.
  - Use the calculated properties of the cleaved protein to plan subsequent purification or characterization steps.

By considering these factors, you can better interpret the results of the protein sequence analysis and make informed decisions about protein expression, purification, and functional studies.

## Disulfide Bond Prediction

### How It Works

In this script, the prediction of disulfide bonds is based on a simple heuristic:

- **Cysteine Residue Count**: The script counts the number of cysteine (`C`) residues in the protein sequence. Cysteine is the amino acid that can form disulfide bonds when oxidized.
- **Basic Prediction Rule**: If the protein sequence contains two or more cysteine residues, the script assumes that disulfide bonds could potentially form. This is based on the idea that disulfide bonds require at least two cysteine residues to form a covalent bond (`Cys-S-S-Cys`).

### Limitations

- **Simplistic Approach**: This method is very basic and does not account for the actual spatial arrangement of cysteine residues within the protein. It only checks if there are enough cysteines to form at least one disulfide bond.
- **No Structural Information**: The prediction does not consider the three-dimensional structure of the protein, which is critical for determining whether the cysteines are close enough to form disulfide bonds.

### More Advanced Methods

For more accurate disulfide bond predictions, especially in complex proteins or those with known structures, consider using more advanced tools:

- **Structural Analysis**: Use structural data (from X-ray crystallography or NMR) to assess the proximity of cysteine residues in three-dimensional space.
- **Machine Learning Tools**: Tools like **DiANNA** or **DISULFIND** use sequence-based features and machine learning models to predict disulfide bonds more accurately, considering the likelihood and connectivity of potential bonds.
