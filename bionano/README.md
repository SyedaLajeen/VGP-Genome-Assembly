# Bionano — Optical Map Scaffolding

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
