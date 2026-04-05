# VGP Genome Assembly — *Saccharomyces cerevisiae* S288C

![Platform](https://img.shields.io/badge/Platform-Galaxy-blue)
![Status](https://img.shields.io/badge/Status-Complete-brightgreen)
![Species](https://img.shields.io/badge/Species-S.cerevisiae-orange)
![Method](https://img.shields.io/badge/Method-Hi--C%20Phased-purple)

A de novo diploid genome assembly of *Saccharomyces cerevisiae* S288C
performed using the Vertebrate Genome Project (VGP) pipeline on Galaxy,
combining PacBio HiFi long reads, Illumina Hi-C data, and Bionano
optical maps to produce chromosome-level haplotype assemblies.

---

## What is Genome Assembly?

Genome assembly is the process of reconstructing the complete DNA
sequence of an organism from millions of short sequencing reads.
Think of it like solving a jigsaw puzzle where each read is a small
piece and the assembler figures out how all the pieces fit together
to rebuild the full picture.

Modern pipelines like VGP use multiple types of sequencing data
together to produce assemblies that are more complete and accurate
than any single technology alone.

---

## Objective

- Assemble the *S. cerevisiae* S288C genome from synthetic PacBio HiFi reads using the VGP Galaxy pipeline
- Produce two fully phased haplotype assemblies — Hap1 and Hap2
- Evaluate assembly quality using BUSCO, Merqury, and gfastats
- Scaffold contigs to chromosome level using Bionano optical maps and Hi-C data

---

## Organism

| Property | Value |
|----------|-------|
| Species | *Saccharomyces cerevisiae* |
| Strain | S288C |
| Estimated genome size | ~11.7 Mb |
| Ploidy | Diploid |
| Number of chromosomes | 16 |

---

## Input Data

| Dataset | Format | Source |
|---------|--------|--------|
| PacBio HiFi reads — 3 files, 50x coverage | FASTA | [Zenodo 6098306](https://zenodo.org/record/6098306) |
| Illumina Hi-C forward reads | FASTQ.GZ | [Zenodo 5550653](https://zenodo.org/record/5550653) |
| Illumina Hi-C reverse reads | FASTQ.GZ | [Zenodo 5550653](https://zenodo.org/record/5550653) |
| Bionano optical map | CMAP | [Zenodo 5887339](https://zenodo.org/record/5887339) |

---

## Tools Used

| Tool | Version | Purpose |
|------|---------|---------|
| Cutadapt | 4.4+galaxy0 | Remove adapter sequences from HiFi reads |
| Meryl | 1.3+galaxy6 | Count k-mers from reads |
| GenomeScope2 | 2.0+galaxy2 | Estimate genome size and heterozygosity |
| hifiasm | 0.19.8+galaxy0 | Assemble contigs using HiFi and Hi-C reads |
| gfastats | 1.3.6+galaxy0 | Convert GFA to FASTA and generate statistics |
| BUSCO | 5.5.0+galaxy0 | Check gene completeness of assembly |
| Merqury | 1.3+galaxy3 | Measure assembly accuracy using k-mers |
| Bionano Hybrid Scaffold | — | Scaffold contigs using optical maps |
| BWA-MEM | 0.7.17+galaxy4 | Map Hi-C reads to assembly |
| Samtools | 1.15+galaxy0 | Filter and sort mapped reads |
| YaHS | 1.2a+galaxy1 | Hi-C chromosome-level scaffolding |
| PretextMap | 0.1.9+galaxy0 | Generate Hi-C contact map |
| PretextSnapshot | 0.0.4+galaxy1 | Save contact map as image |

---

## Step-by-Step Workflow

### Step 1 — Adapter Trimming (Cutadapt)
The three HiFi FASTA files were first processed to remove any
adapter sequences that may have been introduced during sequencing.
These adapters can interfere with assembly if not removed. All reads
containing adapter sequences were discarded rather than trimmed.

### Step 2 — k-mer Counting (Meryl)
Meryl was used to count all 31-letter DNA substrings, called k-mers,
across all three HiFi read files. Each file was processed separately
to allow parallel computation, and the resulting databases were merged
into a single combined k-mer database.

### Step 3 — Genome Profiling (GenomeScope2)
The merged k-mer database was used as input to GenomeScope2, which
fits a statistical model to the k-mer frequency distribution. This
allowed estimation of the haploid genome size, heterozygosity level,
sequencing coverage, and repeat content before assembly began.

### Step 4 — Hi-C Phased Contig Assembly (hifiasm)
The trimmed HiFi reads were assembled into contigs using hifiasm
running in Hi-C phased mode. Both Hi-C read files were provided
alongside the HiFi reads, allowing hifiasm to use the chromatin
contact information to separate the two haplotypes. The output was
two separate contig graphs — one for Hap1 and one for Hap2.

### Step 5 — GFA to FASTA Conversion and Statistics (gfastats)
The assembly graph files produced by hifiasm are in GFA format.
gfastats was used to convert these into standard FASTA format for
downstream tools. gfastats was also used to generate assembly
statistics including contig count, total length, N50, and largest
contig for both haplotypes.

### Step 6 — Gene Completeness Assessment (BUSCO)
BUSCO was run on both haplotype FASTA files using the Ascomycota
lineage database. It checked whether genes expected to be present
in single copy across fungi were found in the assembly. A high
percentage of complete single-copy genes indicates a complete
and well-assembled genome.

### Step 7 — k-mer Quality Assessment (Merqury)
Merqury compared k-mers from the original reads against k-mers
in the two haplotype assemblies. This produced quality value scores
indicating base-level accuracy, completeness statistics, and spectra
plots showing how k-mers are distributed between the two haplotypes.

### Step 8 — Bionano Optical Map Scaffolding (Bionano Hybrid Scaffold)
The Bionano CMAP optical map file was used to scaffold the hifiasm
contigs. Optical mapping provides long-range physical information
about the order and spacing of sequence motifs across the genome,
which helps join contigs into longer scaffolds where Hi-C data
alone is insufficient.

### Step 9 — Hi-C Read Mapping (BWA-MEM and Samtools)
The Illumina Hi-C reads were mapped to the Bionano scaffolded
assembly using BWA-MEM. Samtools was then used to filter for
properly paired reads and sort the resulting BAM file by read name,
preparing it for input to the scaffolding tool.

### Step 10 — Hi-C Chromosome-Level Scaffolding (YaHS)
YaHS used the Hi-C contact frequency information from the mapped
reads to arrange the Bionano scaffolds into chromosome-level
sequences. Regions of the genome that physically interact more
frequently in the cell are placed closer together in the final
assembly.

### Step 11 — Contact Map Generation (PretextMap and PretextSnapshot)
Hi-C reads were remapped to the final YaHS scaffolded assembly.
PretextMap converted the BAM alignment file into a contact map
format. PretextSnapshot was used to export this as a PNG image,
producing a visual heatmap of Hi-C contacts across the genome.
For a correct chromosome-level assembly of S. cerevisiae, this
map should show 16 distinct bright diagonal blocks corresponding
to the 16 chromosomes.

---

## Explanation of Key Output Files

| File | What it means |
|------|---------------|
| `genomescope_linear_plot.png` | Shows k-mer frequency distribution — two peaks confirm a diploid genome |
| `genomescope_log_plot.png` | Same plot on a log scale — easier to see low-frequency k-mers |
| `genomescope_summary.txt` | Text summary of genome size, heterozygosity, and coverage estimates |
| `gfastats_contig_stats.txt` | Contig-level assembly stats for Hap1 and Hap2 before scaffolding |
| `final_assembly_stats.txt` | Scaffold-level stats after YaHS — shows final assembly quality |
| `busco_hap1_image.png` | Bar chart showing what percentage of expected genes were found |
| `busco_hap1_summary.txt` | Exact BUSCO gene counts as text |
| `merqury_spectra_cn.png` | k-mer copy number distribution colored by how many times found in assembly |
| `merqury_spectra_asm.png` | k-mers colored by which haplotype contains them — confirms phasing |
| `merqury_qv_stats.txt` | Quality value score — higher means fewer errors in assembly |
| `bionano_report.txt` | Full report from Bionano hybrid scaffolding step |
| `contact_map_pre_scaffolding.png` | Hi-C contact map before YaHS — shows contigs before ordering |
| `contact_map_final.png` | Hi-C contact map after YaHS — 16 diagonal blocks = 16 chromosomes |

---

## Results Summary

### Genome Profile — GenomeScope2

| Property | Value |
|----------|-------|
| Haploid genome size | ~11.7 Mb |
| Heterozygosity | ~0.576% |
| Diploid sequencing coverage | ~50x |
| Model fit | >93% |

---

### Contig Assembly — hifiasm Hi-C Phased

| Metric | Hap1 | Hap2 |
|--------|------|------|
| Number of contigs | ~16 | ~17 |
| Total contig length | ~11.3 Mb | ~12.2 Mb |
| Contig N50 | ~922 kb | ~923 kb |
| Largest contig | ~1.53 Mb | ~1.53 Mb |

---

### BUSCO Assessment — Ascomycota Lineage

| Category | Hap1 |
|----------|------|
| Complete single-copy | ~95.8% |
| Duplicated | Low |
| Missing | ~29 genes |

---

---

## How to Reproduce

1. Go to [usegalaxy.cz](https://usegalaxy.cz) and create a free account
2. Upload input data from the Zenodo links listed in the Input Data section above
3. Follow the complete tutorial step by step at the link in the References section
4. Run each tool in the order described in the Workflow section above
5. Download your result files and compare with those in this repository

---

## Conclusion

This project successfully applied the VGP genome assembly pipeline
to *Saccharomyces cerevisiae* S288C. Starting from raw synthetic
PacBio HiFi reads, the pipeline produced two phased haplotype
assemblies with high gene completeness of over 95% complete
single-copy BUSCO genes, a contig N50 of approximately 922 kb,
and chromosome-level scaffolding confirmed by a Hi-C contact map
showing 16 clear diagonal blocks matching the known 16 chromosomes
of this organism.

The combination of HiFi reads for accurate contig assembly, Hi-C
data for haplotype phasing and chromosome-level scaffolding, and
Bionano optical maps for additional contiguity improvement
demonstrates the strength of the multi-platform VGP approach for
producing reference-quality genome assemblies.

---

## References

1. Rhie et al. (2021). Towards complete and error-free genome assemblies of all vertebrate species. *Nature* 592, 737–746.
2. Cheng et al. (2021). Haplotype-resolved de novo assembly using phased assembly graphs with hifiasm. *Nature Methods* 18, 170–175.
3. Simão et al. (2015). BUSCO: assessing genome assembly and annotation completeness with single-copy orthologs. *Bioinformatics* 31(19), 3210–3212.
4. Rhie et al. (2020). Merqury: reference-free quality, completeness, and phasing assessment for genome assemblies. *Genome Biology* 21, 245.
5. Ranallo-Benavidez et al. (2020). GenomeScope 2.0 and Smudgeplots for reference-free profiling of polyploid genomes. *Nature Communications* 11, 1432.
6. [VGP Galaxy Tutorial](https://training.galaxyproject.org/training-material/topics/assembly/tutorials/vgp_genome_assembly/tutorial.html)

---

## Author

**[SYEDA LAJEEN]**
Assembly performed using the VGP pipeline on Galaxy as part of a genome bioinformatics course.
