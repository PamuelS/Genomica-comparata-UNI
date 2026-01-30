# Gene Annotation and Functional Enrichment
Una volta eseguita l'analisi di CAFE5, si può proseguire con: annotazione delle sequenze e arricchimento funzionale.

Per annotazione si intende l'associazione di una determinata funzione ad una sequenza oppure, come in questo specifico caso, ad un intero ortogruppo. Questo procedimento utilizza delle targhette standardizzate che vengono definite GO (Gene Onthology) che descrivono ruoli biologici ben specifici. 

I ruoli biologici vengono suddivisi in tre categorie diverse:
- Molecular Function (MF) cosa fa la proteina a livello molecolare
- Biological Process (BP) in quale processo biologico la proteina è coinvolta
- Cellular Components (CC) dove è situata la proteina nella cellula

Durante l'annotazione si fa sempre riferimento ad una banca dati dalla quale estrarre le informazioni relative alla sequenza che si sta osservando, perciò il ruolo della proteina viene dedotto per confronto con sequenze già caratterizzate.

In seguito alla annotazione, si esegue l'arricchimento funzionale che consente di convertire l'informazione associata ai GO terms in una interpretazione biologica. Questo viene eseguito tramite due informazioni fondamentali, ovvero: [GO Backrounds](./go_back.tsv) (che non è altro che l'annotazione delle sequenze di tutti gli ortogruppi ottenuti dai passaggi precedenti) e gli [ortogruppi di interesse](./ortogruppi_nettare_importante)(cioè gli ortogruppi che ho scelto personalmente perchè presentano una contrazione/espansione significativa nelle tip che mi interessano). ==Una determinata funzione è definibile come arricchita quando compare molto più spesso nell'ortogruppo che sto studiando, rispetto al background.==




