# Allineamento delle sequenze
Per proseguire con la costruzione dell'albero filogenetico, è necessario prima eseguire alcuni procedimenti per poter garantire una migliore inferenza dell'albero. Questi passaggi corrispondono alla fasi di allineamento e di successivo trimming.

## Creazione del file dell'albero
Vengono selezionati i file che verranno usati per l'inferenza sull'albero delle specie. Questi file vengono estratti proprio dalla cartella "Single_Copy_Orthologue_Sequences" creatasi dopo il lancio di Orthofinder.
```bash
ls *.fa | shuf -n 200 > species_tree_OG.txt
```
>Per questo esempio sono stati scelti in maniera random soltanto 200 file dei vari ortogruppi

## Allineamento delle sequenze
Allineamento delle sequenze di ogni ortogruppo, per poter osservare il quantitativo di variazioni a livello amminoacidico di ogni gene rispetto agli geni appartenenti al medesimo ortogruppo.
```bash
#|sequence|
for OG in $(cat species_tree_OG.txt); do mafft --auto --anysymbol "$OG" > ../../../03_aligned/${OG/.fa/_aligned.faa}; done
```
> for OG in ../../02_disco_OG/*.faa; do mafft --auto --anysymbol "$OG" > ./${OG/.faa/_aligned.faa}; done
> Lo stesso comando di allienamentoe di trimming è stato eseguito per tutti i file degli ortogruppi all'intero della cartella "02_disco_OG" (ottenuto nel passaggio ### Creazione dei singoli ortogruppi)

