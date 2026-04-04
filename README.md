# VGP Genome Assembly, *Saccharomyces cerevisiae*

De novo chromosome-level genome assembly of *Saccharomyces cerevisiae* using 
PacBio HiFi, Bionano optical maps, and Illumina Hi-C data, following the VGP 
pipeline on Galaxy.

---

## Repository Structure
```
.
├── images/
│   ├── busco/              # BUSCO completeness bar charts for hap1 and hap2
│   ├── contactmaps/
│   │   ├── before_yahs/    # Hi-C contact maps generated from Bionano scaffolds (pre-YaHS)
│   │   └── after_yahs/     # Hi-C contact maps after YaHS scaffolding (final assembly)
│   ├── genomescope/        # GenomeScope2 linear and log k-mer plots
│   └── merqury/            # Merqury spectra-cn and spectra-asm plots for both haplotypes
└── results/
    ├── assembly/           # Final scaffold AGP files from YaHS (scaffolding rounds + final)
    └── stats/              # Assembly statistics: gfastats, BUSCO summaries, GenomeScope2 outputs, Bionano stats
```
## Pipeline
![VGP Workflow](images/vgp-workflow.jpeg)

*Official VGP pipeline diagram showing the four workflow stages: k-mer profiling (WF1), 
HiFi + Hi-C assembly with hifiasm (WF4), Bionano scaffolding (WF7), and Hi-C scaffolding (WF8).*
```
HiFi reads (3 FASTA files, PacBio CCS)
        |
    Cutadapt — trims adapter sequences from HiFi reads
        |
    Meryl — counts k-mers (k=21) across all trimmed reads
        |
    GenomeScope2 — estimates genome size (~11.7 Mb), heterozygosity (~0.58%),
                   and ploidy from the k-mer histogram
        |
    hifiasm — assembles HiFi reads into phased contigs using Hi-C data
              to partition haplotypes; produces hap1 and hap2 contig graphs
        |
    gfastats — converts GFA graphs to FASTA and reports assembly stats
               (contig count, N50, total length) for both haplotypes
        |
    BUSCO — assesses gene space completeness against Saccharomycetes
            lineage database (hap1: ~96%, hap2: ~88.7%)
        |
    Merqury — evaluates assembly quality using k-mers from the reads;
              generates spectra-cn and spectra-asm plots to confirm
              phasing and completeness
        |
    Bionano Hybrid Scaffold — integrates optical map data with hap1 contigs
                              to resolve large-scale misassemblies and
                              increase scaffold contiguity (N50: ~923 kb)
        |
    BWA-MEM2 — maps forward and reverse Hi-C reads separately against
               the Bionano-scaffolded assembly
        |
    Filter and merge — filters chimeric Hi-C read pairs using the
                       Arima Genomics protocol (mapq >= 20)
        |
    YaHS — uses Hi-C contact frequency to order and orient scaffolds
           into chromosome-level sequences; produces 16 final scaffolds
           matching the expected chromosome count of S. cerevisiae
        |
    PretextMap + Pretext Snapshot — converts final BAM to a contact map
                                    and exports PNG images for visual
                                    validation of scaffolding quality
```
---

## Species

| | |
|---|---|
| Species | *Saccharomyces cerevisiae* S288C |
| Estimated genome size | ~11.7 Mb |
| Heterozygosity | ~0.58% |
| Ploidy | Diploid |
| Expected chromosomes | 16 |

---

## Tools

| Tool | Version | Purpose |
|---|---|---|
| Cutadapt | 4.4+galaxy0 | HiFi read trimming |
| Meryl | 1.3+galaxy7 | k-mer counting |
| GenomeScope2 | 2.0+galaxy2 | Genome profiling |
| hifiasm | 0.19.9+galaxy0 | Contig assembly |
| gfastats | - | Assembly statistics |
| BUSCO | 5.5.0+galaxy0 | Gene completeness |
| Merqury | 1.3+galaxy3 | k-mer QC |
| Bionano Hybrid Scaffold | 3.7.0+galaxy3 | Optical map scaffolding |
| BWA-MEM2 | 2.2.1+galaxy1 | Hi-C read mapping |
| Filter and merge | 1.0+galaxy1 | Chimeric read filtering |
| YaHS | 1.2a.2+galaxy1 | Hi-C scaffolding |
| PretextMap | 0.1.9+galaxy0 | Contact map generation |
| Pretext Snapshot | 0.0.3+galaxy1 | Contact map visualization |

---

## Results

| Metric | Value |
|---|---|
| Hap1 contigs | 16 |
| Hap2 contigs | 17 |
| Hap1 BUSCO completeness | ~96% |
| Hap2 BUSCO completeness | ~88.7% |
| Bionano N50 | ~923 kb |
| Final chromosomes after YaHS | 16 |

---

## Genome Profile

| Linear | Log |
|---|---|
| ![linear](images/genomescope/genomescope_linear.png) | ![log](images/genomescope/genomescope_log.png) |

---

## BUSCO

| Hap1 | Hap2 |
|---|---|
| ![hap1](images/busco/busco_hap1.png) | ![hap2](images/busco/busco_hap2.png) |

---

## Hi-C Contact Maps

Before and after YaHS scaffolding. The clean diagonal in the after map 
confirms chromosome-level assembly.

| Before YaHS | After YaHS |
|---|---|
| ![before](images/contactmaps/before_yahs/before_yahs_fullmap.png) | ![after](images/contactmaps/after_yahs/after_yahs_fullmap.png) |

---

## Platform

All steps were run on Galaxy (usegalaxy.eu).

## Reference

GTN VGP Tutorial: [Vertebrate genome assembly using HiFi, Bionano and Hi-C data](https://training.galaxyproject.org/training-material/topics/assembly/tutorials/vgp_genome_assembly/tutorial.html)
