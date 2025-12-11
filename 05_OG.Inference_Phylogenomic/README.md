# Orthology Inference
Questa cartella è dedicata interamente all'analisi e alla ricostruzione delle relazioni tra le varie specie, in riferimento al dataset utilizzato, mediante l'inferenza di ortologia tra i vari geni osservati ed inoltre alla creazione dell'albero delle specie.
Prima di procedere è bene ricordare che la ricostruzione degli eventi evolutivi associati ad un gruppo di specie (come in questo caso) oppure a più taxa, viene tendenzialmente eseguito medianto lo studio dei vari geni ortologhi. Questo perchè i geni ortologhi rappresentano coppie di geni che si sono originati dopo eventi di speciazione, quindi riescono ad esprimere al meglio la storia evolutiva delle specie entro le quali li ritroviamo (a differenza dei geni paraloghi che si generano dopo eventi di duplicazione genica).
Le operazioni che verranno eseguite di seguito, per l'identificazione di geni ortologhi, faranno riferimento alla creazione di Ortogruppi. Gli ortogruppi rappresentano raggruppamenti contenenti unicamente geni ortologhi tra di loro per le specie prese in considerazione, e che differiscono dal concetto di "gruppo di ortologhi" ovvero un raggruppamento di più ortogruppi che probabilmente racchiude geni paraloghi tra di loro. 

Sostanzialmente l'esecuzione di questo passaggio può essere riassunto in alcuni passaggi fondamentali:
1) Gestione e pulizia delle sequenze
2) Inferenza di ortologia
3) Creazione dell'albero delle specie

-----

## Gestione e pulizia delle sequenze
Questa sezione è importatnte da eseguire perchè consente di andare a migliorare le sequenze relative ad ogni specie, consentendo di avere una informazione più chiara e precisa da fornire ai successivi programmi (es. Orthofinder)

#### Isoforma più lunga
Per ogni gene vengono eliminate tutte le isoforme associate ad esso, al di fuori dell'isoforma più lunga.
```bash
#|sequence|
for gff in *.gff; do agat_sp_keep_longest_isoform.pl --gff "$gff"-o ${gff/.gff/_longest.gff}; done
```

Estrazione del CDS dal .gff appna creato (_longest.gff) con successiva traduzione della sequenza in amminoacidi. Quindi viene eseguita l'associazione del file FASTA al file .gff e dopo vengono tradotti i nucleotidi in amminoacidi.
```bash
#|sequence|
for gff in *_longest.gff; do agat_sp_extract_sequences.pl -g "$gff" -f ../00_genome/${gff/_longest.gff/.fna} -t cds -p --cfs --output ../02_Proteome/${gff/_longest.gff/.faa}; done
```
>.fna file contenente una sequenza di nucleotidi

>.faa file contenente una sequenza di amminoacidi

#### Eliminazione degli pseudogeni
Per ciascuna sequenza di ogni specie, venogno eliminati gli pseufogeni, dato che il focus del lavoro è totalmente incentrato sulle sequenze codificanti.
```bash
bash ../../99_scripts/pseudogene_find_eliminate.sh
```

#### Miglioramento della forma
A questo punto si procede con la modifica dell'header per facilitare le operazioni successive e per rendere più leggibile l'output.
```bash
for prot in *.faa; do ID=$(basename -s .faa "$prot"); sed -i.old -E "s/>(rna-XM_[0-9]+\.[0-9]) (gene=gene-.[^ ]+) name=(.[^ ]+) .+$/>${ID}\|\3/" "$prot"; done
```

-----


