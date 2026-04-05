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
GenomeScope2 uses this to estimate genome size, heterozygosity,
and sequencing coverage before assembly begins.
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
Step 5 — Quality Control (BUSCO + Merqury)
BUSCO checks if expected genes are present in the assembly.
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

## Repository Structure
VGP-Genome-Assembly-Saccharomyces-cerevisiae/
│
├── genome-scope/              # GenomeScope2 genome profile plots and summary
│   ├── genomescope_linear_plot.png
│   ├── genomescope_log_plot.png
│   └── genomescope_summary.txt
│
├── assembly-stats/            # gfastats contig and scaffold statistics
│   ├── gfastats_contig_stats.txt
│   └── final_assembly_stats.txt
│
├── busco/                     # BUSCO gene completeness assessment
│   ├── busco_hap1_image.png
│   └── busco_hap1_summary.txt
│
├── merqury/                   # Merqury k-mer accuracy and completeness
│   ├── merqury_spectra_cn.png
│   ├── merqury_spectra_asm.png
│   └── merqury_qv_stats.txt
│
├── bionano/                   # Bionano optical map scaffolding
│   └── bionano_report.txt
│
├── hi-c-scaffolding/          # YaHS scaffolding and Hi-C contact maps
│   ├── contact_map_pre_scaffolding.png
│   └── contact_map_final.png
│
└── README.md

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

**[Your Name]**
Assembly performed using the VGP pipeline on Galaxy as part of a
genome bioinformatics course.

Click Commit changes ✅


Your Complete Checklist
TaskStatusDownload all 12 files from Galaxy⬜Create repo VGP-Genome-Assembly-Saccharomyces-cerevisiae⬜Create genome-scope/README.md⬜Upload 3 genomescope files⬜Create assembly-stats/README.md⬜Upload 2 assembly stats files⬜Create busco/README.md⬜Upload 2 busco files⬜Create merqury/README.md⬜Upload 3 merqury files⬜Create bionano/README.md⬜Upload bionano report⬜Create hi-c-scaffolding/README.md⬜Upload 2 contact map images⬜Edit main README.md⬜Replace Your Name with your actual name⬜give more clear steps like how to create each file all step by step11:06 PMHere are complete beginner steps for every single action.

PART 1 — Download files from Galaxy

Go to your Galaxy history on usegalaxy.cz
Find dataset 98 in your history
Click the 💾 download icon on that dataset
Your browser will download a file — rename it to genomescope_linear_plot.png
Repeat for every dataset below:

Dataset #Rename downloaded file to98genomescope_linear_plot.png99genomescope_log_plot.png102genomescope_summary.txt124gfastats_contig_stats.txt127busco_hap1_image.png128busco_hap1_summary.txt132click to open collection → download each png → merqury_spectra_cn.png and merqury_spectra_asm.png133merqury_qv_stats.txt153bionano_report.txt164click to open collection → download first image → contact_map_pre_scaffolding.png206final_assembly_stats.txtFinal Pretext snapshotcontact_map_final.png
Put all downloaded files in one folder on your desktop called vgp_files so you can find them easily.

PART 2 — Create GitHub Repository

Open your browser and go to github.com
Sign in to your account
Look at the top right corner — click the + icon
Click New repository from the dropdown

![You will see a form like this]

Fill in the form:

Repository name box → type: VGP-Genome-Assembly-Saccharomyces-cerevisiae
Description box → type: De novo diploid genome assembly of S. cerevisiae S288C using the VGP Galaxy pipeline with PacBio HiFi and Hi-C data
Click the Public radio button
Scroll down → find Add a README file checkbox → tick it ✅


Click the green Create repository button
Your repo is now created and you will be taken to its main page


PART 3 — Create genome-scope folder

In GitHub you cannot create an empty folder. You must create a file inside it at the same time.


You are on your repo main page
Click Add file button (top right area of file list)
Click Create new file from dropdown
You will see a text box at the top that says Name your file
Click inside that box and type: genome-scope/README.md

As soon as you type the / GitHub will automatically create the folder


Now click in the big white text area below (where it says Enter file contents here)
Copy and paste this exactly:

markdown# GenomeScope2 — Genome Profiling

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

Scroll down to the bottom of the page
You will see a box that says Commit new file
In the first text box type: Add genome-scope folder
Make sure Commit directly to the main branch is selected
Click the green Commit new file button
You will be taken back to your repo and you will see the genome-scope folder is now created ✅


