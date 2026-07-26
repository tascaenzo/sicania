# 4. Linker script

Il linker script dice a `ld` come organizzare l'ELF del kernel: quali sezioni includere, in che ordine, a quali indirizzi virtuali.

## Layout della memoria virtuale del kernel

```
Spazio di indirizzamento x86-64 (48 bit canonici)

0x0000000000000000
    ┌──────────────────────┐
    │                      │
    │   SPAZIO UTENTE     │   processi, programmi, librerie
    │                      │   (non accessibile dal kernel? sì,
    │                      │    ma con permessi separati)
    │                      │
    ├──────────────────────┤ 0x00007FFFFFFFFFFF
    │                      │
    │   CANONICAL GAP      │   spazio non indirizzabile
    │                      │   (errore se usato)
    ├──────────────────────┤ 0xFFFFFFFF80000000
    │                      │
    │   HIGHER HALF        │
    │   ┌──────────────┐   │
    │   │ .text        │   │  codice kernel
    │   ├──────────────┤   │
    │   │ .rodata      │   │  costanti
    │   ├──────────────┤   │
    │   │ .data        │   │  dati inizializzati
    │   ├──────────────┤   │
    │   │ .bss         │   │  zero-inizializzato
    │   │              │   │  contiene lo stack (16KB)
    │   └──────────────┘   │
    │                      │
    └──────────────────────┘ 0xFFFFFFFFFFFFFFFF
```

## Specifica del linker script

### OUTPUT_FORMAT

```
OUTPUT_FORMAT(elf64-x86-64)
```

Il linker produce un ELF a 64 bit per x86-64. Questo è il formato che Limine si aspetta.

### ENTRY

```
ENTRY(_start)
```

L'entry point è il simbolo `_start`, definito in `entry.asm` con `global _start`. Quando Limine carica l'ELF, legge questo simbolo e salta al suo indirizzo.

### Indirizzo base

```
. = 0xFFFFFFFF80000000 + 0x100000;
```

Il kernel viene caricato all'indirizzo virtuale `0xFFFFFFFF80100000` (4 MB + 1 MB dentro il higher half). Questo indirizzo:

- è nel **higher half** (non accessibile da ring 3)
- è allineato a 4 KB (page granularity)
- è sufficientemente alto da lasciare spazio per i mapping delle strutture dati del bootloader

### Sezioni

```
Sezione    Tipo contenuto    Esempi
────────   ───────────────   ─────────────────
.text      codice eseguibile   entry.asm, funzioni C
.rodata    dati di sola lettura stringhe, tabelle costanti
.data      dati inizializzati variabili globali con valore
.bss       zero-inizializzato  stack, variabili senza valore iniziale
```

### Il `.bss` in dettaglio

Il `.bss` è speciale: non occupa spazio nel file ELF, ma occupa spazio in memoria. Contiene tutte le variabili globali senza inizializzazione esplicita.

```
File ELF (su disco)           Memoria (al boot)
┌──────────────────┐         ┌──────────────────┐
│ .text (codice)   │  load   │ .text            │
│ .rodata          │  ────→  │ .rodata          │
│ .data (valori)   │         │ .data            │
│ .bss (0 byte)    │         │ .bss (16 KB)     │
└──────────────────┘         │   ↓              │
                             │  azzerato!       │
                             └──────────────────┘
```

Limine azzera il `.bss` prima di saltare al kernel. Questo garantisce che lo stack parta pulito.

## Relazioni

```
link.ld
  ├─ ENTRY(_start)       → entry.asm (definisce _start)
  ├─ sezione .text       → codice di entry.asm, kernel.c, serial.c
  ├─ sezione .bss        → stack di entry.asm
  └─ indirizzo base       → Limine per mappare il kernel
```

## Rischi

- **Indirizzo base sbagliato**: se non coincide con ciò che Limine/Linker si aspettano, page fault immediato
- **BSS non azzerato**: lo stack contiene spazzatura, il kernel crasha alla prima chiamata di funzione
- **Entry point errato**: se `_start` non è definito, il linker dà errore "undefined reference"
- **Sezioni mancanti**: se dimentichiamo una sezione (es. `.limine_reqs`), il protocollo Limine non funziona
