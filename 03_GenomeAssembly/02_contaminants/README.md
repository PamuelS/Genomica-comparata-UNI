# Decontamination 
Di seguito vengono riportati i comnandi e i programmi che sono stati utilizzati per la fase di decontaminazione del genoma sequenziato e assemblato.


### Ri-mappatura delle reads
Ulteriore mappatura delle short reads sul genoma che ha terminato la fase di pulizia

>Il codice in questione è stato scritto all'interno del file mapping_contaminants.sh
```bash
#|assembly|
minimap2 -ax sr --MD -t 8 Anoste_pol.fasta SRR11672503_1_paired_fastq SRR11672503_2_paired_fastq | samtools view -Sb - > Anoste-pol_sr.bam
samtools sort -@8 -o Anoste_pol_sr_sorted.bam Anoste_pol_sr.bam
samtools index Anoste_pol_sr_sorted.bam
rm Anoste_pol_sr.bam
```


### Annotazione tassonomica dei contigs
Viene associata un gruppo tassonomico alle contigs che sono presenti nel genoma, grazie al confronto diretto con la banca dati NCBI

```bash
#|assembly|
blastn -query Anoste_pol.fasta -db <PATH/TO/nt/> -outfmt '6 qseqid staxids bitscore std sscinames sskingdoms stitle' -max_target_seqs 25 -max_hsps 1 -num_threads 25 -evalue 1e-25 -out Anoste_blast.tsv
```


### Individuazione delle contigs contaminate
Per poter capire quali sono le contigs che non appartengono al genoma di *A. stephensis* utilizzo il programma Blobtools

```bash
#|sequence|
blobtools create -i Anoste_pol.fasta -b Anoste_pol_sorted.bam -t Anoste_blast.tsv -o Anoste_blob
blobtools view -i Anoste_blob.blobDB.jason -o Anoste
blobtools plot -i Anoste_blob.blobDB.jason -o Anoste
```
