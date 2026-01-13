# Gene Families Evolution 
Una volta ottenuto il file dell'albero ultametrico, si può procedere con l'analisi dell'esspansione e della contrazione delle famiglie geniche. 

Per approfondire, una espansione si verifica quando una specie possiede più copie di geni appartenenti ad una determinata famiglia genica rispetto all'antenato. Mentre si parla di contrazione in tutte quelle situazioni diametralmente opposte, dove una specie presenta una riduzione nel numero di copie di geni associati ad una determinata famiglia genica rispetto all'antenato.

L'utilità associata allo studio delle modificazioni intraprese da una famiglia genica nel corso della sua evoluzione, risiede proprio nelle informazioni che essa trasmette. Infatti osservare la contrazione o l'espansione di una data famiglia può fornire risposte su eventuali adattamenti evolutivi intrapresi da una specie.

Lo studio delle modificazioni delle famigli geniche lungo la storia evolutiva dei vari taxa viene eseguita grazie all'applicazione di un modello *Birth and Death* che viene associato alle varie famiglie geniche analizzate.

-----

## Preparazione all'analisi 
Per poter utilizzare il programma CAFE5 è necessario predisporre alla lettura un file
```bash
sed $'s/^/NONE\t/g' Orthogroups.GeneCount.tsv | rev Orthogroups.GeneCount.tsv  | cut -f 2- | rev > GeneCount_CAFE.tsv 
```

## Creazione dell'Error model
```bash
cafe5 -i GeneCount_CAFE.tsv -t timetree.nwk -o Error_model -e
```

## Analisi di CAFE5 a una 1 lambda
```bash
for k in {1..5}; do for n in {1..10}; do mkdir -p 00_1L/${k}K/${n}N; cafe5 -i GeneCount_ -t timetree.nwk -o 00_1L/${k}K/${n}N -e./Error_model/Base_error_model.txt -k ${k}; done; done
```

## Analisi di CAFE5 a 2 lambda

```bash
for k in {1..5}; do for n in {1..10}; do mkdir -p 00_2L/${k}K/${n}N; cafe5 -i GeneCount_ -t timetree.nwk -o 00_2L/${k}K/${n}N -y timetree_2Lambda.nwk -e./Error_model/Base_error_model.txt -k ${k}; done; done
```
