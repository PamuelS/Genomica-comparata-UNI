# Costruzione Albero delle Specie
Ora si posseggono tutti gli input per poter avviare l'analisi filogenetica per la costruzione dell'albero delle specie. In questo caso è stato usato il programma iqtree, che andrà ad utilizzare come dati proprio le sequenze derivanti dal concatenamento.

```bash
#|tree|
iqtree -m TESTNEW -b 100 -s conc_species_tree —prefix species_tree -nt 9
```



