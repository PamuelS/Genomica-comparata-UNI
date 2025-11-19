# Kmer - Based Genome Survey
Procedimento iniziale che consente di acquisire le informazioni sullo stato di qualità delle short-reads, ottenute precedentemente mediante il sequenziamento dell'organismo Aopheles stephensis.                        Per ottenere queste informazioni, vengono esegueite tre operazioni distinte riportate al di sotto.


## Controllo qualitativo
Analisi del controllo della qualità delle Reads.

```bash
#|assembly|
fastqc SRR11672503_1.fastq.gz SRR11672503_2.fastq.gz
```


## Sequenze paired e unpaired
Suddivisione delle short-reads sulla base della corrispondenza ottenuta attraverso la complementarietà delle reads sovrapposte.

```bash
#|assembly|
trimmomatic PE -threads 20 -phred33 SRR11672503_1.fastq.gz SRR11672503_2.fastq.gz SRR11672503_1_paired.fastq SRR11672503_1_unpaired.fastq SRR11672503_2_paired.fastq SRR11672503_2_unpaired.fastq ILLUMINACLIP:/opt/miniforge3/envs/assembly/share/trimmomatic-0.40-0/adapters/TruSeq3-PE.fa:2:30:10 LEADING:3 TRAILING:3 SLIDINGWINDOW:4:15 MINLEN:36 2> stats_trimmomatic.log 
```


## Kmer method
Applicazione del metodo Kmer alle reads che sono risultate paired nel passaggio precedente.

```bash
#|kat|
kat hist -t 6 -m 27 -o Anoste_kmer27 SRR11672503_1_paired_fastqc.html SRR11672503_2_paired_fastqc.html
```
