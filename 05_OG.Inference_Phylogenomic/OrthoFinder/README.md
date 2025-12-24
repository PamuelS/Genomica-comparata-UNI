# Inferenza di Ortologia
Una volta impostate tutte le sequenze secondo i parametri usati in precedenza, si può procedere con l'inferenza di ortologia, eseguita interamente dal programma [Orthofinder](https://github.com/davidemms/OrthoFinder).
La ricerca e l'identificazione dei geni ortologhi parte proprio dall'imput dei proteomi "puliti".

-----

## Identificazione di ortogruppi
Con questo comando si esegui il primo passaggio per la ricostruzione dei rapporti di ortologia tra i vari geni delle specie del dataset. Viene restituita una serie di informazioni riguardanti l'ortologia, l'albero dei geni e l'albero delle soecie.
```bash
orthofinder -t 8 -a 8 -f .
```
>Il programma Orthofinder restituisce come output molteplici cartelle contenenti varie informazioni sui dati ottenuti
