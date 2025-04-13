# Example Use Cases

This document provides practical examples for using the Variant Annotation and Filtering Workflow in different research scenarios.

## Example 1: Identifying Potentially Pathogenic Variants

**Research Scenario**: Looking for high-impact variants that might be associated with a rare disease.

**Filter Expression**:
```
(ANN[*].IMPACT has 'HIGH') && (QUAL > 50) && (AF < 0.01)
```

**Explanation**:
- Selects variants with HIGH impact predictions
- Ensures high quality calls (QUAL > 50)
- Focuses on rare variants (allele frequency < 1%)

**Additional Tips**:
- Consider adding `&& (ANN[*].EFFECT has 'stop_gained' || ANN[*].EFFECT has 'frameshift_variant' || ANN[*].EFFECT has 'splice_acceptor_variant' || ANN[*].EFFECT has 'splice_donor_variant')` to specifically target loss-of-function variants

## Example 2: Analyzing Cancer Somatic Mutations

**Research Scenario**: Identifying somatic mutations that might contribute to tumor development.

**Filter Expression**:
```
(ANN[*].IMPACT has 'HIGH' || ANN[*].IMPACT has 'MODERATE') && (QUAL > 30) && (ANN[*].GENE in 'TP53,BRCA1,BRCA2,KRAS,EGFR,PTEN')
```

**Explanation**:
- Includes both HIGH and MODERATE impact variants
- Basic quality filter (QUAL > 30)
- Focuses on common cancer-associated genes

**Additional Tips**:
- Adjust the gene list based on your specific cancer type of interest
- Consider adding criteria for variant allele frequency if your VCF includes these fields

## Example 3: Population Genetics Study

**Research Scenario**: Comparing functional variants across different population groups.

**Filter Expression**:
```
(ANN[*].IMPACT has 'HIGH' || ANN[*].IMPACT has 'MODERATE') && (AF >= 0.05)
```

**Explanation**:
- Includes functionally relevant variants (HIGH or MODERATE impact)
- Focuses on common variants (allele frequency ≥ 5%)

**Additional Tips**:
- If your VCF includes population-specific allele frequencies, you can filter based on these: `(EUR_AF > 0.05 && EAS_AF < 0.01)` to find variants common in European but rare in East Asian populations

## Example 4: Pharmacogenomics Analysis

**Research Scenario**: Identifying variants in drug metabolism genes.

**Filter Expression**:
```
(ANN[*].GENE has 'CYP2D6' || ANN[*].GENE has 'CYP2C19' || ANN[*].GENE has 'CYP3A4' || ANN[*].GENE has 'VKORC1' || ANN[*].GENE has 'SLCO1B1') && (ANN[*].IMPACT has 'HIGH' || ANN[*].IMPACT has 'MODERATE')
```

**Explanation**:
- Targets specific pharmacogenes involved in drug metabolism
- Focuses on functionally relevant variants

**Additional Tips**:
- Expand the gene list based on specific drug pathways of interest
- For known pharmacogenomic variants, you could filter by specific positions instead

## Example 5: Loss-of-Function Variant Analysis

**Research Scenario**: Comprehensive screen for all potential loss-of-function variants.

**Filter Expression**:
```
(ANN[*].EFFECT has 'stop_gained' || ANN[*].EFFECT has 'frameshift_variant' || ANN[*].EFFECT has 'splice_acceptor_variant' || ANN[*].EFFECT has 'splice_donor_variant' || ANN[*].EFFECT has 'start_lost' || ANN[*].EFFECT has 'stop_lost') && (QUAL > 30)
```

**Explanation**:
- Specifically targets variant effects that would disrupt protein function
- Applies basic quality filtering

**Additional Tips**:
- Consider adding `&& (ANN[*].IMPACT has 'HIGH')` as a safeguard, though most of these effects should already be classified as HIGH impact

## Using Complex Expressions

SnpSift supports complex logical operations. Here are some advanced examples:

**Combining multiple criteria with AND/OR logic**:
```
((ANN[*].GENE has 'BRCA1' || ANN[*].GENE has 'BRCA2') && ANN[*].IMPACT has 'HIGH') || (ANN[*].EFFECT has 'missense_variant' && QUAL > 100)
```

**Using NOT operator**:
```
!(ANN[*].EFFECT has 'synonymous_variant') && (QUAL > 30)
```

**Filtering based on transcript biotype**:
```
(ANN[*].BIOTYPE has 'protein_coding') && (ANN[*].IMPACT has 'MODERATE')
```

Remember to test your filters on small datasets first to ensure they work as expected before applying them to large VCF files.