# Dataset
Per poter rispondere alla domanda biologica alla quale ero interessato, è stato costruito un dataset all'interno del qaule sono state selezionate cinque specie differenti, ognuna appartenente alla famiglia Culicidae e alla sottofamiglia delle Culicinae, ma con collocazione differente all'interno delle varie tribù che la compongono.
La peculiarità di queste specie e il motivo per cui sono state selezionate risiede proprio nelle differenze sviluppate nei comportamenti alimentari, infatti solamente le femmine adulte delle specie *Aedes albopictus*, *Culex pipiens* e *Uranotaenia lowii* basano la propria alimentazione sulla raccolta di sangue, a differenza degli adulti di sesso femminile delle specie *Malaya genurostris* e *Toxorhynchites rutilus* che si alimentano unicamente di nettare.


-----

## Rappresentazione del file 
Di seguito è riportata una rappresentazione delle informazioni raccolte per ciascuna delle specie indicate in tabella:

| Accession number | Nome specie | ID | Tribù | Alimentazione Adulto F |
| --- | --- | --- | --- | --- |
| GCF_035046485.1 | *Aedes_albopictus* | Aedalb | Aedini | Sangue |
| GCF_016801865.2 | *Culex_pipiens* | Culpip | Culicini | Sangue |
| GCF_030247185.1 | *Malaya_genurostris* | Malgen | Sabethini | Nettare |
| GCF_029784135.1 | *Toxorhynchites_rutilus* | Toxrut | Toxorhynchitini | Nettare |
| GCF_029784155.1 | *Uranotaenia_lowii* | Uralow | Uranotaeniini | Sangue |

>"Accession number" rappresenta l'identificativo in baca dati NCBI di una sequenza specifica associata ad una specie

Il [Dataset](./dataset.tsv) è stato salvato in formato tsv in modo tale da possedere una tabulazione adatta per le operazioni successive.

-----

## Download 
Ad ogni specie è stato associato un genoma e un file .gff, acquisiti direttamente dall'Accession number estratto in precedenza. Tutto il procedimento è stato eseguito grazie allo script "download_dataset.sh dataset.tsv".

```bash
bash ../99_scripts/download_dataset.sh dataset.tsv
```

-----
