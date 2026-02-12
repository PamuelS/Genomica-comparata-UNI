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
La fase di annotazione rappresenta un punto estremamente importante per la corretta riuscita di un arricchimento funzionale. Le tecniche utilizzate per svolgere l'annotazione possono essere molteplici, tuttavia per lo svolgimento di questo lavoro, sono stati utilizzati i programmi di: [InterProScan](https://github.com/ebi-pf-team/interproscan) e [Diamond](https://github.com/bbuchfink/diamond). Lo scopo ultimo di questo processo, è quello di riuscire ad associare una determinata funzione alle sequenze proteiche.

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

## Arricchimento funzionale
L'arricchimento funzionale corrisponde ad una analisi statistica, che mi consente di verificare se determinate funzioni biologiche sono particolarmente rappresentate all'interno dei geni presi in considerazione, rispetto alla totalità delle sequenze ottenute (background).

### Creazione del Background
Per proseguire con la fase di arricchimento funzionale, è necessario creare il file di [Background](./go_back.tsv).

Il background rappresenta l'elenco completo di tutti gli Orthogroups identificati da OrthoFinder che hanno ricevuto almeno un'annotazione funzionale (un GO term).

```bash
awk -F'\t' '{ gsub(/@.*/,"",$1); gsub(/\([^)]*\)/,"",$2); gsub(/\|/,",",$2); split($2,a,","); for(i in a) if(a[i]!="") seen[$1,a[i]]=1 } END { for(k in seen){ split(k,b,SUBSEP); groups[b[1]]=(groups[b[1]] ? groups[b[1]] "," b[2] : b[2]) } for(g in groups) print g "\t" groups[g] }' <(cut -f1,14 panther.tsv) > go_back.ts

```

### Scelta dei geni significativi
Dall'analisi di [CAFE5](../07_GeneFamilies_Evolution), dal file Base_asr.tre sono stati selezioni fli alberi che presentavano una significatività. I criteri di significatività utilizzati sono stati due:
- significatività solamente nelle specie che si [nutrono di nettare](./ortogruppi_nettare_importante.txt)
- significatività in tutte le specie che si [nutrono di sangue](./ortogruppi_sangue_importante.txt)
- significatività nel [nodo tra le due specie nettarivore](./oerofruppi_nodo_nettare_importante.txt)

### Analisi di arricchimento 
L'arricchimento vero e proprio viene eseguito tramite il programma R-studio, dove vengono inseriti i file di background, degli ortogruppi significativamente espansi/contratti e del codice da utilizzare per l'analisi.