PART 4 — Upload files into genome-scope folder

Click on the genome-scope folder in your repo to open it
You will see the README.md file inside
Click Add file button
Click Upload files
You will see a big box that says Drag files here to add them to your repository
Open your vgp_files folder on your desktop
Drag these 3 files from your desktop folder into that box:

genomescope_linear_plot.png
genomescope_log_plot.png
genomescope_summary.txt


Wait for them to upload — you will see a green tick next to each file
Scroll down to Commit changes section
In the text box type: Add GenomeScope plots and summary
Click green Commit changes button ✅


PART 5 — Create assembly-stats folder

Click your repo name at the top to go back to main page
Click Add file → Create new file
In the filename box type: assembly-stats/README.md
In the content area paste:

markdown# Assembly Statistics — gfastats

This folder contains assembly statistics generated by gfastats
at two stages of the pipeline.

## Files

| File | Description |
|------|-------------|
| `gfastats_contig_stats.txt` | Contig-level stats for Hap1 and Hap2 after hifiasm |
| `final_assembly_stats.txt` | Final scaffold-level stats after YaHS Hi-C scaffolding |

## What these statistics mean

- **Contigs** — continuous sequences assembled from reads with no gaps
- **Scaffolds** — contigs joined together using Hi-C contact information
- **N50** — the length where 50% of the assembly is in sequences
  this long or longer. Higher N50 = better contiguity
- **Largest contig/scaffold** — the longest single sequence in assembly

## Contig Stats — hifiasm Hi-C phased (pre-scaffolding)

| Metric | Hap1 | Hap2 |
|--------|------|------|
| Number of contigs | ~16 | ~17 |
| Total length | ~11.3 Mb | ~12.2 Mb |
| Contig N50 | ~922 kb | ~923 kb |
| Largest contig | ~1.53 Mb | ~1.53 Mb |

## Final Scaffold Stats — post YaHS

See `final_assembly_stats.txt` for exact values.

Scroll down → commit message: Add assembly-stats folder
Click Commit new file ✅

Upload files into assembly-stats:

Click on assembly-stats folder to open it
Click Add file → Upload files
Drag and drop:

gfastats_contig_stats.txt
final_assembly_stats.txt


Commit message: Add assembly statistics files
Click Commit changes ✅


PART 6 — Create busco folder

Go back to main repo page — click your repo name at top
Click Add file → Create new file
Filename box type: busco/README.md
Content area paste:

markdown# BUSCO — Assembly Completeness Assessment

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

Commit message: Add busco folder
Click Commit new file ✅

Upload files into busco:

Click busco folder
Add file → Upload files
Drag and drop:

busco_hap1_image.png
busco_hap1_summary.txt


Commit message: Add BUSCO result files
Click Commit changes ✅


PART 7 — Create merqury folder

Go back to main repo page
Click Add file → Create new file
Filename: merqury/README.md
Paste:

markdown# Merqury — k-mer Based Quality Assessment

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

Commit message: Add merqury folder
Click Commit new file ✅

Upload files into merqury:

Click merqury folder
Add file → Upload files
Drag and drop:

merqury_spectra_cn.png
merqury_spectra_asm.png
merqury_qv_stats.txt


Commit message: Add Merqury QC files
Click Commit changes ✅


PART 8 — Create bionano folder

Go back to main repo page
Click Add file → Create new file
Filename: bionano/README.md
Paste:

markdown# Bionano — Optical Map Scaffolding

Bionano optical mapping labels DNA at specific sequence motifs
and images the fragments. The resulting optical map provides
long-range physical information that helps join contigs into
larger scaffolds.

## Parameters

| Parameter | Value |
|-----------|-------|
| Enzyme | DLE1 |
| Tool | Bionano Hybrid Scaffold |

## Files

| File | Description |
|------|-------------|
| `bionano_report.txt` | Full hybrid scaffolding report |

## What happens in this step

Bionano maps are compared against the hifiasm contigs. Where
the map and contigs agree on the order and spacing of sequences,
contigs are joined into scaffolds. The output is then passed
to YaHS for Hi-C based chromosome-level scaffolding.

Commit message: Add bionano folder
Click Commit new file ✅

Upload file into bionano:

Click bionano folder
Add file → Upload files
Drag and drop: bionano_report.txt
Commit message: Add Bionano scaffolding report
Click Commit changes ✅


