# Steps to make bwa indices for ATACseq pipeline

### Reference fasta

The human GDC hg38 fasta file is downloaded from [GDC website](https://gdc.cancer.gov/about-data/gdc-data-processing/gdc-reference-files). The GDC_hg38 fasta file contains random chromosomes and decoy chromosomes and viral genome fasta.

e.g.,

```
chr11_KI270721v1_random.fa
chrUn_GL000195v1.fa
chrUn_KN707992v1_decoy.fa
HBV.fa
HPV100.fa
```

read:

*  https://genestack.com/blog/2016/07/12/choosing-a-reference-genome/
*  https://lh3.github.io/2017/11/13/which-human-reference-genome-to-use

We will only use conventional chromosomes chr1 to chr22 and chrX, chrY.

On `kraken`:

```bash
cd /liulab/mtang/check_files/GDC_hg38/raw_genome

cat chr{1..22}.fa chrX.fa chrY.fa > ../../GDC_hg38_bwa/GDC_hg38_convential_chrs.fa

```


### build bwa indices 

```bash
conda activate chips

## bwa Version: 0.7.15-r1140
cd ../../GDC_hg38_bwa/
bwa index -a bwtsw GDC_hg38_convential_chrs.fa -p GDC_hg38

...
[BWTIncConstructFromPacked] 680 iterations done. 6165927008 characters processed.
[bwt_gen] Finished constructing BWT in 686 iterations.
[bwa_index] 2636.03 seconds elapse.
[bwa_index] Update BWT... 16.80 sec
[bwa_index] Pack forward-only FASTA... 13.92 sec
[bwa_index] Construct SA from BWT and Occ... 1256.72 sec
[main] Version: 0.7.15-r1140
[main] CMD: bwa index -a bwtsw -p GDC_hg38 GDC_hg38_convential_chrs.fa
[main] Real time: 4035.590 sec; CPU: 3947.647 sec
```


