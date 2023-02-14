# TP1

## Table of contents
* [Genome assembly using PacBio HiFi reads](#Genome-assembly-using-PacBio-HiFi-reads)
* [Genome assembly using Nanopore reads](#Genome-assembly-using-Nanopore-reads)
* [Genome assembly using Illumina reads](#Genome-assembly-using-Illumina-reads)
* [Hybrid genome assembly](#Hybrid-genome-assembly)

## Genome assembly using PacBio HiFi reads

Reads available at [ERR9588941](https://www.ebi.ac.uk/ena/browser/view/ERR9588941).

### Preliminary analyses of HiFi reads

[KAT](https://github.com/TGAC/KAT)
```sh
kat hist -o kat_hist_hifi ERR9588941.fastq.gz 
kat gcp -o kat_gcp_hifi ERR9588941.fastq.gz 
```

### *De novo* assembly

[Flye](https://github.com/fenderglass/Flye) 
```sh
flye -o ERR9588941_flye_v29_default --pacbio-hifi ERR9588941.fastq.gz
cp ERR9588941_flye_v29_default/assembly.fasta ERR9588941.flye_v29_default.fasta 
```

[hifiasm](https://github.com/chhylp123/hifiasm) 
```sh
hifiasm -o ERR9588941.hifiasm_v016 ERR9588941.fastq.gz 
awk '$1 ~/S/ {print ">"$2"\n"$3}' ERR9588941.hifiasm_v016.bp.p_ctg.gfa > ERR9588941.hifiasm_v016.bp.p_ctg.fasta
```

### First assembly evaluation

```sh
raw_n50 ERR9588941.flye_v29_default.fasta 
```

## Genome assembly using Nanopore reads

### *De novo* assembly

[Flye](https://github.com/fenderglass/Flye)
```sh
flye -o mycobacterium_flye_v29_default --nano-raw ONT_reads.fa.gz
cp mycobacterium_flye_v29_default/assembly.fasta mycobacterium.flye_v29_default.fasta 
```

[minimap2](https://github.com/lh3/minimap2) 
[miniasm](https://github.com/lh3/miniasm) 
```sh
minimap2 -x ava-ont ONT_reads.fa.gz ONT_reads.fa.gz > ONT_reads_ava.paf
miniasm -f ONT_reads.fa.gz ONT_reads_ava.paf > mycobacterium.miniasm_v03_default.gfa
awk '$1 ~/S/ {print ">"$2"\n"$3}' mycobacterium.miniasm_v03_default.gfa > mycobacterium.miniasm_v03_default.fasta
```

[Raven](https://github.com/lbcb-sci/raven) 
```sh
raven --graphical-fragment-assembly mycobacterium.raven_v181_default.gfa ONT_reads.fa.gz > mycobacterium.raven_v181_default.fasta
```

[wtdbg2](https://github.com/ruanjue/wtdbg2) 
```sh
wtdbg2.pl -o mycobacterium.wtdbg2_v25_g5Mb -t 20 -x ont -g 5m ONT_reads.fa.gz
cp mycobacterium.wtdbg2_v25_g5Mb.cns.fa mycobacterium.wtdbg2_v25_g5Mb.fasta
```

### First assembly evaluation

## Genome assembly using Illumina reads

### Preliminary analysis of Illumina reads

```sh
kat hist -o kat_hist_illumina Illumina_reads1.fq Illumina_reads2.fq
kat gcp -o kat_gcp_illumina Illumina_reads1.fq Illumina_reads2.fq
```

### *De novo* assembly

[idba](https://github.com/loneknightpy/idba) 
```sh
fq2fa --merge Illumina_reads1.fq Illumina_reads2.fq illumina.both_ends.fasta
idba -o idba_out -l illumina.both_ends.fasta
cp idba_out/contig.fa mycobacterium.idba_v113_default.fasta
```

[Megahit](https://github.com/voutcn/megahit) 
```sh
megahit -1 Illumina_reads1.fq -2 Illumina_reads2.fq -o megahit_out
cp megahit_out/final.contigs.fa mycobacterium.megahit_v129_default.fasta
```

### First assembly evaluation

## Hybrid genome assembly

### *De novo* assembly

[Unicycler](https://github.com/rrwick/Unicycler)
```sh
unicycler-runner.py -1 Illumina_reads1.fq -2 Illumina_reads2.fq -l ONT_reads.fa.gz -o unicycler_out
cp unicycler_out/assembly.fasta mycobacterium.unicycler_v050_default.fasta
```

### First assembly evaluation 

**Unicycler**
```sh
raw_n50 mycobacterium.unicycler_v050_default.fasta
```
