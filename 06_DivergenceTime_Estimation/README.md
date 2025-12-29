# Divergence Time Estimation
Questa cartella contiene tutti passaggi che consentono di datare l'albero delle specie ottenuto nella [cartella precedente](../05_OG.Inference_Phylogenomic) al fine di ottenere un albero ultrametrico.
Per riuscire a calibrare un albero filogenetico sono necessarie delle informazioni a priori che consentano di stimare temporalmente la divergenza tra i vari taxa all'interno dell'albero delle specie. Questa informazione è stata ottenuta tramite il caricamento del file dell'albero delle specie sulla piattaforma di [Timetree.org](https://timetree.org). Con l'acquisizione di questi dati è stato creato un file di "calibrazione" con le coordinate temporali dei nodi e delle specie legate a quel nodo.
Infine è stata eseguita l'analisi per la ricerca dell'albero tramite il metodo di Maximum Likelyhood, piuttosto che eseguire una analisi con il metodo dell'inferenza Bayesiana, dato che l'ottenimento dell'albero ultramtrico è necessario solo per l'osservazione dell'evoluzione delle famiglie geniche 

-----

## File di Calibrazione 
Creazione del [file di calibrazione](./calibration.txt) partendo dalle informazioni ottenute da Timetree.org.

Essendo che molti nodi non potevano essere utilizzati come datazioni effettiva (essendo che quasi la metà delle specie prese in considerazione per lo studio presentavano un asterico all'interno della piattaforma), è stato scelto un nodo rappresentato da una tricotomia.
>nella tricotomia sono state scelte tutte le specie che divergevano da essa

## Ottenimento albero ultrametrico 
L'albero calibrato è stato ottenuto tramite il programma iqtree 

```bash
#|tree|
iqtree -s ../05_OG.Inference_Phylogenomic/05_tree/conc_species_tree--date calibration.txt --date-tip 0 -o Anoste -m Q.INSECT+F+I+R3 -nt 13 --prefix time_tree --date-options "-u 1"
```
