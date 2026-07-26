# 1. Che cos'è un sistema operativo

La domanda sembra semplice, ma "sistema operativo" significa cose diverse a seconda di chi parla.

Per chi usa il computer, è Windows o macOS. Per uno sviluppatore, è il kernel più le librerie di sistema. Per un progetto come Sicania, dobbiamo essere più precisi.

## I tre ruoli del sistema operativo

```
┌──────────────────────────────────────────────┐
│           SISTEMA OPERATIVO                   │
│                                              │
│  1. GESTORE DI RISORSE                       │
│     ┌─────────┬──────────┬──────────────┐   │
│     │ CPU     │ Memoria  │ Dispositivi  │   │
│     │ (quanto │ (quanta, │ (disco,      │   │
│     │  tempo) │  dove)   │  rete, ...)  │   │
│     └─────────┴──────────┴──────────────┘   │
│                                              │
│  2. COSTRUTTORE DI ASTRAZIONI                │
│     hardware → processi, file, socket       │
│                                              │
│  3. APPLICATORE DI PROTEZIONE                │
│     isola processi, protegge kernel         │
└──────────────────────────────────────────────┘
```

## Senza sistema operativo

```
Programma A              Programma B
    │                        │
    │ (scrive su disco)      │ (legge dallo stesso disco)
    │ (usa tutta la CPU)     │ (aspetta per sempre)
    │ (legge RAM di B)       │ (corrotto)
    │                        │
    └─────── caos totale ────┘
```

Ogni programma farebbe quello che vuole. Non esisterebbero ordine, isolamento, priorità.

## Con sistema operativo

```
Programma A              Programma B
    │                        │
    ├── syscall: write ─────→│─── kernel verifica permessi
    │                        │─── driver gestisce accesso
    │                        │─── scheduler dà tempo a entrambi
    │                        │─── MMU isola memoria
    │                        │
    └─────── ordine ─────────┘
```

## Gestore di risorse

L'hardware ha capacità finite. Il sistema operativo decide chi usa cosa, quando e per quanto tempo.

```
Risorsa      Problema              Soluzione del SO
────────     ────────              ────────────────
CPU          1 CPU, N processi     scheduling: a turno
RAM          limitata, contesa     memoria virtuale, protezione
Disco        accesso lento,       filesystem, cache, journal
             condiviso
Rete         pacchetti in          stack di rete, socket
             arrivo casuale
```

## Macchina estesa

L'hardware è scomodo. Il SO costruisce astrazioni:

```
Hardware              → Astrazione
───────────────────────────────────────────────
CPU + timer           → processo / thread
RAM                   → memoria virtuale
disco (settori)       → file / directory
scheda di rete        → socket
schermo + tastiera    → terminale
```

## Protezione

Il SO impedisce che un programma ne danneggi un altro:

```
Meccanismo            Cosa impedisce
───────────────────────────────────────────────
2 livelli di          kernel non accessibile
privilegio (ring)     dallo spazio utente
memoria virtuale      un processo non vede la RAM altrui
syscall               ogni richiesta viene validata
```

Queste tre funzioni (risorse, astrazioni, protezione) sono il nucleo di qualunque sistema operativo, incluso Sicania.
