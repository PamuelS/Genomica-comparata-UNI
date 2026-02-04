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

## Annotazione funzionale degli ortogruppi
La fase di annotazione rappresenta un punto estremamente importante per la corretta riuscita di un arricchimento funzionale. Le tecniche utilizzate per svolgere l'annotazione possono essere molteplici, tuttavia per lo svolgimento di questo lavoro, sono stati utilizzati i programmi di: [InterProScan](https://github.com/ebi-pf-team/interproscan) e [Diamond](https://github.com/bbuchfink/diamond).

>è importante ricordare che **Diamond** serve per identificare la proteina associata ad una sequenza, mentre **Panther** consente di capire il ruolo della proteina (quindi ci associa un GO)

### Associazione dei Go terms mediante InterProScan
L'annotazione eseguita da InterProScan non si basa su una banale associazione di sequenza per similarità, ma consente di analizzare porzioni specifiche (ad esempio i domini proteici) contenute all'interno della sequenza per tentare l'associazione ad una famiglia proteica già nota, e (tramite la flag -goterms) li tramuta in GO

```bash
/home/PERSONALE/dbs/interproscan-5.65-97.0/interproscan.sh -i longest_protein_OGs.txt -goterms -pa -b longest_samuel.tsv -cpu <N_CPUS>
```

### Associazione dei GO terms mediante Diamond
L'altro metodo prevede l'utilizzo di Diamond. Per riuscire ad associare i GO terms alle sequenze del mio studio, è prima necessario convertire il file di input in un file specifico leggibile dal programma che utilizzeremo, ovvero Diamond. Questa operazione viene svolta per rendere più veloce e performante la lettura del file al programma.

```bash
diamond makedb --in /home/STUDENTI/samuel.pederzini/Genomica-comparata-UNI/09_GeneAnnotation_functional_enrichment/longest_protein_OGs.txt --db ./nr_diamond
```

Sempre utilizzando il programma Diamond si esegue un blast, per associare le sequenze proteiche relative ai miei ortogruppi, alle sequenze presenti nella banca dati. 
In questo modo Diamond riesce ad associare la sequenza di riferimento, con tutte le sequenze (presenti in questo caso nel databse NCBI) e restituisce 25 possibili risultati per una proteina.

## Creazione del Background
Per proseguire con la fase di arricchimento funzionale, è necessario creare il file di [Background](./go_back.tsv). Questo file contiene la lista di tutti i GO terms che sono stati associati alle sequenze proteiche relative ad ogni ortogruppo.

```bash
awk -F'\t' '{ gsub(/@.*/,"",$1); gsub(/\([^)]*\)/,"",$2); gsub(/\|/,",",$2); split($2,a,","); for(i in a) if(a[i]!="") seen[$1,a[i]]=1 } END { for(k in seen){ split(k,b,SUBSEP); groups[b[1]]=(groups[b[1]] ? groups[b[1]] "," b[2] : b[2]) } for(g in groups) print g "\t" groups[g] }' <(cut -f1,14 panther.tsv) > go_back.ts

```


-----

> **diamond_samuel.tsv** è il risultato del blast di Diamond (eseguito sul database di NCBI) dove sono state scelti 25 risultati per ogni proteina

> **longest_samuel.faa** è il mio file di input (ovvero quello della proteina più lunga)

> **longest_samuel.tsv** è il file dell'annotazione funzionale eseguito da Panther

> **diamond_samuel_names.tsv** è il file di mapping/rifinituare che contiene i nomi delle proteine associate agli ortogruppi


