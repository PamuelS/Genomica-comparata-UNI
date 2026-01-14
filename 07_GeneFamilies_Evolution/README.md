# Gene Families Evolution 
Una volta ottenuto il file dell'albero ultametrico, si può procedere con l'analisi dell'esspansione e della contrazione delle famiglie geniche. 

Per approfondire, una espansione si verifica quando una specie possiede più copie di geni appartenenti ad una determinata famiglia genica rispetto all'antenato. Mentre si parla di contrazione in tutte quelle situazioni diametralmente opposte, dove una specie presenta una riduzione nel numero di copie di geni associati ad una determinata famiglia genica rispetto all'antenato.

L'utilità associata allo studio delle modificazioni intraprese da una famiglia genica nel corso della sua evoluzione, risiede proprio nelle informazioni che essa trasmette. Infatti osservare la contrazione o l'espansione di una data famiglia può fornire risposte su eventuali adattamenti evolutivi intrapresi da una specie.

Lo studio delle modificazioni delle famigli geniche lungo la storia evolutiva dei vari taxa viene eseguita grazie all'applicazione di un modello *Birth and Death* che viene associato alle varie famiglie geniche analizzate.

-----

## Preparazione all'analisi 
Per poter utilizzare il programma CAFE5 è necessario predisporre alla lettura un file nello specifico, che è stato denominato [Gene Count CAFE](./GeneCount_CAFE.tsv) ed è stato ottenuto direttamente dall'output di [Orthofinder](../05_OG.Inference_Phylogenomic/OrthoFinder). In questo file ritroviamo tutti gli ortogruppi e le divisioni dei geni (di quell'ortogruppo) per ogni specie.

```bash
sed $'s/^/NONE\t/g' Orthogroups.GeneCount.tsv | rev Orthogroups.GeneCount.tsv  | cut -f 2- | rev > GeneCount_CAFE.tsv 
```

## Creazione dell'Error model
A questo punto per rendere l'analisi di CAFE5 il più veritiero possibile, si calcola un Error Model. Questo Error Model rappresenta un modello stratistico che descrive la probabilità che il nuemro di geni osservati in una famiglia genica sia diverso dal numero reale a causa di errori tecnici. La statistica restituita dal modello verrà poi implementata nei passaggi successivi.

```bash
cafe5 -i GeneCount_CAFE.tsv -t timetree.nwk -o Error_model -e
```

## Analisi di CAFE5
CAFE5 è il programma che è stato utilizzare per l'inferenza dell'espansione e copntrazione delle famiglie geniche nelle varie specie prese in considerazione.

Questo programma utilizza alcuni parametri nello specifico per copiere l'analisi come ad esempio:
- Lambda (λ) --> rappresenta il tasso evolutivo di Birth/Death nelle varie specie. Il valore di Lambda viene associato ad ogni specie e valori diversi di Lambda implicano tassi evolutivi differenziati tra le specie osservate.
- Gamma (γ) --> descrive la variabilità del valore Lambda all'interno delle varie famiglie geniche. Molteplici valori di gamma implicano che non tutte le famiglie evolvono allo stesso ritmo.

Per compiere l'analisi, CAFE5 necessita anche dell'[albero ultrametrico in formato .nkw](./timetree.nkw) ottenuto tramite l'esportazione del file [time_tree.timetree.nex](../06_DivergenceTime_Estimation/time_tree.timetree.nex) in formato .nkw mediante il sito [iTol](https://itol.embl.de).

### Utilizzo di una Lambda 
```bash
for k in {1..5}; do for n in {1..10}; do mkdir -p 00_1L/${k}K/${n}N; cafe5 -i GeneCount_ -t timetree.nwk -o 00_1L/${k}K/${n}N -e./Error_model/Base_error_model.txt -k ${k}; done; done
```

### Utilizzo di due Lambda

```bash
for k in {1..5}; do for n in {1..10}; do mkdir -p 00_2L/${k}K/${n}N; cafe5 -i GeneCount_ -t timetree.nwk -o 00_2L/${k}K/${n}N -y timetree_2Lambda.nwk -e./Error_model/Base_error_model.txt -k ${k}; done; done
```
