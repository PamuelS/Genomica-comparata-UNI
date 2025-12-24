# Associazione delle sequenze 
Quando sono stati ottenuti tutti gli ortogruppi ripuliti dai geni paraloghi, si può passare alla fase successiva, ovvero la ricostruzione delle sequenze amminoacidiche associate a ciascun gene presente nei vari ortogruppi.
A questo punto si procede con la divisione dei singoli file degli alberi filogenetici (.nwk) di ogni ortogruppo in file di sequenze amminoacidiche (.faa).

-----

## Creazione dei singoli ortogruppi
Questo passaggio è stato eseguito mediante il lancio di uno script (split_disco_output.sh), che serve per estrarre dal file di DISCO, tutti gli alberi filogenetici e li inserisce all'interno di singoli file, associandoci anche la corrsipettiva sequenza amminoacidica estratta ditrettamente dalla cartella di Orthofinder.

```bash
bash ../../99_scripts/split_disco_output.sh /home/STUDENTI/samuel.pederzini/Genomica-comparata-UNI/05_OG.Inference_Phylogenomic/OrthoFinder/Results_Dec01/Orthogroup_Sequences
```


