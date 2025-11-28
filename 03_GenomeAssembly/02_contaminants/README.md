# Decontamination 
Per decontaminanzione del genoma, viene inteso quell'insieme di procedimenti che consentono di individuare ed eliminare le sequenze geniche non appartenenti all'organismo che sto studiando. Di seguito vengono riportati i comnandi e i programmi che sono stati utilizzati per la fase di decontaminazione del genoma sequenziato e assemblato.


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

-----

### Annotazione tassonomica dei contigs
Viene associata un gruppo tassonomico alle contigs che sono presenti nel genoma, grazie al confronto diretto con la banca dati NCBI

```bash
#|assembly|
blastn -query Anoste_pol.fasta -db <PATH/TO/nt/> -outfmt '6 qseqid staxids bitscore std sscinames sskingdoms stitle' -max_target_seqs 25 -max_hsps 1 -num_threads 25 -evalue 1e-25 -out Anoste_blast.tsv
```

-----

### Individuazione delle contigs contaminate
Per poter capire quali sono le contigs che non appartengono al genoma di *A. stephensis* utilizzo il programma Blobtools

```bash
#|sequence|
blobtools create -i Anoste_pol.fasta -b Anoste_pol_sorted.bam -t Anoste_blast.tsv -o Anoste_blob
blobtools view -i Anoste_blob.blobDB.jason -o Anoste
blobtools plot -i Anoste_blob.blobDB.jason -o Anoste
```

-----

### Salvataggio dei contigs non contaminati
Creazione di un file che contiene l'informazione di quali contigs sono stati associati ad *A. stephensis*, in questo caso che sono indicati con il nome "Arthropoda"

```bash
grep "Arthropoda" Anoste.Anoste_blob.blobDB.table.txt > contig_arthropoda.tsv
```

-----

### Estrazione dei contigs non contaminati in formato fasta
A questo punto si può associare il file con le informazioni dei contigs non contaminati, con la sequenza del genoma espressa in formato fasta, per completare l'operazione di decontaminazione

```bash
awk '{ if ((NR>1)&&($0~/^>/)) { printf("\n%s", $0); } else if (NR==1) { printf("%s", $0); } else { printf("\t%s", $0); } }' Anoste_pol.fasta | grep -w -Ff <(cut -f1 contig_arthropoda.tsv) - | tr "\t" "\n" > Anoste_decontaminated.fasta
```
