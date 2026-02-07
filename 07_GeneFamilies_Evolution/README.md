# Gene Families Evolution 
Una volta ottenuto il file dell'albero ultametrico, si può procedere con l'analisi dell'esspansione e della contrazione delle famiglie geniche. 

Per approfondire, una espansione si verifica quando una specie possiede più copie di geni appartenenti ad una determinata famiglia genica rispetto all'antenato. Mentre si parla di contrazione in tutte quelle situazioni diametralmente opposte, dove una specie presenta una riduzione nel numero di copie di geni associati ad una determinata famiglia genica rispetto all'antenato.

L'utilità associata allo studio delle modificazioni intraprese da una famiglia genica nel corso della sua evoluzione, risiede proprio nelle informazioni che essa trasmette. Infatti osservare la contrazione o l'espansione di una data famiglia può fornire risposte su eventuali adattamenti evolutivi intrapresi da una specie.

Lo studio delle modificazioni delle famigli geniche lungo la storia evolutiva dei vari taxa viene eseguita grazie all'applicazione di un modello *Birth and Death* che viene associato alle varie famiglie geniche analizzate.

-----

## Preparazione all'analisi 
Per poter utilizzare il programma CAFE5 è necessario predisporre alla lettura un file nello specifico, che è stato denominato [Gene Count CAFE](./GeneCount_CAFE.tsv) ed è stato ottenuto direttamente dall'output di [Orthofinder](../05_OG.Inference_Phylogenomic/OrthoFinder). In questo file ritroviamo tutti gli ortogruppi e le divisioni dei geni (di quell'ortogruppo) per ogni specie.

```bash
sed $'s/^/NONE\t/g' Orthogroups.GeneCount.tsv | rev Orthogroups.GeneCount.tsv  | cut -f 2- | rev > GeneCount_CAFE.tsv 
```

## Creazione dell'Error model
A questo punto per rendere l'analisi di CAFE5 il più veritiero possibile, si calcola un Error Model. Questo Error Model rappresenta un modello stratistico che descrive la probabilità che il nuemro di geni osservati in una famiglia genica sia diverso dal numero reale a causa di errori tecnici. La statistica restituita dal modello verrà poi implementata nei passaggi successivi.

```bash
cafe5 -i GeneCount_CAFE.tsv -t timetree.nwk -o Error_model -e
```

## Analisi di CAFE5
[CAFE5](https://github.com/hahnlab/CAFE5) è il programma che è stato utilizzare per l'inferenza dell'espansione e copntrazione delle famiglie geniche nelle varie specie prese in considerazione.

Questo programma utilizza alcuni parametri nello specifico per copiere l'analisi come ad esempio:
- Lambda (λ) --> rappresenta il tasso evolutivo di Birth/Death nelle varie specie. Il valore di Lambda viene associato ad ogni specie e valori diversi di Lambda implicano tassi evolutivi differenziati tra le specie osservate.
- Gamma (γ) --> descrive la variabilità del valore Lambda all'interno delle varie famiglie geniche. Molteplici valori di gamma implicano che non tutte le famiglie evolvono allo stesso ritmo.

Per compiere l'analisi, CAFE5 necessita anche dell'[albero ultrametrico in formato .nkw](./timetree.nkw) ottenuto tramite l'esportazione del file [time_tree.timetree.nex](../06_DivergenceTime_Estimation/time_tree.timetree.nex) in formato .nkw mediante il sito [iTol](https://itol.embl.de).

### Utilizzo di una Lambda
È stata eseguita prima una analisi CAFE5 impostando un singolo valore di Lambda per ogni singola specie, presupponendo che il Turnover (tendenza di una famiglia genica di espandersi o contrarsi) rimanga invariato per tutte le specie.

```bash
for k in {1..5}; do for n in {1..10}; do mkdir -p 00_1L/${k}K/${n}N; cafe5 -i GeneCount_ -t timetree.nwk -o 00_1L/${k}K/${n}N -e./Error_model/Base_error_model.txt -k ${k}; done; done
```

### Utilizzo di due Lambda
Successivamente è stata compiuta una seconda analisi di CAFE5 dove sono stati selezionati due valori di Lambda. Come è possibile osservare dal file [timetree_2Lambda.nkw](./timetree_2Lambda.nkw]) il valore 2 è stato associato alle due specie di Culicidi che presentano una alimentazione eterogenea rispetto alle altre specie, presupponendo uno tasso evolutivo contraddistinto (vedi [tabella specie](../00_data)). 

