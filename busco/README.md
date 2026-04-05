# BUSCO — Assembly Completeness Assessment

BUSCO checks how complete a genome assembly is by looking for
genes that are expected to be present in all species of a given
group. Think of it like a checklist — if most expected genes are
present and not duplicated, the assembly is likely complete.

## Files

| File | Description |
|------|-------------|
| `busco_hap1_image.png` | Visual bar chart summary of BUSCO results |
| `busco_hap1_summary.txt` | Text summary with exact gene counts |

## Parameters Used

| Parameter | Value |
|-----------|-------|
| Lineage | Ascomycota |
| Mode | Genome assembly DNA |
| Prediction tool | Metaeuk |

## How to Read BUSCO Results

- **Complete Single-copy** ✅ — gene found exactly once, good
- **Complete Duplicated** ⚠️ — gene found more than once, may need purging
- **Fragmented** ⚠️ — gene only partially found
- **Missing** ❌ — gene not found, possible gaps in assembly

## Results

| Category | Hap1 |
|----------|------|
| Complete single-copy | ~95.8% |
| Duplicated | Low |
| Missing | ~29 genes |
