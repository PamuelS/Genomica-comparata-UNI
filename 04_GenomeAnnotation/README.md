# Genome Annotation 
L'annotazione rappresenta la fase che segue l'assemblaggio di un genoma, ed è il momento in cui vengono identificate e descritte tutte le funzioni della sequenza genomica ottenuta, come ad esempio: sequenze codificanti, pseudogeni, trasposoni, ncRNA, tRNA, ecc. 
Quello che viene eseguito nell'effettivo è l'associazione di una funzione bilogica a quello che per il momento rappresenta solamente una sequenza anonima di nucleotidi, associando una posizione alla funzione descritta.
Per l'annotazione vengono utilizzati degli strumenti, come SNAP e AUGUSTUS, ovvero dei software addestrati per associare le regioni codificanti a geni già noti (predizione genica).

Questo passaggio solitamente viene eseguito a round, dato che ogni round successivo consente agli algoritmi di migliorare progressivamente la predittività, migliorando di conseguenza l'identificazione dei geni.

### Annotazione di elementi ripetuti
In questo progetto il focus è improntato unicamente sulle regioni codificanti del genoma, quindi risulta necessario andare prima ad identificare, per poi successivamente nascondere, tutti quegli elementi classificati come "ripetitive elements", dato che presentano delle strutture differenti dalle regioni codificanti.
>Per gli elementi ripetuti si utilizzano programmi come RepeatModeler oppure RepeatMasker.

-----

### Generazione dell'annotazione
MAKER è il programma adoperato per eseguire l'annotazione. Per poter essere utilizzato necessita di alcuni file di controllo/opzione che vengono generati con il comando sottostante.

```bash
maker -CTL
```

Una volta generati i file che consentono a MAKER di lavorare, si procede con la creazione del file .gff, contenente le informazioni relative all'annotazione dei geni. Per la annotazione sono stati forniti al programma informazioni sul proteoma della specie *A. stephensis*.

```bash
    #-----Genome (these are always required)
    genome= <GENOME> #genome sequence (fasta file or fasta embeded in GFF3 file)
    organism_type=eukaryotic #eukaryotic or prokaryotic. Default is eukaryotic
    
    #-----Re-annotation Using MAKER Derived GFF3
    maker_gff= #MAKER derived GFF3 file
    est_pass=0 #use ESTs in maker_gff: 1 = yes, 0 = no
    altest_pass=0 #use alternate organism ESTs in maker_gff: 1 = yes, 0 = no
    protein_pass=0 #use protein alignments in maker_gff: 1 = yes, 0 = no
    rm_pass=0 #use repeats in maker_gff: 1 = yes, 0 = no
    model_pass=0 #use gene models in maker_gff: 1 = yes, 0 = no
    pred_pass=0 #use ab-initio predictions in maker_gff: 1 = yes, 0 = no
    other_pass=0 #passthrough anyything else in maker_gff: 1 = yes, 0 = no
    
    #-----EST Evidence (for best results provide a file for at least one)
    est= #set of ESTs or assembled mRNA-seq in fasta format
    altest= #EST/cDNA sequence file in fasta format from an alternate organism
    est_gff= #aligned ESTs or mRNA-seq from an external GFF3 file
    altest_gff= #aligned ESTs from a closly relate species in GFF3 format
    
    #-----Protein Homology Evidence (for best results provide a file for at least one)
    protein= <PROTEOME> #protein sequence file in fasta format (i.e. from mutiple oransisms)
    protein_gff=  #aligned protein homology evidence from an external GFF3 file
    
    #-----Repeat Masking (leave values blank to skip repeat masking)
    model_org=<EMPTY> #select a model organism for RepBase masking in RepeatMasker
    rmlib= <RepeatModeler_library> #provide an organism specific repeat library in fasta format for RepeatMasker
    repeat_protein= #provide a fasta file of transposable element proteins for RepeatRunner
    rm_gff= #pre-identified repeat elements from an external GFF3 file
    prok_rm=0 #forces MAKER to repeatmask prokaryotes (no reason to change this), 1 = yes, 0 = no
    softmask=1 #use soft-masking rather than hard-masking in BLAST (i.e. seg and dust filtering)
    
    #-----Gene Prediction
    snaphmm= #SNAP HMM file
    gmhmm= #GeneMark HMM file
    augustus_species= #Augustus gene prediction species model
    fgenesh_par_file= #FGENESH parameter file
    pred_gff= #ab-initio predictions from an external GFF3 file
    model_gff= #annotated gene models from an external GFF3 file (annotation pass-through)
    est2genome=0 #infer gene predictions directly from ESTs, 1 = yes, 0 = no
    protein2genome=<0 OR 1> #infer predictions from protein homology, 1 = yes, 0 = no
    trna=0 #find tRNAs with tRNAscan, 1 = yes, 0 = no
    snoscan_rrna= #rRNA file to have Snoscan find snoRNAs
    unmask=0 #also run ab-initio prediction programs on unmasked sequence, 1 = yes, 0 = no
    
    #-----Other Annotation Feature Types (features MAKER doesn't recognize)
    other_gff= #extra features to pass-through to final MAKER generated GFF3 file
    
    #-----External Application Behavior Options
    alt_peptide=C #amino acid used to replace non-standard amino acids in BLAST databases
    cpus=<CPUS> #max number of cpus to use in BLAST and RepeatMasker (not for MPI, leave 1 when using MPI)
    
    #-----MAKER Behavior Options
    max_dna_len=100000 #length for dividing up contigs into chunks (increases/decreases memory usage)
    min_contig=1 #skip genome contigs below this length (under 10kb are often useless)
    
    pred_flank=200 #flank for extending evidence clusters sent to gene predictors
    pred_stats=<0 or 1> #report AED and QI statistics for all predictions as well as models
    AED_threshold=1 #Maximum Annotation Edit Distance allowed (bound by 0 and 1)
    min_protein=50 #require at least this many amino acids in predicted proteins
    alt_splice=0 #Take extra steps to try and find alternative splicing, 1 = yes, 0 = no
    always_complete=0 #extra steps to force start and stop codons, 1 = yes, 0 = no
    map_forward=0 #map names and attributes forward from old GFF3 genes, 1 = yes, 0 = no
    keep_preds=0 #Concordance threshold to add unsupported gene prediction (bound by 0 and 1)
    
    split_hit=10000 #length for the splitting of hits (expected max intron size for evidence alignments)
    single_exon=0 #consider single exon EST evidence when generating annotations, 1 = yes, 0 = no
    single_length=250 #min length required for single exon ESTs if 'single_exon is enabled'
    correct_est_fusion=0 #limits use of ESTs in annotation to avoid fusion genes
    
    tries=2 #number of times to try a contig if there is a failure for some reason
    clean_try=0 #remove all data from previous run before retrying, 1 = yes, 0 = no
    clean_up=0 #removes theVoid directory with individual analysis files, 1 = yes, 0 = no
    TMP= #specify a directory other than the system default temporary directory for temporary files
```

