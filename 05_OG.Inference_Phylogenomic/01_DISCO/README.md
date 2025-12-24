# Eliminazione dei Paraloghi
È estremamente probabile che il risultato di Orthofinder abbia restituito dei raggruppamenti di geni entro i quali possiamo riconoscere dei geni paraloghi. Questi geni devono essere eliminati perchè altrimenti rischiano di falsare i risultati dell'analisi filogenomica che verrà svolta nei prossimi passaggi.

-----

## Esecuzione di DISCO
Pulizia del file Resolved_Gene_Tree.txt (contiene il nome dell'ortogruppo e l'albero associato) dalla presenza di eventuali geni paraloghi (ovvero duplicazioni), mediante l'utilizzo di "disco.py". Inoltre grazie al comando "sed" vengono modificati gli header delle specie per poter renderli accessibili da disco.
>Separazione di geni paraloghi da quelli ortologhi trovati con Orthofinder

```bash
while IFS=' ' read -r OG tree; do python3 /home/STUDENTI/samuel.pederzini/ Genomica-comparata-UNI/99_scripts/disco.py -i <(echo "$tree") -o ../../../01_DISCO/${OG/:/}.nwk -d "|" -m 4 --remove_in_paralogs --keep-labels --verbose >> ../../../01_DISCO/disco.log; done < <(sed -E 's/[A-Z][a-z]{5}_//g; s/\)n[0-9]*+/\)/g' Resolved_Gene_Trees.txt)
```
>.nwk file contenente l'albero filogenetico


## Eliminazione di alberi vuoti
Rimozione di possibili "alberi" vuoti creatisi in precedenza.

```bash
find . -size 0 -print > empty_disco.txt
find . -size 0 -delete
```

