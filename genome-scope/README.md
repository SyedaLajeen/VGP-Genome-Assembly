# GenomeScope2 — Genome Profiling

This folder contains the output of GenomeScope2, which was used
to estimate key properties of the genome before assembly.

## Files

| File | Description |
|------|-------------|
| `genomescope_linear_plot.png` | k-mer frequency plot on a linear scale |
| `genomescope_log_plot.png` | Same plot on a logarithmic scale |
| `genomescope_summary.txt` | Text summary of all estimated genome properties |

## What these plots show

The plots display the distribution of k-mer frequencies across
the sequencing reads. For a diploid genome like S. cerevisiae,
you expect to see two peaks — one at roughly half the coverage
(heterozygous regions) and one at full coverage (homozygous regions).

## Estimated Genome Properties

| Property | Estimated Value |
|----------|----------------|
| Haploid genome size | ~11.7 Mb |
| Heterozygosity | ~0.576% |
| Sequencing coverage | ~50x |
| Model fit | >93% |
