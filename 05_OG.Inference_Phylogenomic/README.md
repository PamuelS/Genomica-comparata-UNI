# Orthology Inference
Questa cartella è dedicata interamente all'analisi e alla ricostruzione delle relazioni tra le varie specie, in riferimento al dataset utilizzato, mediante l'inferenza di ortologia tra i vari geni osservati ed inoltre alla creazione dell'albero delle specie.
Prima di procedere è bene ricordare che la ricostruzione degli eventi evolutivi associati ad un gruppo di specie (come in questo caso) oppure a più taxa, viene tendenzialmente eseguito medianto lo studio dei vari geni ortologhi. Questo perchè i geni ortologhi rappresentano coppie di geni che si sono originati dopo eventi di speciazione, quindi riescono ad esprimere al meglio la storia evolutiva delle specie entro le quali li ritroviamo (a differenza dei geni paraloghi che si generano dopo eventi di duplicazione genica).
Le operazioni che verranno eseguite di seguito, per l'identificazione di geni ortologhi, faranno riferimento alla creazione di Ortogruppi. Gli ortogruppi rappresentano raggruppamenti contenenti unicamente geni ortologhi tra di loro per le specie prese in considerazione, e che differiscono dal concetto di "gruppo di ortologhi" ovvero un raggruppamento di più ortogruppi che probabilmente racchiude geni paraloghi tra di loro. 

Sostanzialmente l'esecuzione di questo passaggio può essere riassunto in alcuni passaggi fondamentali:
1) Gestione e pulizia delle sequenze
2) Inferenza di ortologia
3) Creazione dell'albero delle specie

