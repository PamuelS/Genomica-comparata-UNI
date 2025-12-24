# Orthology Inference
Questa cartella è dedicata interamente all'analisi e alla ricostruzione delle relazioni tra le varie specie, in riferimento al dataset utilizzato, mediante l'inferenza di ortologia tra i vari geni osservati ed inoltre alla creazione dell'albero delle specie.

Prima di procedere è bene ricordare che la ricostruzione degli eventi evolutivi associati ad un gruppo di specie (come in questo caso) oppure a più taxa, viene tendenzialmente eseguito medianto lo studio dei vari geni ortologhi. Questo perchè i geni ortologhi rappresentano coppie di geni che si sono originati dopo eventi di speciazione, quindi riescono ad esprimere al meglio la storia evolutiva delle specie entro le quali li ritroviamo (a differenza dei geni paraloghi che si generano dopo eventi di duplicazione genica).

Le operazioni che verranno eseguite di seguito, per l'identificazione di geni ortologhi, faranno riferimento alla creazione di Ortogruppi. Gli ortogruppi rappresentano raggruppamenti contenenti unicamente geni ortologhi tra di loro per le specie prese in considerazione, e che differiscono dal concetto di "gruppo di ortologhi" ovvero un raggruppamento di più ortogruppi che probabilmente racchiude geni paraloghi tra di loro. 

In modo sostanziale il compimento dell'inferenza di ortologia e della creazione dell'albero delle specie, può essere riassunto in alcuni passaggi fondamentali:
1) [Inferenza di Ortologia](./OrthoFinder/Results_Dec01/Comparative_Genomics_Statistics)
2) [Eliminazione paraloghi](./01_DISCO)
3) [Identificazione delle sequenze](./02_disco_OG)
4) [Allineamento delle sequenze](./03_aligned)
5) [Trimming e Concatenazione](./04_trimmed)
6) [Inferenza Albero delle Specie](./05_tree)

-----

## Gestione e pulizia delle sequenze
Questo passaggio rappresenta un punto importante nel workflow perchè è utile per il miglioramento delle sequenze relative ad ogni specie, consentendo di avere una informazione più chiara e precisa da fornire ai successivi programmi (es. Orthofinder)

### Isoforma più lunga
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

### Eliminazione degli pseudogeni
Per ciascuna sequenza di ogni specie, venogno eliminati gli pseufogeni, dato che il focus del lavoro è totalmente incentrato sulle sequenze codificanti.

```bash
bash ../../99_scripts/pseudogene_find_eliminate.sh
```

### Miglioramento della forma
A questo punto si procede con la modifica dell'header per facilitare le operazioni successive e per rendere più leggibile l'output.

```bash
for prot in *.faa; do ID=$(basename -s .faa "$prot"); sed -i.old -E "s/>(rna-XM_[0-9]+\.[0-9]) (gene=gene-.[^ ]+) name=(.[^ ]+) .+$/>${ID}\|\3/" "$prot"; done
```

-----

## Inferenza di Ortologia
A questo punto si può procedere con la creazione dei potenziali ortogruppi identificati dal programma Orthofinder. La ricerca e l'identificazione dei geni ortologhi parte proprio dall'imput dei proteomi "puliti".

### Identificazione di ortogruppi
Con questo comando si esegui il primo passaggio per la ricostruzione dei rapporti di ortologia tra i vari geni delle specie del dataset. Viene restituita una serie di informazioni riguardanti l'ortologia, l'albero dei geni e l'albero delle soecie.
```bash
orthofinder -t 8 -a 8 -f .
```
>Il programma Orthofinder restituisce come output molteplici cartelle contenenti varie informazioni sui dati ottenuti

