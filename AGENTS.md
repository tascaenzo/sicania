# AGENTS.md

## 1. Scopo

Sicania è un progetto didattico e tecnico dedicato alla progettazione e alla realizzazione di un sistema operativo moderno.

Il progetto produce contemporaneamente:

- un libro tecnico in italiano;
- un sistema operativo reale e funzionante;
- test, strumenti e materiali che rendono verificabili le scelte descritte.

Il libro non è una documentazione aggiunta successivamente al codice e il kernel non è un semplice allegato del libro. Sicania è l'opera completa: teoria, progettazione, implementazione, debugging e verifica evolvono insieme.

## 2. Fonte autorevole

Il libro è il documento tecnico e didattico principale del progetto.

Ogni capitolo deve spiegare il problema, introdurre la teoria necessaria, confrontare le alternative, motivare la soluzione adottata in Sicania e accompagnare il lettore fino al codice e ai test.

Non deve essere creata una gerarchia parallela di specifiche che duplichi il contenuto dei capitoli. Quando serve un contratto tecnico preciso, esso viene descritto nel capitolo pertinente, vicino alle strutture dati, alle interfacce e all'implementazione che disciplina.

Le ADR registrano in forma sintetica le decisioni architetturali significative e le loro motivazioni. Non sostituiscono il libro e non devono duplicarne la trattazione.

## 3. Stato e obiettivo corrente

La fondazione editoriale è pronta per sostenere lo sviluppo del libro e del kernel.

L'obiettivo corrente è avviare la costruzione progressiva di Sicania attraverso un primo percorso completo:

1. ambiente di sviluppo e toolchain;
2. formato ELF e vincoli x86-64;
3. avvio tramite Limine;
4. kernel minimale;
5. output seriale;
6. panic e diagnostica iniziale;
7. esecuzione con QEMU;
8. debugging con GDB;
9. test automatico del primo boot.

Non è necessario completare in anticipo l'intera architettura del sistema. Ogni sottosistema deve però avere, prima o insieme alla sua implementazione, una spiegazione e un contratto minimo sufficienti a renderlo comprensibile e verificabile.

## 4. Principio guida

Ogni incremento significativo segue questo ciclo:

```text
Problema
    ↓
Teoria
    ↓
Alternative
    ↓
Scelta di Sicania
    ↓
Strutture dati e interfacce
    ↓
Implementazione
    ↓
Debug e test
    ↓
Esperimenti e riflessioni
```

Il progetto non separa una lunga fase teorica da una successiva fase di implementazione. La teoria viene introdotta quando serve a comprendere il passo concreto che il lettore sta per realizzare.

## 5. Metodo di lavoro

Per ogni modifica significativa:

1. descrivere il problema e il risultato osservabile atteso;
2. identificare requisiti, vincoli e rischi;
3. introdurre la teoria necessaria;
4. confrontare le alternative realistiche;
5. motivare la scelta adottata in Sicania;
6. documentare strutture dati, interfacce, proprietà e invarianti;
7. implementare il minimo incremento coerente;
8. aggiungere diagnostica e test;
9. aggiornare il capitolo e la roadmap nello stesso cambiamento, quando pertinente.

Una decisione architetturale significativa deve essere registrata anche in una ADR sintetica.

## 6. Principi architetturali

La piattaforma iniziale è x86-64 e opera esclusivamente a 64 bit.

I vincoli iniziali sono:

- bootloader: Limine;
- formato eseguibile principale: ELF;
- configurazione e compilazione: Meson e Ninja;
- ambiente di esecuzione: QEMU;
- diagnostica iniziale: porta seriale;
- debugger iniziale: GDB;
- prima architettura del kernel: monolitica modulare.

I sottosistemi condividono inizialmente lo spazio di indirizzamento del kernel, ma devono avere responsabilità definite, stato controllato, strutture dati di proprietà, interfacce esplicite e dipendenze dichiarate.

La modularità deve consentire evoluzioni future verso un'architettura ibrida o microkernel, senza simulare prematuramente un microkernel all'interno del kernel monolitico.

## 7. Regole

- Non modificare obiettivi o roadmap senza una motivazione documentata.
- Non cambiare la terminologia senza aggiornare il glossario.
- Non introdurre decisioni architetturali significative senza spiegarle nel libro e, quando utile, registrarle in una ADR.
- Non creare documenti paralleli che duplichino il contenuto dei capitoli.
- Non introdurre dipendenze importanti senza analizzarne costi e conseguenze.
- Non condividere indiscriminatamente strutture dati interne.
- Evitare dipendenze circolari tra sottosistemi.
- Evitare astrazioni prive di un'esigenza concreta.
- Non ottimizzare prematuramente.
- Trattare osservabilità, debugging, testabilità e riproducibilità come requisiti progettuali.
- Non presentare decisioni provvisorie come definitive.
- Non implementare grandi sottosistemi privi di un contratto minimo documentato nel capitolo pertinente.
- Usare l'espressione "AI-native" solo quando descrive una proprietà architetturale concreta.

## 8. Struttura del repository

```text
AGENTS.md
README.md

adr/

docs/
└── book/
    ├── README.md
    ├── ROADMAP.md
    ├── ARCHITECTURE.md
    ├── STYLE_GUIDE.md
    ├── CONVENTIONS.md
    ├── GLOSSARY.md
    ├── BIBLIOGRAPHY.md
    ├── chapter-template.md
    ├── chapters/
    ├── diagrams/
    ├── images/
    └── assets/

kernel/
tests/
tools/
```

`docs/book/` contiene la trattazione autorevole. `adr/` conserva decisioni sintetiche. Il codice realizza quanto spiegato nei capitoli. I test verificano il comportamento e gli invarianti descritti.

Le directory di implementazione vengono create quando il primo incremento concreto ne richiede l'esistenza.

## 9. Rapporto tra libro e codice

Ogni capitolo pratico deve indicare chiaramente:

- quale problema risolve;
- quali concetti introduce;
- quali alternative sono state considerate;
- quale soluzione adotta Sicania;
- quali file e componenti vengono aggiunti o modificati;
- come osservare il risultato;
- come eseguire i test;
- quali limiti restano aperti.

Il codice può evolvere e sostituire implementazioni precedenti. Il libro deve spiegare questa evoluzione senza fingere che la prima soluzione sia già quella definitiva.

## 10. Cosa può fare un agente

Un agente può:

- analizzare e migliorare i capitoli;
- proporre e registrare decisioni architetturali;
- progettare componenti e interfacce;
- implementare incrementi già motivati nel percorso del libro;
- aggiungere diagrammi, strumenti diagnostici e test;
- mantenere coerenti libro, codice e roadmap.

Un agente non può cambiare autonomamente gli obiettivi fondamentali, introdurre dipendenze importanti senza analisi, eliminare documentazione senza motivazione o costruire sottosistemi scollegati dal percorso didattico approvato.

Quando è necessaria una nuova scelta, l'agente deve presentare problema, alternative, raccomandazione e conseguenze.

## 11. Definizione di completamento

Una modifica è completa quando, per quanto pertinente:

- il problema e la scelta sono spiegati;
- il capitolo è coerente con il codice;
- le interfacce e gli invarianti sono documentati;
- l'implementazione è compilabile;
- il comportamento è osservabile e diagnosticabile;
- i test verificano il risultato atteso;
- la roadmap e il glossario restano coerenti;
- le ADR pertinenti sono aggiornate.

Sicania non cresce per accumulo di codice o documenti, ma attraverso incrementi completi nei quali comprensione, progettazione, implementazione e verifica rimangono inseparabili.
