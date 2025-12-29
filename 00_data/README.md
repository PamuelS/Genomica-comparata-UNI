# Dataset
Per poter rispondere alla domanda biologica alla quale ero interessato, è stato costruito un dataset all'interno del qaule sono state selezionate cinque specie differenti, ognuna appartenente alla famiglia Culicidae e alla sottofamiglia delle Culicinae, ma con collocazione differente all'interno delle varie tribù che la compongono.
La peculiarità di queste specie e il motivo per cui sono state selezionate risiede proprio nelle differenze sviluppate nei comportamenti alimentari, infatti solamente le femmine adulte delle specie *Aedes albopictus*, *Culex pipiens* e *Uranotaenia lowii* basano la propria alimentazione sulla raccolta di sangue, a differenza degli adulti di sesso femminile delle specie *Malaya genurostris* e *Toxorhynchites rutilus* che si alimentano unicamente di nettare.


-----

### Rappresentazione del file 
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

### Download 
Ad ogni specie è stato associato un genoma e un file .gff, acquisiti direttamente dall'Accession number estratto in precedenza. Tutto il procedimento è stato eseguito grazie ad uno script specifico.

```bash
bash ../99_scripts/download_dataset.sh dataset.tsv
```

-----

### Manipolazione dei dati
una volta che le informazioni relative al genoma e alla sua annotazione sono state scaricate per ciascuna delle specie, si può proseguire con la modifica di alcune intestazioni e l'eliminazione di dati in eccesso (es. pseudogeni e isoforme)

#### Isoforma più lunga 
Ad ogni gene possono essere associate molteplici isoforme, quindi con questo comando vengono eliminate tutte le isoforme al di fuori dell'isoforma più lunga.
```bash
for gff in *.gff; do agat_sp_keep_longest_isoform.pl --gff "$gff"-o ${gff/.gff/_longest.gff}; done
```

#### Creazione del Proteoma
Mediante i dati ottenuti, vengono creati i proteomi per ognuna delle specie. Questo comando consente di estrarre le sequenze associate ad ogni gene e di tradurle sotto forma di amminoacido.

```bash
for gff in *_longest.gff; do agat_sp_extract_sequences.pl -g "$gff" -f ../00_genome/${gff/_longest.gff/.fna} -t cds -p --cfs --output ../02_Proteome/${gff/_longest.gff/.faa}; done
```

#### Eliminazione pseudogeni
All'interno dei genomi scaricati dalla bancadati, potrebbero essere presenti delle sequenze riconosciute come pseudogeni. Queste determinate sequenze vengono rimosse con il seguente comando, dato che non rappresentano CDS (Coding DNA Sequencing)
>Uno pseudogene viene solitamente identificato dentro un file, grazie alla presenza del simbolo * all'interno della sequenza

```bash
bash ../../99_scripts/pseudogene_find_eliminate.sh
```

#### Modifica dell'intestazione 
Per rendere il file dei proteomi più chiaro e per prepararlo alle successive operazioni, viene modificata l'intestazioine (header) per ciascun gene che ritroviamo nella specie.

```bash
for prot in *.faa; do ID=$(basename -s .faa "$prot"); sed -i.old -E "s/>(rna-XM_[0-9]+\.[0-9]) (gene=gene-.[^ ]+) name=(.[^ ]+) .+$/>${ID}\|\3/" "$prot"; done
```
