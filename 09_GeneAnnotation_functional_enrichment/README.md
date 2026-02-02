# Gene Annotation and Functional Enrichment
Una volta eseguita l'analisi di CAFE5, si può proseguire con: annotazione delle sequenze e arricchimento funzionale.

Per annotazione si intende l'associazione di una determinata funzione ad una sequenza oppure, come in questo specifico caso, ad un intero ortogruppo. Questo procedimento utilizza delle targhette standardizzate che vengono definite GO (Gene Onthology) che descrivono ruoli biologici ben specifici. 

I ruoli biologici vengono suddivisi in tre categorie diverse:
- Molecular Function (MF) cosa fa la proteina a livello molecolare
- Biological Process (BP) in quale processo biologico la proteina è coinvolta
- Cellular Components (CC) dove è situata la proteina nella cellula

Durante l'annotazione si fa sempre riferimento ad una banca dati dalla quale estrarre le informazioni relative alla sequenza che si sta osservando, perciò il ruolo della proteina viene dedotto per confronto con sequenze già caratterizzate.

In seguito alla annotazione, si esegue l'arricchimento funzionale che consente di convertire l'informazione associata ai GO terms in una interpretazione biologica. Questo viene eseguito tramite due informazioni fondamentali, ovvero: [GO Backrounds](./go_back.tsv) (che non è altro che l'annotazione delle sequenze di tutti gli ortogruppi ottenuti dai passaggi precedenti) e gli [ortogruppi di interesse](./ortogruppi_nettare_importante) (cioè gli ortogruppi che ho scelto personalmente perchè caratterizzati da una contrazione/espansione significativa nelle tip che mi interessano). **Una determinata funzione è definibile come arricchita quando compare molto più spesso nell'ortogruppo che sto studiando, rispetto al background.**

-----

## Creazione del file di annotazione
Per eseguire l'annotazione viene utilizzato come file di partenza il file che è stato ottenuto mediante l'allineamento e il trimming di tutti gli ortogruppi (longest_proteine_OGs.txt) ottenuti dopo l'esecuzione del comando di disco, al quali si associa lo script longest_protein_OGs.txt. 
Questo script consente di estrarre la proteina più lunga associata a ciascun ortogruppo, per limitare il ritrovamento di gaps all'interno della sequenza.

```bash
bash ../99_scripts/longest_protein_OG.sh /home/STUDENTI/samuel.pederzini/Genomica-comparata-UNI/05_OG.Inference_Phylogenomic/04_trimmed/tutti_ortogruppi_trimmed/longest_protein_OGs.txt

```

## Confronto delle sequenze con un Database
Per riuscire ad associare i GO terms alle sequenze del mio studio, è prima necessario fare un confronto diretto con tutte le sequenze già preesistenti all'interno di una banca dati. Per eseguire questa operazione è stato utilizzato il programma Diamond.

```bash
diamond makedb --in /home/STUDENTI/samuel.pederzini/Genomica-comparata-UNI/09_GeneAnnotation_functional_enrichment/longest_protein_OGs.txt --db ./nr_diamond
```
