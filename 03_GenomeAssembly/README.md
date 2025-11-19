# Genome Assembly
All'interno di questa cartella vengono affrontate tutte le fasi che si susseguono per assemblare un genoma. 
I provedimenti svolti possono essere sintetizzati in alcuni passaggi chiave.

- [Assemblaggio contig-level](): creazione dei contig e del consensus, mediante l'utilizzo delle reads.
  
- [Pulizia](./01_polishing): rimozione di artefatti ed errori accumulatisi nei passaggi precedenti, tramite l'utilizzo delle short e long reads.
  
- [Decontaminazione](./02_contaminants): identificazione e rimozione di sezioni del genoma assemblato associate ad organismi differenti da *A. stephensis*.
  
- [Assembleggio scaffolding-level](): unione dei contig ottenuti in scaffold
