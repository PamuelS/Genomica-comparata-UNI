# Genome Assembly
All'interno di questa cartella vengono affrontate tutte le fasi che si susseguono per assemblare un genoma. 

I procedimenti svolti sono stati sintetizzati all'interno di quattro passaggi fondamentali:

- [Assemblaggio contig-level](./00_Assembly_raw): creazione dei contig e del consensus, mediante l'utilizzo delle reads.
  
- [Pulizia](./01_polishing): rimozione di artefatti ed errori accumulatisi nei passaggi precedenti, tramite l'utilizzo delle short e long reads.
  
- [Decontaminazione](./02_contaminants): identificazione e rimozione di sezioni del genoma assemblato associate ad organismi differenti da *A. stephensis*.
  
- [Assembleggio scaffolding-level](./03_Scaffolding): unione dei contig ottenuti in scaffold
