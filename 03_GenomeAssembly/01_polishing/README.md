# Polishing
Di seguito vengono riportati i comnandi e i programmi che sono stati utilizzati per compiere la pulizia del genoma.


### Mappatura delle reads
Mappatura delle short reads sul genoma assemblato

```bash
#|assembly|
minimap2 -ax --MD -t 6 Anoste_raw.fasta SRR11672503_1_paired_fastq SRR11672503_1_paired_fastq > Anoste_raw_sr.sam
samtools view -Sb Anoste_raw_sr.sam > Anoste_raw_sr.bam
rm Anoste_raw_sr.sam
samtools sort -@6 -o Anoste_raw_sr_sorted.bam Anoste_raw_sr.bam
samtools index Anoste_raw_sr_sorted.bam
rm Anoste_raw_sr.bam
```

Mappatura delle long reads sul genoma assemblato

```bash
#|assembly|
minimap2 -ax --MD -t 6 Anoste_raw.fasta SRR11672503_1_paired_fastq SRR11672503_1_paired_fastq > Anoste_raw_lr.sam
samtools view -Sb Anoste_raw_lr.sam > Anoste_raw_lr.bam
rm Anoste_raw_lr.sam
samtools sort -@6 -o Anoste_raw_lr_sorted.bam Anoste_raw_lr.bam
samtools index Anoste_raw_lr_sorted.bam
rm Anoste_raw_lr.bam
```


### Pulizia dell'assemblaggio
miglioramento della sequenza del genoma frazie al confronto con le reads mappate precedentemente

```bash
hypo -d Anoste_raw.fasta -r @Sr.path -s 227054799 -c 136 -b Anoste_raw_sr_sorted.bam -B Anoste_raw_lr_sorted.bam -t 6 
```
