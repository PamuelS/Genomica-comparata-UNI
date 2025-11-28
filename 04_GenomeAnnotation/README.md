# Genome Annotation 
L'annotazione rappresenta la fase che segue l'assemblaggio di un genoma, ed è il momento in cui vengono identificate e descritte tutte le funzioni della sequenza genomica ottenuta, come ad esempio: sequenze codificanti, pseudogeni, trasposoni, ncRNA, tRNA, ecc. Quindi quello che viene compiuto è l'associazione di una funzione bilogica a quello che per il momento rappresenta solamente una sequenza anonima di nucleotidi, associando una posizione alla funzione descritta.
Per l'annotazione vengono utilizzati degli strumenti, come SNAP e Augustus, ovvero dei software addestrati per associare le regioni codificanti a geni già noti (predizione genica).
Questo passaggio solitamente viene eseguito a round, dato che ogni round successivo consente agli algoritmi di migliorare progressivamente la predittività, migliorando di conseguenza l'identificazione dei geni.

### Annotazione di elementi ripetuti
In questo progetto il focus è improntato unicamente sulle regioni codificanti del genoma, quindi risulta necessario andare prima ad identificare, per poi successivamente nascondere, tutti quegli elementi classificati come "ripetitive elements" perchè presentano delle strutture differenti dalle regioni codificanti.
>Per gli elementi ripetuti si utilizzano programmi come RepeatModeler oppure RepeatMasker.

-----

### Generazione dell'annotazione
MAKER è il programma adoperato per eseguire l'annotazione. Per poter essere utilizzato necessita di alcuni file di controllo/opzione che vengono generati con il comando sottostante.


```bash
maker -CTL
```

-----

### Miglioramento del file .gff


```bash

```

-----

### Predizione genica


```bash

```

-----

### Round ulteriori


```bash

```

-----




