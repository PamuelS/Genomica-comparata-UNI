# Raw Assembly
In questa fase si procede con l'assemblaggio del genoma a livello di contig (contig-level). Un contig rappresenta proprio una sequenza contigua di nucleotidi ottenuta mediante la sovrapposizione e l'allineamento di molteplici reads (short e long reads)


### Assemblaggio contig-level
Mediante la sovrapposizione delle reads riesco ad ottenere una sequenza univoca.

```bash
wtdbg2 -x rs -g <EXPECTED_GENOMESIZE> -t <NUMBERS_OF_CORES> -i <FASTQ> -fo Anoste_raw
```


### Creazione del consensus 
Riordinamento dei contig ottenuti con il passaggio precedente, per creare la sequenza di consensus ovvero l'insieme di contig correttamente disposti.

```bash
wtpoa-cns -t <NUMBER_OF_CORES> -i <LAYOUT_FILE-.ctg.lay.gz> -fo <OUT_PREFIX>
```


### Controllo qualitativo
Controllo della qualità della sequenza di consensus appena ottenuta. Il controllo è stato eseguito tramite tre metodi differenti ovvero: 
- N50
- Busco
- Spectra-cn (KAT)

#### N50
Un metodo utilizzato per verificare la contiguità relativa all'assemblaggio ottenuto. Indica con un numero il quantitativo di contig che rappresentano almeno il 50% del genoma assemlbato.
```bash
Assembly-stats Anoste_raw > Anoste_raw.stats
```

#### Busco
Metodo che confronta l'assemblaggio con un dataset di geni relativo al livello tassonomico di nostro interesse. Verifica le sequenze ottenute confrontandole direttamente con i geni presenti in un dato dataset, restituendo un valore in percentuale di delle tipologie di geni ritrovati.
```bash
export NUMEXPR_MAX_THREADS=80
busco -m geno -l BUSCO/culicidae_odb12 -c 6 -o Anoste_raw_busco -i Anoste_raw.fasta 
```

#### Spectra-cn (KAT)
Metodo che restituisce un grafico nel quale vengono confrontate le frequenze dei kmer nelle reads originali con quelle nel genoma assemblato, per stimare la coverage.
```bash
kat comp -t 8 -o Anoste_raw 'SRR11672503_1_paired.fastq SRR11672503_2_paired.fastq' Anoste_raw.fasta
```
