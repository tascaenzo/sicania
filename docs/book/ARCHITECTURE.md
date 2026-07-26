# Architettura di Sicania

## Scopo

Questo documento è la mappa evolutiva dell'architettura di Sicania.

Non è una specifica separata dal libro e non sostituisce i capitoli. Riassume i vincoli, i principi, i componenti e le relazioni che devono restare coerenti mentre il sistema cresce.

La trattazione autorevole di ogni sottosistema vive nel capitolo che ne introduce il problema, confronta le alternative, descrive le interfacce e accompagna il lettore fino all'implementazione e ai test.

## Identità del sistema

Sicania è un sistema operativo didattico moderno progettato per essere:

- comprensibile;
- implementabile in modo progressivo;
- osservabile e diagnosticabile fin dal primo boot;
- modulare internamente;
- verificabile tramite test ed esperimenti;
- coerente tra teoria, decisioni, codice e comportamento;
- adatto a diventare un riferimento italiano per lo studio pratico dei sistemi operativi.

## Vincoli iniziali

- Architettura di riferimento: x86-64.
- Modalità operativa: esclusivamente 64 bit.
- Bootloader: Limine.
- Formato eseguibile principale: ELF.
- Ambiente iniziale di esecuzione: QEMU.
- Debug iniziale: porta seriale e GDB.
- Configurazione e compilazione: Meson e Ninja.
- Prima architettura del kernel: monolitica modulare.

Il supporto per altre architetture non appartiene alla prima fase del progetto.

## Principio architetturale principale

La prima versione usa un kernel monolitico, ma ogni sottosistema deve essere progettato come un componente con responsabilità e stato chiaramente delimitati.

La modularità richiede:

- strutture dati di proprietà del sottosistema;
- interfacce esplicite;
- dipendenze dichiarate;
- assenza di accessi arbitrari allo stato interno altrui;
- invarianti documentati;
- cicli di vita definiti;
- possibilità di testare separatamente la logica indipendente dall'hardware.

Questa disciplina deve permettere in futuro di spostare alcuni servizi fuori dallo spazio kernel e di valutare un'architettura ibrida o microkernel senza riscrivere integralmente il sistema.

## Metodo di evoluzione

L'architettura non viene progettata interamente in anticipo. Si evolve per incrementi completi:

```text
Problema
    ↓
Teoria
    ↓
Alternative
    ↓
Scelta di Sicania
    ↓
Contratto minimo
    ↓
Implementazione
    ↓
Debug e test
```

Prima di implementare un componente devono essere chiari almeno:

- responsabilità;
- dati posseduti;
- interfacce pubbliche;
- dipendenze;
- errori possibili;
- invarianti;
- risultato osservabile atteso.

Questi elementi vengono descritti nel capitolo pertinente. Le ADR registrano soltanto le decisioni architetturali significative che meritano una traccia sintetica e stabile.

## Componenti di alto livello

### Boot e inizializzazione

Responsabilità:

- ricevere il controllo da Limine;
- validare le informazioni di boot;
- predisporre stack e stato iniziale;
- inizializzare la diagnostica seriale;
- trasferire il controllo all'inizializzazione indipendente dal bootloader.

Il codice dipendente da Limine deve restare confinato in un adattatore, evitando che il resto del kernel dipenda direttamente dalle strutture del bootloader.

### Diagnostica e logging

Il sistema diagnostico deve essere disponibile nelle prime fasi del boot.

Responsabilità:

- output seriale;
- livelli e categorie di log;
- messaggi strutturati;
- panic e arresto controllato;
- informazioni utili al debugging con GDB;
- futura estensione verso altri canali di output.

L'osservabilità è un requisito architetturale, non un'aggiunta successiva.

### Livello dipendente dall'architettura

Raccoglie le operazioni specifiche di x86-64:

- CPU e registri di controllo;
- GDT e TSS;
- interrupt ed eccezioni;
- timer e controller degli interrupt;
- primitive atomiche;
- operazioni di I/O;
- cambio di contesto e gestione dei privilegi.

Questo livello non deve diventare un contenitore generico di qualunque codice hardware. Le sue interfacce devono esporre meccanismi, non politiche dei sottosistemi superiori.

### Gestione della memoria

Il sottosistema memoria è suddiviso almeno in:

- descrizione della mappa fisica;
- allocazione delle pagine fisiche;
- gestione delle tabelle di paging;
- spazio virtuale del kernel;
- spazi virtuali dei processi;
- allocazione dinamica del kernel;
- protezione e permessi.

Il gestore fisico, il gestore virtuale e l'allocatore dinamico devono conservare responsabilità e strutture dati distinte.

### Interrupt, eccezioni e tempo

Responsabilità:

- configurazione delle strutture x86-64;
- gestione uniforme delle eccezioni;
- registrazione e dispatch degli handler;
- diagnostica dei fault;
- integrazione con timer, scheduler e driver;
- esposizione di una sorgente temporale affidabile.

Il livello architetturale gestisce il meccanismo; i sottosistemi decidono la politica con cui reagire agli eventi.

### Thread, processi e scheduler

Il modello di esecuzione distingue:

- contesto di esecuzione;
- thread;
- processo;
- spazio di indirizzamento;
- stato schedulabile;
- politica di scheduling.

Lo scheduler non deve possedere direttamente ogni dettaglio dei processi. Meccanismo e politica devono restare separati quanto necessario per consentire evoluzioni future.

