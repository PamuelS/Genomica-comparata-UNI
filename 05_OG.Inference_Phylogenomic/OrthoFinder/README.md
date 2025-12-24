# Inferenza di Ortologia
A questo punto si può procedere con la creazione dei potenziali ortogruppi identificati dal programma Orthofinder. La ricerca e l'identificazione dei geni ortologhi parte proprio dall'imput dei proteomi "puliti".

## Identificazione di ortogruppi
Con questo comando si esegui il primo passaggio per la ricostruzione dei rapporti di ortologia tra i vari geni delle specie del dataset. Viene restituita una serie di informazioni riguardanti l'ortologia, l'albero dei geni e l'albero delle soecie.
```bash
orthofinder -t 8 -a 8 -f .
```
>Il programma Orthofinder restituisce come output molteplici cartelle contenenti varie informazioni sui dati ottenuti
