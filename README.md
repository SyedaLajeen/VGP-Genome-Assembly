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
Think of it like solving a jigsaw puzzle — each read is a small piece,
and the assembler figures out how the pieces fit together to rebuild
the full picture.

Modern pipelines like VGP use multiple types of sequencing data
together to produce assemblies that are more accurate and complete
than any single technology alone.

---

## Objective

The goal of this project is to:

- Assemble the *S. cerevisiae* S288C genome from synthetic PacBio
  HiFi reads using the VGP Galaxy pipeline
- Produce two fully phased haplotype assemblies (Hap1 and Hap2)
- Evaluate assembly quality using multiple QC tools
- Scaffold contigs to chromosome level using Bionano and Hi-C data

---

## Organism

| Property | Value |
|----------|-------|
| Species | *Saccharomyces cerevisiae* |
| Strain | S288C |
| Estimated genome size | ~11.7 Mb |
| Ploidy | Diploid |
| Chromosomes | 16 |

---

## Input Data

| Dataset | Format | Source |
|---------|--------|--------|
| PacBio HiFi reads — file 1 | FASTA | [Zenodo 6098306](https://zenodo.org/record/6098306) |
| PacBio HiFi reads — file 2 | FASTA | [Zenodo 6098306](https://zenodo.org/record/6098306) |
| PacBio HiFi reads — file 3 | FASTA | [Zenodo 6098306](https://zenodo.org/record/6098306) |
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
| hifiasm | 0.19.8+galaxy0 | Assemble contigs using HiFi + Hi-C reads |
| gfastats | 1.3.6+galaxy0 | Convert assembly format and generate stats |
| BUSCO | 5.5.0+galaxy0 | Check gene completeness of assembly |
| Merqury | 1.3+galaxy3 | Measure assembly accuracy using k-mers |
| Bionano Hybrid Scaffold | — | Scaffold contigs using optical maps |
| BWA-MEM | 0.7.17+galaxy4 | Map Hi-C reads to assembly |
| Samtools | 1.15+galaxy0 | Filter and sort mapped reads |
| YaHS | 1.2a+galaxy1 | Hi-C based chromosome-level scaffolding |
| PretextMap | 0.1.9+galaxy0 | Generate Hi-C contact map |
| PretextSnapshot | 0.0.4+galaxy1 | Save contact map as image |

---

## Step-by-Step Workflow
HiFi reads (3 FASTA files)
│
▼
Step 1 — Adapter Trimming (Cutadapt)
Remove any adapter sequences that may have been added during
sequencing and could interfere with assembly.
│
▼
Step 2 — k-mer Counting and Genome Profiling (Meryl + GenomeScope2)
Count all 31-letter DNA substrings (k-mers) from the reads.
GenomeScope2 uses this to estimate genome size, heterozygosity,and sequencing coverage before assembly begins.
│
▼
Step 3 — Hi-C Phased Contig Assembly (hifiasm)
Assemble HiFi reads into contigs. Hi-C reads are used at this
stage to separate the two haplotypes (Hap1 and Hap2).
│
▼
Step 4 — Format Conversion and Statistics (gfastats)
Convert assembly graph files (GFA) to standard sequence files
(FASTA). Generate basic statistics like N50 and total length.
│
▼
Step 5 — Quality Control (BUSCO + Merqury)BUSCO checks if expected genes are present in the assembly.
Merqury compares k-mers from reads to the assembly to measure
base-level accuracy without needing a reference genome.
│
▼
Step 6 — Optical Map Scaffolding (Bionano)
Use physical DNA maps from Bionano to join contigs into
larger scaffolds and fill some gaps.
│
▼
Step 7 — Hi-C Scaffolding (BWA-MEM + Samtools + YaHS)
Map Hi-C reads to the Bionano scaffolds. YaHS uses the
contact frequency information to arrange scaffolds into
chromosome-level sequences.
│
▼
Step 8 — Contact Map Visualization (PretextMap + PretextSnapshot)
Generate a heatmap showing Hi-C contacts across the genome.
A good map shows 16 clear diagonal blocks for S. cerevisiae.
│
▼
Final Assembly — chromosome-level Hap1 assembly
---

## Results Summary

### Genome Profile (GenomeScope2)

| Property | Value |
|----------|-------|
| Haploid genome size | ~11.7 Mb |
| Heterozygosity | ~0.576% |
| Diploid sequencing coverage | ~50x |
| Model fit | >93% |

![GenomeScope2 Linear Plot](genome-scope/genomescope_linear_plot.png)

---

### Contig Assembly (hifiasm Hi-C phased)

| Metric | Hap1 | Hap2 |
|--------|------|------|
| Number of contigs | ~16 | ~17 |
| Total contig length | ~11.3 Mb | ~12.2 Mb |
| Contig N50 | ~922 kb | ~923 kb |
| Largest contig | ~1.53 Mb | ~1.53 Mb |

---

### BUSCO Assessment (Ascomycota lineage)

| Category | Hap1 |
|----------|------|
| Complete single-copy | ~95.8% |
| Duplicated | Low |
| Missing | ~29 genes |

![BUSCO Summary](busco/busco_hap1_image.png)

---

### Merqury k-mer QC

![Spectra-CN](merqury/merqury_spectra_cn.png)
![Spectra-ASM](merqury/merqury_spectra_asm.png)

---

### Hi-C Contact Maps

A contact map shows how often different parts of the genome
interact with each other. Bright diagonal blocks represent
individual chromosomes.

| Before Scaffolding | After Scaffolding |
|:-----------------:|:-----------------:|
| ![Pre](hi-c-scaffolding/contact_map_pre_scaffolding.png) | ![Final](hi-c-scaffolding/contact_map_final.png) |

> The final contact map shows clear chromosome-level organization
> consistent with the 16 chromosomes of *S. cerevisiae*.

---
---

## How to Reproduce

1. Go to [usegalaxy.cz](https://usegalaxy.cz) and create an account
2. Upload the input datasets from the Zenodo links in the Input Data section above
3. Follow the full step-by-step tutorial at:
   [VGP Galaxy Tutorial](https://training.galaxyproject.org/training-material/topics/assembly/tutorials/vgp_genome_assembly/tutorial.html)
4. Run each tool in the order described in the Workflow section above
5. Download result files and compare with this repository

---

## Conclusion

This project successfully demonstrates the VGP genome assembly
pipeline applied to *Saccharomyces cerevisiae* S288C. Starting from
raw PacBio HiFi reads, the pipeline produced two phased haplotype
assemblies with high completeness (>95% BUSCO), good contiguity,
and chromosome-level scaffolding confirmed by Hi-C contact maps.

The use of multiple data types — HiFi reads for accurate contig
assembly, Hi-C data for phasing and scaffolding, and Bionano optical
maps for additional contiguity — demonstrates the power of the
multi-platform VGP approach for producing reference-quality genomes.

---

## References

1. Rhie et al. (2021). Towards complete and error-free genome
   assemblies of all vertebrate species. *Nature* 592, 737–746.
2. Cheng et al. (2021). Haplotype-resolved de novo assembly using
   phased assembly graphs with hifiasm. *Nature Methods* 18, 170–175.
3. Simão et al. (2015). BUSCO: assessing genome assembly and
   annotation completeness with single-copy orthologs.
   *Bioinformatics* 31(19), 3210–3212.
4. Rhie et al. (2020). Merqury: reference-free quality, completeness,
   and phasing assessment for genome assemblies. *Genome Biology* 21, 245.
5. Ranallo-Benavidez et al. (2020). GenomeScope 2.0 and Smudgeplots
   for reference-free profiling of polyploid genomes.
   *Nature Communications* 11, 1432.
6. [VGP Galaxy Tutorial](https://training.galaxyproject.org/training-material/topics/assembly/tutorials/vgp_genome_assembly/tutorial.html)

---

## Author

**[SYEDA LAJEEN]**
Assembly performed using the VGP pipeline on Galaxy as part of a
genome bioinformatics course.