PART 9 — Create hi-c-scaffolding folder

Go back to main repo page
Click Add file → Create new file
Filename: hi-c-scaffolding/README.md
Paste:

markdown# Hi-C Scaffolding — YaHS and Contact Maps

Hi-C sequencing captures physical contacts between genomic regions
inside the nucleus. DNA that is close together in 3D space tends
to interact more. YaHS uses this information to arrange scaffolds
into chromosome-level sequences.

## Pipeline
Hi-C reads (forward + reverse)
│
▼
BWA-MEM — map reads to Bionano scaffolds
│
▼
Samtools — filter properly paired reads, sort by name
│
▼
YaHS — scaffold to chromosome level using Hi-C contacts
│
▼
PretextMap — convert to contact map format
│
▼
PretextSnapshot — save as image

## Files

| File | Description |
|------|-------------|
| `contact_map_pre_scaffolding.png` | Contact map BEFORE YaHS scaffolding |
| `contact_map_final.png` | Contact map AFTER YaHS scaffolding — final result |

## How to Read a Contact Map

A contact map is a heatmap where brighter spots = more Hi-C contacts.
- Bright diagonal = nearby regions contact each other most
- Distinct square blocks along diagonal = individual chromosomes
- S. cerevisiae has 16 chromosomes so expect 16 clear blocks

## Contact Maps

### Before Scaffolding
![Pre-scaffolding](contact_map_pre_scaffolding.png)

### After YaHS Scaffolding — Final Assembly
![Final](contact_map_final.png)

Commit message: Add hi-c-scaffolding folder
Click Commit new file ✅

Upload files into hi-c-scaffolding:

Click hi-c-scaffolding folder
Add file → Upload files
Drag and drop:

contact_map_pre_scaffolding.png
contact_map_final.png


Commit message: Add Hi-C contact map images
Click Commit changes ✅


PART 10 — Edit main README.md

Click your repo name at the top to go to main page
You will see README.md in the file list
Click on README.md to open it
You will see the file contents with a toolbar above it
Click the pencil icon ✏️ on the right side of that toolbar
You are now in edit mode
Press Ctrl+A (Windows) or Cmd+A (Mac) to select all text
Press Delete to delete everything
Now paste this entire block:

markdown# VGP Genome Assembly — *Saccharomyces cerevisiae* S288C

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
Think of it like solving a jigsaw puzzle — each read is a small
piece and the assembler figures out how all the pieces fit together.

Modern pipelines like VGP use multiple types of sequencing data
together to produce assemblies that are more complete and accurate
than any single technology alone.

---

## Objective

