# Detailed Usage Instructions

This document provides comprehensive instructions for using the Galaxy Variant Annotation and Filtering Workflow.

## Importing the Workflow

1. Download the workflow file (`variant_annotation_filtering.ga`) from the `workflows` directory of this repository
2. Log in to your Galaxy instance
3. Go to the Workflow menu at the top of the page
4. Click on "Import"
5. You can either:
   - Upload the downloaded workflow file by clicking "Choose File" and then "Import"
   - Or copy and paste the workflow JSON content into the "Paste the workflow" box
6. The workflow should now appear in your list of workflows

## Preparing Your Input Data

The workflow requires a VCF file as input. The VCF file should:

- Be in standard VCF format (v4.0 or later)
- Contain variants aligned to a reference genome that has a corresponding SnpEff database
- Ideally include quality scores (QUAL field) for better filtering

If your VCF file is large, consider subsetting it first for faster processing:

```bash
# Example using bcftools to extract chromosome 22 only
bcftools view input.vcf.gz chr22 > chr22.vcf
```

## Running the Workflow

1. Go to the Workflow menu
2. Find "Variant Annotation and Filtering" in your list of workflows
3. Click on the dropdown menu next to it and select "Run"
4. In the workflow run form:
   - For the "Input VCF File" step, select your VCF file from your history or upload a new one
   - For the "SnpEff eff" step, select the appropriate genome database that matches your reference genome
   - For the "SnpSift filter" step, review and modify the filter expression if needed
5. Click "Run workflow" at the bottom of the page

## Key Parameters to Consider

### SnpEff Annotation

- **Genome database**: Must match the reference genome used for your variants
- **Output format**: Keep as "VCF" to maintain all original variant information
- **Annotation options**: Consider enabling "Annotate loss of function variations" for more detailed impact information

### SnpSift Filter

- **Filter expression**: The default expression is:
  ```
  (ANN[*].IMPACT has 'HIGH' || ANN[*].IMPACT has 'MODERATE') && (QUAL > 30)
  ```
- Adjust this expression based on your research needs (see EXAMPLES.md for more options)

## Understanding the Outputs

The workflow produces two main outputs:

1. **Annotated and Filtered VCF file**: Contains variants that passed your filtering criteria with functional annotations added
   - Each variant includes ANN fields in the INFO column with detailed functional predictions
   - Only variants matching your filter expression are retained

2. **SnpEff HTML Summary Report**: Provides statistical overview of the annotations
   - Summary of variant types (SNPs, insertions, deletions)
   - Distribution of variants by impact (HIGH, MODERATE, LOW, MODIFIER)
   - Distribution of variants by functional class (missense, nonsense, silent)
   - List of affected genes and number of variants per gene

## Next Steps After Running

Once you have your annotated and filtered variants, you might want to:

1. **Convert to a more readable format** using Galaxy's "VCF to tabular" tool
2. **Extract specific fields** for downstream analysis
3. **Visualize the variants** using Galaxy's visualization tools
4. **Export the results** for use in other applications

## Parameter Recommendations

| Research Goal | Recommended Filter Expression |
|---------------|-------------------------------|
| Identify disease-causing variants | `(ANN[*].IMPACT has 'HIGH') && (QUAL > 50)` |
| Comprehensive functional analysis | `(ANN[*].IMPACT has 'HIGH' || ANN[*].IMPACT has 'MODERATE')` |
| Find loss-of-function variants | `(ANN[*].EFFECT has 'stop_gained' || ANN[*].EFFECT has 'frameshift_variant')` |
| Target specific pathway genes | `(ANN[*].GENE has 'GENE1' || ANN[*].GENE has 'GENE2' || ANN[*].GENE has 'GENE3')` |