# Dataset
Per poter rispondere alla domanda biologica alla quale ero interessato, è stato costruito un dataset all'interno del qaule sono state selezionate cinque specie differenti, ognuna appartenente alla famiglia Culicidae, ma con collocazione differente all'interno delle varie tribù che la compongono.

-----

### Rappresentazione del file 
Di seguito è riportata una rappresentazione delle informazioni raccolte per ciascuna delle specie indicate in tabella:

| Accession number | Nome specie | ID | Tribù |
| --- | --- | --- | --- |
| GCF_035046485.1 | Aedes_albopictus | Aedalb | Aedini |
| GCF_016801865.2 | Culex_pipiens | Culpip | Culicini |
| GCF_030247185.1 | Malaya_genurostris | Malgen | Sabethini
| GCF_029784135.1 | Toxorhynchites_rutilus | Toxrut | Toxorhynchitini |
| GCF_029784155.1 | Uranotaenia_lowii | Uralow | Uranotaeniini |

>"Accession number" rappresenta l'identificativo in baca dati NCBI di una sequenza specifica associata ad una specie

Il [Dataset](./dataset.tsv) è stato salvato in formato tsv in modo tale da possedere una tabulazione adatta per le operazioni successive.

-----

### Download 
Ad ogni specie è stato associato un genoma e un file .gff, acquisiti direttamente dall'Accession number estratto in precedenza. Tutto il procedimento è stato eseguito grazie ad uno script specifico.

```bash
bash ../99_scripts/download_dataset.sh dataset.tsv
```
