# Scaffolding
Questa fase dell'assemblaggio, prevede la creazione degli scuffold. Uno scaffold non è altro che una rappresentazione virtuale delle sequenze di contig riordinate seguendo criteri, per rispecchiare al meglio la struttura del cromosoma.


### Miglioramento e correzione 
Questo procedimento consente di migliorare ulteriormente la qualità del genoma prima di eseguire lo scaffolding. Viene confrontato e allineato l'assemblaggio ottenuto con un genoma di riferimento, per correggerne eventuali errori.
>Se l'assemblaggio è de novo, il genomadi riferimento scelto apparterrà ad una specie filogeneticamente vicina alla specie che si sta studiando

```bash
ragtag.py correct -t 20 <REFERENCE_GENOME> <DRAFT_GENOME>
```

-----

### Assemblaggio scaffold-level


```bash
ragtag.py scaffold -C -t 20 -o <OUTPUT_DIR> <REFERENCE_GENOME> <CORRECTED_DRAFTGENOME>
```
