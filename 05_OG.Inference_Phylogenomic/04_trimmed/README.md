# Trimming e Concatenamento di sequenze
Dopo l'esecuzione dell'allinemanto delle sequenze si può passare alla fase di Trimming, che consiste nella rimozione di nucleotidi o persino regioni che possono presentare errori, rendendo in questo modo le sequenze più pulite ed informative per l'analisi filogenetica.

Al termine del trimming si procede con il concatenamento delle sequenze allineate e trimmate in un unico file.

-----

## Trimming delle sequenze
La fase di trimming può essere eseguita solamente dopo aver compiuto l'allineamento. 
Ha lo scopo di eliminare e pulire le sequenze che risultano sbaglliate dopo l'allineamento.

```bash
conda deactivate 
for OG in *; do bmge -i "$OG" -t AA -m BLOSUM62 -e 0.5 -g 0.4 -of ../04_trimmed/${OG/_aligned.faa/_trimmed.faa}; done
```
>Per lanciare questo comando bisogna essere all'esterno di qualsiasi environment

## Concatenamento
A questo punto vengono unite tutte le sequenze (già allineate e trimmate), ritrovate in ogni singolo ortogruppo, in quello che rappresenta un grande ed unico allineamento. 
Questo viene eseguito dal comando AMAS.py

```bash
../../../99_scripts/AMAS.py concat -y nexus -i *.faa -f fasta -d aa -t conc_species_tree
```


