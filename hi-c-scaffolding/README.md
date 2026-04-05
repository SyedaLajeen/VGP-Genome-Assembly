# Hi-C Scaffolding — YaHS and Contact Maps

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
