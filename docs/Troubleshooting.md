# Troubleshooting Guide

This document addresses common issues you might encounter when running the Variant Annotation and Filtering workflow and provides solutions.

## Common Issues and Solutions

### SnpEff Database Issues

**Problem**: Error message about missing or incorrect SnpEff database.

**Possible Causes**:
- Selected database doesn't match the reference genome used for variant calling
- The database isn't installed on your Galaxy instance
- Database name is misspelled

**Solutions**:
1. Check which reference genome was used for alignment and variant calling
2. Verify the exact database name in Galaxy's SnpEff tool form (drop-down list)
3. If using a local Galaxy, you may need to install the appropriate database:
   - Go to Admin → Local Data → SnpEff Databases
   - Find and install the matching database

**Prevention**:
- Always document which reference genome you use throughout your analysis
- For human data, common databases are GRCh38.p13 or GRCh37.p13 (hg19)

### Empty or Very Small Results After Filtering

**Problem**: The filtered VCF file contains very few or no variants.

**Possible Causes**:
- Filter criteria too stringent
- Incorrect field names in the filter expression
- SnpEff version differences (ANN vs. EFF field)

**Solutions**:
1. Check the annotated VCF (before filtering) to verify annotations were added correctly
2. Examine the INFO field in the header to confirm the correct field names
3. Relax your filter criteria (e.g., include MODERATE impacts, lower QUAL threshold)
4. For older SnpEff versions, you might need to use `EFF[*].IMPACT` instead of `ANN[*].IMPACT`

**Prevention**:
- Start with simple filters and progressively add criteria
- Test on small subsets first

### Syntax Errors in Filter Expression

**Problem**: SnpSift filter reports syntax error or doesn't process the expression correctly.

**Possible Causes**:
- Incorrect logical operators (use `&&` for AND, `||` for OR)
- Missing or mismatched parentheses
- Incorrect field names or values

**Solutions**:
1. Verify basic syntax: proper parentheses, correct operators
2. Check exact field names from the VCF header
3. For string comparisons, use single quotes around values
4. Start with simple expressions and build up complexity

**Example of correct syntax**:
```
(ANN[*].IMPACT has 'HIGH') && (QUAL > 30)
```

**Prevention**:
- Test filter expressions on small datasets first
- Reference the SnpSift documentation for proper syntax

### Performance Issues with Large Files

**Problem**: Workflow runs very slowly or fails with memory errors.

**Possible Causes**:
- Input VCF file too large
- Insufficient resources allocated
- Complex filter expressions

**Solutions**:
1. Split your VCF file by chromosome before processing
2. Use a Galaxy instance with more resources
3. Simplify filter expressions if possible
4. For local Galaxy installations, increase memory allocation

**Prevention**:
- Subset large VCF files for initial testing
- Use compressed VCF files (VCF.gz) when possible

### Incorrect Variant Effects

**Problem**: Variants are not annotated with the expected effects.

**Possible Causes**:
- Using incorrect SnpEff database version
- Differences in gene annotations between database versions
- Variants not matching transcripts in the database

**Solutions**:
1. Verify the database version matches your reference genome
2. Check if variants fall in regions with known genes/transcripts
3. Consider using a different/updated SnpEff database
4. For custom genomes, ensure gene annotations are properly loaded

**Prevention**:
- Use the most up-to-date SnpEff database for your reference genome

### HTML Report Not Generated

**Problem**: The SnpEff HTML summary report is missing or incomplete.

**Possible Causes**:
- SnpEff settings might have disabled report generation
- Bug in specific SnpEff version
- Galaxy workflow configuration issue

**Solutions**:
1. Check SnpEff settings to ensure HTML report generation is enabled
2. Try downloading and viewing the report locally if it exists but can't be viewed in Galaxy
3. Update to a newer SnpEff version if available

**Prevention**:
- Always include the HTML report output in your workflow configuration

## Getting Additional Help

If you encounter issues not covered in this guide:

1. Check the [SnpEff manual](http://snpeff.sourceforge.net/SnpEff_manual.html) and [SnpSift documentation](http://snpeff.sourceforge.net/SnpSift.html)
2. Search the [Galaxy Help Forum](https://help.galaxyproject.org/)
3. File an issue in this GitHub repository with:
   - A description of the problem
   - Steps to reproduce
   - Error messages or screenshots
   - Details about your input data and Galaxy instance