### Eliminazione di Geni Paraloghi
Pulizia del file Resolved_Gene_Tree.txt (contiene il nome dell'ortogruppo e l'albero associato) dalla presenza di eventuali geni paraloghi oppure duplicazioni, mediante l'utilizzo di "disco.py". Inoltre grazie al comando "sed" vengono modificati glie header delle specie per poter renderli accessibili da disco.
>Separazione di geni paraloghi da quelli ortologhi trovati con Orthofinder
```bash
while IFS=' ' read -r OG tree; do python3 /home/STUDENTI/samuel.pederzini/ Genomica-comparata-UNI/99_scripts/disco.py -i <(echo "$tree") -o ../../../01_DISCO/${OG/:/}.nwk -d "|" -m 4 --remove_in_paralogs --keep-labels --verbose >> ../../../01_DISCO/disco.log; done < <(sed -E 's/[A-Z][a-z]{5}_//g; s/\)n[0-9]*+/\)/g' Resolved_Gene_Trees.txt)
```
>.nwk file contenente l'albero filogenetico


### Eliminazione di alberi vuoti
Rimozione di possibili "alberi" vuoti creatisi in precedenza.

```bash
find . -size 0 -print > empty_disco.txt
find . -size 0 -delete
```

### Creazione dei singoli ortogruppi
Lo script utilizzato serve per estrarre dal file di disco, tutti gli alberi filogenetici e li inserisce all'interno di singoli file, associandoci anche la corrsipettiva sequenza amminoacidica.

```bash
bash ../../99_scripts/split_disco_output.sh /home/STUDENTI/samuel.pederzini/Genomica-comparata-UNI/05_OG.Inference_Phylogenomic/OrthoFinder/Results_Dec01/Orthogroup_Sequences
```

-----

## Creazione dell'Albero delle specie
Per proseguire con la costruzione dell'albero filogenetico, è necessario prima eseguire alcuni procedimenti per poter garantire una migliore inferenza dell'albero. Questi passaggi corrispondono alla fasi di allineamento e di successivo trimming.

### Creazione del file dell'albero
Vengono selezionati i file che verranno usati per l'inferenza sull'albero delle specie. Questi file vengono estratti proprio dalla cartella "Single_Copy_Orthologue_Sequences" creatasi dopo il lancio di Orthofinder.
```bash
ls *.fa | shuf -n 200 > species_tree_OG.txt
```
>Per questo esempio sono stati scelti in maniera random soltanto 200 file dei vari ortogruppi

### Allineamento delle sequenze
Allineamento delle sequenze di ogni ortogruppo, per poter osservare il quantitativo di variazioni a livello amminoacidico di ogni gene rispetto agli geni appartenenti al medesimo ortogruppo.
```bash
#|sequence|
for OG in $(cat species_tree_OG.txt); do mafft --auto --anysymbol "$OG" > ../../../03_aligned/${OG/.fa/_aligned.faa}; done
```
> for OG in ../../02_disco_OG/*.faa; do mafft --auto --anysymbol "$OG" > ./${OG/.faa/_aligned.faa}; done
> Lo stesso comando di allienamentoe di trimming è stato eseguito per tutti i file degli ortogruppi all'intero della cartella "02_disco_OG" (ottenuto nel passaggio ### Creazione dei singoli ortogruppi)

### Trimming delle sequenze
La fase di trimming può essere eseguita solamente dopo aver compiuto l'allineamento. Ha lo scopo di eliminare e pulire le sequenze che risultano sbaglliate dopo l'allineamento.
```bash
conda deactivate 
for OG in *; do bmge -i "$OG" -t AA -m BLOSUM62 -e 0.5 -g 0.4 -of ../04_trimmed/${OG/_aligned.faa/_trimmed.faa}; done
```
>Per lanciare questo comando bisogna essere all'esterno di qualsiasi environment

### Concatenazione
A questo punto vengono unite tutte le sequenze (già allineate e trimmate), ritrovate in ogni singolo ortogruppo, in quello che rappresenta un grande ed unico allineamento. Questo viene eseguito dal comando AMAS.py
```bash
../../../99_scripts/AMAS.py concat -y nexus -i *.faa -f fasta -d aa -t conc_species_tree
```

### Costruzione Albero delle Specie
Ora si posseggono tutti gli input per poter avviare l'analisi filogenetica per la costruzione dell'albero delle specie. In questo caso è stato usato il programma iqtree, che andrà ad utilizzare come dati proprio le sequenze derivanti dal concatenamento.
```bash
#|tree|
iqtree -m TESTNEW -b 100 -s conc_species_tree —prefix species_tree -nt 9
```