### Syscall e ABI

Questo sottosistema definisce il confine tra kernel e spazio utente.

Deve stabilire:

- convenzione di chiamata;
- numerazione delle syscall;
- passaggio e validazione degli argomenti;
- codifica degli errori;
- compatibilità e versionamento dell'ABI;
- copia sicura tra spazi di indirizzamento.

Le strutture interne del kernel non devono essere esposte direttamente attraverso l'ABI.

### IPC

L'IPC fornisce primitive esplicite per la comunicazione tra contesti di esecuzione.

Le alternative da valutare includono:

- messaggi;
- code;
- notifiche;
- memoria condivisa;
- primitive di sincronizzazione.

La progettazione dell'IPC è centrale per una futura separazione dei servizi.

### Filesystem e VFS

Il filesystem deve separare:

- modello degli oggetti;
- namespace;
- operazioni sui file;
- file descriptor;
- cache;
- filesystem concreti;
- dispositivi di memorizzazione.

La VFS deve evitare sia la dipendenza da un unico formato persistente sia astrazioni premature non richieste dal sistema reale.

### Driver

I driver interagiscono con il resto del kernel tramite interfacce definite.

La progettazione distingue:

- scoperta del dispositivo;
- inizializzazione;
- gestione delle risorse;
- interrupt;
- trasferimento dei dati;
- esposizione del dispositivo ai servizi superiori.

I driver non devono accedere indiscriminatamente alle strutture interne degli altri sottosistemi.

### Networking

Il networking viene introdotto dopo la stabilizzazione dei sottosistemi fondamentali.

La progettazione separa:

- driver di rete;
- gestione dei buffer;
- protocolli;
- routing;
- socket e API utente;
- sicurezza e isolamento.

### Sicurezza

La sicurezza attraversa tutti i sottosistemi e non costituisce soltanto una fase finale.

Devono essere affrontati progressivamente:

- separazione dei privilegi;
- isolamento degli spazi di indirizzamento;
- validazione degli input;
- controllo delle risorse;
- autorizzazione;
- protezione dell'ABI;
- comportamento in caso di errore;
- contenimento dei fault.

## Comunicazione tra componenti

Nella prima implementazione i componenti del kernel comunicano mediante chiamate interne esplicite, eventi e strutture dati controllate.

Sono vietati come impostazione predefinita:

- accessi diretti allo stato privato di un altro sottosistema;
- variabili globali usate come interfaccia implicita;
- dipendenze circolari;
- callback prive di contratto;
- condivisione di strutture dati senza proprietà e ciclo di vita documentati.

Ogni interfaccia significativa deve specificare:

- proprietario dei dati;
- ciclo di vita;
- sincronizzazione;
- contesto di invocazione;
- errori possibili;
- invarianti.

## Spazio kernel e spazio utente

La prima fase concentra i meccanismi fondamentali nello spazio kernel.

Lo spazio utente viene introdotto quando sono disponibili almeno:

- memoria virtuale affidabile;
- gestione dei privilegi;
- thread e processi;
- syscall;
- caricamento ELF;
- gestione degli errori al confine kernel-utente.

La prima implementazione monolitica non implica che ogni servizio debba restare definitivamente nello spazio kernel.

## Decisioni iniziali da registrare

Le ADR iniziali possono includere:

1. x86-64 come piattaforma iniziale;
2. esecuzione esclusivamente a 64 bit;
3. Limine come bootloader;
4. kernel monolitico modulare;
5. Meson e Ninja come strumenti di build;
6. QEMU e GDB come ambiente di esecuzione e debug;
7. seriale come primo canale diagnostico;
8. proprietà delle strutture dati e dipendenze tra sottosistemi;
9. strategia per una futura separazione dei servizi.

Non deve essere registrata come decisione la separazione tra libro e specifiche: il libro è il documento tecnico autorevole e contiene i contratti necessari all'implementazione.

## Ordine evolutivo

1. toolchain e ambiente riproducibile;
2. boot e diagnostica;
3. livello x86-64;
4. interrupt, eccezioni e timer;
5. memoria fisica e virtuale;
6. allocazione dinamica;
7. thread e scheduler;
8. processi e caricamento ELF;
9. syscall e ABI;
10. IPC;
11. filesystem e driver;
12. networking;
13. sicurezza e stabilizzazione.

Questo ordine può essere raffinato dalla roadmap quando emergono dipendenze reali, purché ogni cambiamento sia motivato.

## Relazione con il libro

I capitoli devono:

- introdurre il problema;
- spiegare la teoria necessaria;
- confrontare le alternative;
- mostrare perché Sicania adotta una determinata soluzione;
- descrivere strutture dati, interfacce e invarianti;
- accompagnare il lettore verso implementazioni verificabili;
- mostrare strumenti di debugging e test;
- dichiarare limiti e passi successivi.

Quando l'architettura cambia, devono essere aggiornati il capitolo pertinente, questa mappa, le ADR interessate, il codice e i test.

## Primo incremento

Il primo incremento completo del sistema comprende:

1. configurazione di Meson e Ninja;
2. produzione di un ELF x86-64 freestanding;
3. boot tramite Limine;
4. entry point del kernel;
5. output seriale;
6. panic minimale;
7. esecuzione in QEMU;
8. sessione di debug con GDB;
9. test automatico che riconosca il boot riuscito.

Al termine di questo incremento Sicania deve essere già compilabile, avviabile, osservabile e verificabile.