```bash
for k in {1..5}; do for n in {1..10}; do mkdir -p 00_2L/${k}K/${n}N; cafe5 -i GeneCount_ -t timetree.nwk -o 00_2L/${k}K/${n}N -y timetree_2Lambda.nwk -e./Error_model/Base_error_model.txt -k ${k}; done; done
```

## AIC / BIC
AIC e BIC sono due indici statistici che vengono fondamentalmente utilizzati per capire quale, tra i vari modelli statistici utilizzati, rappresenta il più adatto. Viene assegnato un valore ad ogni modello utilizzato, con l'obiettivo di raggiungere un modello preciso ma allo stesso tempo il più semplice possibile, andando a penalizzare molto spesso la complessità di un modello.
Ciascuno dei due indici statistici presenta formule distinte:
- AIC (Akaike Information Criterion)     ---> AIC=2k−2ln(L^)
- BIC (Bayesian Information Criterion)   ---> BIC=kln(n)−2ln(L^)

### Valore migliore nei replicati tecnici
Inizialmente si è cercato quale fosse il valore migliore di likelihood all'interno dei dieci replicati tecnici, ripetendo questo procedimento per ogni valore di Gamma presente nelle analisi ad una e a due Lambda.

```bash
for folder in */; do lnL=$(grep "lnL" ${folder}/Base_results.txt | grep -oE "[0-9]*([\.,][0-9]*)?"); L=$(grep "Lambda" ${folder}/Base_results.txt | grep -oE "[0-9]*\.[0-9]*"); E=$(grep "Epsilon" ${folder}/Base_results.txt | grep -oE "[0-9]*\.[0-9]*"); echo -e "$lnL\t$L\t$E" >> sum_results.tsv; done
```
>Quando il valore di Gamma è superiore ad uno, si deve sostituire **Base_** con **Gamma_** all'interno del codice riportato qui sopra.

### Valore migliore tra le Gamma
Una volta eseguito questo procedimento per tutte le cartelle con dentro i replicati tecnici associati a ciascuna 
Gamma, si puù procedere con la scelta del miglior valore di likelihood associato ad ogni Gamma.

```bash
for f in */; do cut -f1 "$f"/sum_results.tsv | sort -n | head -n1; done > all_L.txt
```
Il procedimento deve essere eseguito per ciascuna delle due cartelle di Lambda usate nello studio
>Ricordarsi che per ogni valore di Gamma bisogna selezionare soltanto il replicato tecnico che presenta il valore di likelihood più basso, dal quale poi si prosegue con il codice soprastante

### Calcolo degli indici AIC e BIC
A questo punto si procede con il calcolo degli indici statistici, per decretare quale tra i due modelli (una singola lambda oppure due) sia il miglior modello da applicare ai nostri dati.

```bash
paste --delimiters=$"\t" all_L.txt <(while IFS=$'\t' read -r L k; do echo "2*$k + 2*$L" | bc; done < all_L.txt) <(while IFS=$'\t' read -r L k; do echo "$k*9.23 + 2*$L" | bc; done < all_L.txt) | sort -k4,4n > AIC_BIC.tsv
```
>N.B. che il valore di 9.23 corrisponde al logaritmo naturale del totale degli alberi ritrovarti all'interno delle analisi di CAFE5