- Assemble the *S. cerevisiae* S288C genome from synthetic PacBio HiFi reads
- Produce two fully phased haplotype assemblies — Hap1 and Hap2
- Evaluate quality using BUSCO, Merqury, and gfastats
- Scaffold contigs to chromosome level using Bionano and Hi-C data

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
| PacBio HiFi reads — 3 files, 50x | FASTA | [Zenodo 6098306](https://zenodo.org/record/6098306) |
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
HiFi reads (3 FASTA files)
│
▼
Step 1 — Adapter Trimming (Cutadapt)
Removes adapter sequences added during sequencing
│
▼
Step 2 — k-mer Counting and Genome Profiling
(Meryl → GenomeScope2)
Counts 31-letter DNA substrings to estimate genome size,
heterozygosity, and sequencing coverage
│
▼
Step 3 — Hi-C Phased Contig Assembly (hifiasm)
Assembles HiFi reads into contigs, uses Hi-C reads to
separate two haplotypes — Hap1 and Hap2
│
▼
Step 4 — Format Conversion and Statistics (gfastats)
Converts GFA assembly graphs to FASTA format,
generates N50, contig count, total length stats
│
▼
Step 5 — Quality Control (BUSCO + Merqury)
BUSCO checks gene presence and completeness
Merqury measures base-level accuracy using k-mers
│
▼
Step 6 — Optical Map Scaffolding (Bionano)
Uses physical DNA maps to join contigs into larger scaffolds
│
▼
Step 7 — Hi-C Scaffolding (BWA-MEM + Samtools + YaHS)
Maps Hi-C reads to scaffolds, YaHS arranges them into
chromosome-level sequences
│
▼
Step 8 — Contact Map (PretextMap + PretextSnapshot)
Generates a heatmap showing Hi-C contacts across the genome
16 diagonal blocks = 16 chromosomes of S. cerevisiae
│
▼
Final chromosome-level Hap1 assembly

---

## Results Summary

### Genome Profile — GenomeScope2

| Property | Value |
|----------|-------|
| Haploid genome size | ~11.7 Mb |
| Heterozygosity | ~0.576% |
| Diploid sequencing coverage | ~50x |
| Model fit | >93% |

![GenomeScope2 Linear Plot](genome-scope/genomescope_linear_plot.png)

---

### Contig Assembly — hifiasm Hi-C Phased

| Metric | Hap1 | Hap2 |
|--------|------|------|
| Number of contigs | ~16 | ~17 |
| Total contig length | ~11.3 Mb | ~12.2 Mb |
| Contig N50 | ~922 kb | ~923 kb |
| Largest contig | ~1.53 Mb | ~1.53 Mb |

---

### BUSCO — Ascomycota lineage

| Category | Hap1 |
|----------|------|
| Complete single-copy | ~95.8% |
| Duplicated | Low |
| Missing | ~29 genes |

![BUSCO](busco/busco_hap1_image.png)

---

### Merqury k-mer QC

![Spectra-CN](merqury/merqury_spectra_cn.png)
![Spectra-ASM](merqury/merqury_spectra_asm.png)

---

### Hi-C Contact Maps

| Before Scaffolding | After Scaffolding |
|:-----------------:|:-----------------:|
| ![Pre](hi-c-scaffolding/contact_map_pre_scaffolding.png) | ![Final](hi-c-scaffolding/contact_map_final.png) |

> The final contact map shows 16 diagonal blocks confirming
> successful chromosome-level assembly of *S. cerevisiae*.

---

## Repository Structure
VGP-Genome-Assembly-Saccharomyces-cerevisiae/
│
├── genome-scope/              # GenomeScope2 genome profile plots and summary
├── assembly-stats/            # gfastats contig and scaffold statistics
├── busco/                     # BUSCO gene completeness results
├── merqury/                   # Merqury k-mer accuracy plots and stats
├── bionano/                   # Bionano optical map scaffolding report
├── hi-c-scaffolding/          # YaHS scaffolding and Hi-C contact maps
└── README.md                  # This file

---

## How to Reproduce

1. Go to [usegalaxy.cz](https://usegalaxy.cz) and create a free account
2. Upload the input data files from the Zenodo links in the Input Data section
3. Follow the full tutorial step by step at:
   [VGP Galaxy Tutorial](https://training.galaxyproject.org/training-material/topics/assembly/tutorials/vgp_genome_assembly/tutorial.html)
4. Run each tool in the order shown in the Workflow section above
5. Download your result files and compare with those in this repository

---

## Conclusion

This project successfully applied the VGP genome assembly pipeline
to *Saccharomyces cerevisiae* S288C. Starting from raw PacBio HiFi
reads, the pipeline produced two phased haplotype assemblies with:

- High gene completeness — >95% BUSCO single-copy genes present
- Good contiguity — Contig N50 of ~922 kb
- Chromosome-level scaffolding — confirmed by Hi-C contact maps
  showing 16 clear diagonal blocks

The combination of HiFi reads, Hi-C phasing, Bionano optical maps,
and YaHS scaffolding demonstrates the power of the multi-platform
VGP approach for producing reference-quality genome assemblies.

---

## References

1. Rhie et al. (2021). Towards complete and error-free genome assemblies
   of all vertebrate species. *Nature* 592, 737–746.
2. Cheng et al. (2021). Haplotype-resolved de novo assembly using phased
   assembly graphs with hifiasm. *Nature Methods* 18, 170–175.
3. Simão et al. (2015). BUSCO: assessing genome assembly and annotation
   completeness. *Bioinformatics* 31(19), 3210–3212.
4. Rhie et al. (2020). Merqury: reference-free quality, completeness,
   and phasing assessment. *Genome Biology* 21, 245.
5. Ranallo-Benavidez et al. (2020). GenomeScope 2.0 and Smudgeplots.
   *Nature Communications* 11, 1432.
6. [VGP Galaxy Tutorial](https://training.galaxyproject.org/training-material/topics/assembly/tutorials/vgp_genome_assembly/tutorial.html)

---

## Author

**[SYEDALAJEEN HAIDER]**
Assembly performed using the VGP pipeline on Galaxy.

