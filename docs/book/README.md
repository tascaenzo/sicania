# Sicania Book

Benvenuto nel libro ufficiale di Sicania.

## Obiettivo

Questo libro accompagna il lettore nella progettazione e nella realizzazione di un sistema operativo moderno, costruendo progressivamente un kernel reale e verificabile.

> Comprendere prima di implementare. Implementare per verificare di aver compreso.

Sicania non è un esempio separato dalla trattazione: è il sistema operativo sviluppato nel libro. Ogni capitolo collega teoria, decisioni progettuali, codice, debugging e test.

Il lettore non studia soltanto i sistemi operativi: costruisce progressivamente Sicania.

## Metodo

I capitoli pratici seguono, quando pertinente, questo percorso:

1. problema e risultato atteso;
2. teoria necessaria;
3. alternative e compromessi;
4. scelta adottata in Sicania;
5. strutture dati e interfacce;
6. implementazione reale;
7. debugging e test;
8. esperimenti e approfondimenti.

Il libro è il documento tecnico e didattico principale. I contratti dei sottosistemi vengono descritti nei capitoli che li introducono, evitando una gerarchia separata di specifiche duplicate.

Le ADR conservano in forma sintetica le decisioni architetturali significative e le loro motivazioni.

## Come leggere il libro

1. Segui i capitoli nell'ordine indicato dalla roadmap.
2. Esegui gli incrementi del kernel insieme alla lettura.
3. Osserva ogni risultato tramite seriale, QEMU, GDB e test automatici.
4. Consulta il glossario per la terminologia adottata.
5. Usa la bibliografia e i riferimenti dei capitoli per approfondire.

## Documenti di riferimento

- `ROADMAP.md`: percorso editoriale e tecnico del progetto;
- `ARCHITECTURE.md`: mappa evolutiva dell'architettura di Sicania;
- `STYLE_GUIDE.md`: stile della trattazione;
- `CONVENTIONS.md`: convenzioni editoriali e tecniche;
- `GLOSSARY.md`: terminologia del progetto;
- `BIBLIOGRAPHY.md`: fonti principali;
- `chapter-template.md`: struttura di riferimento dei capitoli.

## Struttura

```text
book/
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
```

Il codice del kernel, i test e gli strumenti vivono nelle rispettive directory del repository e avanzano insieme ai capitoli.