il comando riportato di seguito serve per riorganizzare la directory di lavoro, eliminando i file intermedi creati dal lancio del comando precedente.

```bash
maker -base <OUTPUT PREFIX>
```

-----

### Ricostruzione del file .gff
Vengono successivamente eseguiti i seguenti comandi per generare il file .gff finale e la sequenza amminoacidica/nucleotidica dei vari geni analizzati. Questo comando risulta importante perchè consente di estrarre le informazioni dai vari file creati da MAKER e di inserirli all'interno di un unico file adoperabile in futuro.
>Il file .gff è un file suddiviso in colonne nelle quali sono riportate le infomrazioni associate a ciascuna sequenza nucleotidica riconosciuta dall'algoritmo come sequenza codificante.

```bash
fasta_merge -d <DATASTORE INDEX FILE>
gff3_merge -d <DATASTORE INDEX FILE>
```

-----

### Predizione genica
Come accenntato in precedenza, il processo di predizione genica è eseguito intermanete da due software altamente specializzati nell'identificazione di sequenze codificanti all'interno di genomi.

I software impiegti sono:
- [SNAP](https://github.com/KorfLab/SNAP.git) (Semi-HMM-based Nucleic Acid Parser) è un programma di ricerca genetica generico adatto sia per genomi eucariotici che procariotici. Questo software può essere allenato utilizzando una serie di modelli genetici noti, in modo tale da migliorare la qualità della annotazione. 
- [AUGUSTUS](https://github.com/Gaius-Augustus/Augustus.git) è un programma di predizione genica che può essere utilizzato come un programma Ab initio.Come per il programma precedente, può essere allenato.
>I programmi Ab initio non sono altro che modelli predittivi che non si basano sulla somiglianza con sequenze di geni noti, ma fanno affidamento unicamente sulla sequenza di DNA e sui modelli statistici dei geni(non si basano sull'evidenza della sequenza).

-----

### Riassunto del procedimento e Ulteriori round
Il procdiemnto di annotazione è un passaggio estremamente lungo, ma che rappresenta uno dei passaggi chiave durante il procedimento di assemllaggio di un genoma.

I passaggi fondamentali possono essere sintetizzati in alcuni punti chiave:
1.

-----