```R
library(tidyverse)
library(topGO)

gene_universe <- readMappings(file =
                                "go_back_collapsed.tsv)
geneUniverse <- names(gene_universe)

genesOfInterest <- read.table("interesting.txt",header=FALSE)
list_interest1 <- list( "name_interest" = genesOfInterest)

#upload of gene of interest
GOenrichment <- function(trait, trait_name) {
  if (!dir.exists("01_enrichment")) {
    dir.create("01_enrichment")
  }
  
  genesOfInterest <- as.character(trait[[1]]) #as vector not character 
  geneList <- factor(as.integer(geneUniverse %in% genesOfInterest))
  names(geneList) <- geneUniverse
  
  print(trait_name)
  
  ontology_values = c("BP", "MF", "CC")
  
  GOdata_list <- lapply(ontology_values, function(ontology_value) {
    GOdata_name <- paste("GOdata_", ontology_value, sep = "")
    # annot = annFUN.gene2GO this imparts the program which annotation it should use. In this case, it is specified that it will be in gene2GO format and provided by the user.
    # gene2GO = gene_universe is the argument used to tell where is the annotation
    assign(GOdata_name, new("topGOdata", ontology=ontology_value, allGenes=geneList, annot = annFUN.gene2GO, gene2GO = gene_universe))
  })
  
  elim_list <- lapply(seq_along(ontology_values), function(i) {
    elim_name <- paste("elim_", ontology_values[i], sep="")
    assign(elim_name, runTest(GOdata_list[[i]], algorithm="elim", statistic="fisher"))
  })
  
  results_elim <- function(GO_data, elim_data) {
    num_nodes <- min(1000, length(elim_data@score))
    resulte <- GenTable(GO_data, Classic_Fisher = elim_data,
                        orderBy = "Classic_Fisher", topNodes=num_nodes, numChar=1000)
    resulte$Classic_Fisher <- as.numeric(resulte$Classic_Fisher)
    resulte <- subset(resulte, Classic_Fisher < 0.05)
    return(resulte)
  }
  
  results_elim_list <- lapply(seq_along(ontology_values), function(i) {
    resulte_name <- paste("resulte_", ontology_values[i], sep="")
    assign(resulte_name, envir = .GlobalEnv, results_elim(GOdata_list[[i]], elim_list[[i]]))
  })
  
  write_elim_results <- function(result, ontology_value, trait_name) {
    table_name <- paste("01_enrichment/topGOe_", trait_name, "_", ontology_value, ".txt", sep="")
    write.table(result, file=table_name, quote=F, sep = "\t", row.names = F)
  }
  
  lapply(seq_along(ontology_values), function(i) {
    write_elim_results(results_elim_list[[i]], ontology_values[i], trait_name)
  })
}

GMT <- function(trait, trait_name) {
  genesOfInterest <- as.character(trait$V1) #as vector not character 
  geneList <- factor(as.integer(geneUniverse %in% genesOfInterest))
  names(geneList) <- geneUniverse
  
  print(paste("Working on", trait_name, sep = " "))
  
  ontology_values = c("BP", "MF", "CC")
  
  GOdata_list <- lapply(ontology_values, function(ontology_value) {
    GOdata_name <- paste("GOdata_", ontology_value, sep = "")
    # annot = annFUN.gene2GO this imparts the program which annotation it should use. In this case, it is specified that it will be in gene2GO format and provided by the user.
    # gene2GO = gene_universe is the argument used to tell where is the annotation
    assign(GOdata_name, new("topGOdata", ontology=ontology_value, allGenes=geneList, annot = annFUN.gene2GO, gene2GO = gene_universe))
  })
  
  elim_list <- lapply(seq_along(ontology_values), function(i) {
    elim_name <- paste("elim_", ontology_values[i], sep="")
    assign(elim_name, runTest(GOdata_list[[i]], algorithm="elim", statistic="fisher"))
  })
  
  results_elim <- function(GO_data, elim_data) {
    resulte <- GenTable(GO_data, Classic_Fisher = elim_data, orderBy = "Classic_Fisher", topNodes=1000, numChar=1000)
    resulte$Classic_Fisher <- as.numeric(resulte$Classic_Fisher)
    resulte <- subset(resulte, Classic_Fisher < 0.05)
    return(resulte)
  }
  
  results_elim_list <- lapply(seq_along(ontology_values), function(i) {
    resulte_name <- paste("resulte_", ontology_values[i], sep="")
    assign(resulte_name, envir = .GlobalEnv, results_elim(GOdata_list[[i]], elim_list[[i]]))
  })
  
  #transform in GMT format
  list_OG_GO <- function(GO_term, GOdata, gene_of_interest){
    genes <- intersect(genesInTerm(GOdata, GO_term)[[1]], gene_of_interest)
    return(paste(genes, collapse = ","))
  }
  
  result_GTM_list <- lapply(seq_along(ontology_values), function(i) {
    results_elim_list[[i]]$Genes <- unlist(sapply(results_elim_list[[i]]$GO.ID, function(GO_term){list_OG_GO(GO_term, GOdata_list[[i]], genesOfInterest)}))
    return(results_elim_list[[i]])
  })
  
  print("Done! writing results.")
  
  write_gtm_results <- function(result, ontology_value, trait_name) {
    table_name <- paste("02_enrichment/GTM_", trait_name, "_", ontology_value, ".gtm", sep="")
    write.table(result, file=table_name, quote=F, sep = "\t", row.names = F)
  }
  
  lapply(seq_along(ontology_values), function(i) {
    write_gtm_results(result_GTM_list[[i]], ontology_values[i], trait_name)
  })
}

#Complete function to perform enrichment
##this particular syntax has been necessary since it was impossible to give the function the trait name it was computing.
GO_enrichment <- function(list) {
  lapply(seq_along(list), function(i) {
  GOenrichment(list[[i]], names(list)[i])
  })
}

GMT_parsing <- function(list) {
  lapply(seq_along(list), function(i) {
    GMT(list[[i]], names(list)[i])
  })
}

#Run the complete function
GO_enrichment(social_not_aculeata)
GMT_parsing(list_notext_11)

#search for specific gene
genesOfInterest <- as.character(notext_biggest$V1) #as vector not character 
geneList <- factor(as.integer(geneUniverse %in% genesOfInterest))
names(geneList) <- geneUniverse
topGO <- new("topGOdata", ontology="BP", allGenes=geneList, annot = annFUN.gene2GO, gene2GO = gene_universe)
print(intersect(genesInTerm(topGO, "GO:0051289")$'GO:0051289', notext_biggest$V1)) #to investigate which genes are annotated with a particular GO term
```

-----

> **diamond_samuel.tsv** è il risultato del blast di Diamond (eseguito sul database di NCBI) dove sono state scelti 25 risultati per ogni proteina

> **longest_samuel.faa** è il mio file di input (ovvero quello della proteina più lunga)

> **longest_samuel.tsv** è il file dell'annotazione funzionale eseguito da Panther

> **diamond_samuel_names.tsv** è il file di mapping/rifinituare che contiene i nomi delle proteine associate agli ortogruppi






