# Merqury — k-mer Based Quality Assessment

Merqury evaluates assembly quality by comparing k-mers found
in the raw reads against those in the assembly. It is completely
reference-free — no known reference genome is needed.

## Files

| File | Description |
|------|-------------|
| `merqury_spectra_cn.png` | k-mer copy number distribution plot |
| `merqury_spectra_asm.png` | k-mers colored by which assembly contains them |
| `merqury_qv_stats.txt` | Quality Value score and completeness percentage |

## Understanding the Plots

### Spectra-CN Plot
Shows how many times each k-mer appears in the assembly.
Most k-mers in a good assembly appear 1 or 2 times.

### Spectra-ASM Plot
Shows k-mers colored by haplotype. A good Hi-C phased assembly shows:
- Large shared green peak at ~50x coverage (homozygous regions)
- Two smaller peaks at ~25x (haplotype-specific regions)

## Quality Value

QV measures base-level accuracy. QV40 = 1 error per 10,000 bases.
Higher QV = more accurate assembly.
