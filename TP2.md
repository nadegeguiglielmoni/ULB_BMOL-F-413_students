# TP2

## Table of contents
* [Assembly graphs](#Assembly-graphs)
* [BUSCO score](#BUSCO-score)

### Assembly graphs

[gfastats]()
```sh
module load gfastats/1.3.6-GCCcore-11.2.0
```
```sh
gfastats mycobacterium.flye_v29_default.gfa
```

### BUSCO score

[BUSCO](https://busco.ezlab.org/)
```sh
busco -i mycobacterium.flye_v29_default.fasta -o busco_flye -m genome
```
