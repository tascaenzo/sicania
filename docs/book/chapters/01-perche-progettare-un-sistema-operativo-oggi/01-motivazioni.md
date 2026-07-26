# 1. Perché scrivere un sistema operativo

Studiare i sistemi operativi sui libri di teoria è come studiare nuoto su una lavagna. Impari parole come *scheduler*, *paginazione*, *syscall*, ma non senti mai l'acqua.

Questo libro funziona al contrario: scriverai un sistema operativo funzionante, partendo da zero. Ogni concetto viene introdotto quando serve a scrivere la prossima riga di codice.

## Come funziona un libro tradizionale

```
capitolo 1: teoria dei processi
capitolo 2: teoria della memoria
capitolo 3: teoria dei filesystem
...
capitolo 10: esercizi
----------------------------------------------------
→ Lo studente legge 9 capitoli prima di scrivere una riga
```

## Come funziona questo libro

```
capitolo 1: "boota il kernel"
  ├─ teoria minima: cos'è un entry point
  ├─ codice: entry.asm, kernel.c
  └─ verifica: QEMU stampa "ok"

capitolo 2: "stampa sulla seriale"
  ├─ teoria minima: come funziona una UART
  ├─ codice: serial.c
  └─ verifica: vedi il messaggio sul terminale

capitolo 3: "gestisci un'interrupt"
  ├─ teoria minima: IDT, eccezioni
  ├─ codice: idt.c, handler.asm
  └─ verifica: premi un tasto, il kernel lo stampa

...

Ogni capitolo = un incremento COMPILABILE E FUNZIONANTE
```

## Perché un nuovo OS

Linux, Windows e macOS esistono. Perché scriverne un altro?

Non per competere. Perché l'unico modo per capire veramente come funziona un sistema operativo è costruirne uno.

Quando leggi il codice di Linux, ogni decisione è sepolta sotto 30 anni di compatibilità, ottimizzazioni e supporto hardware. Un kernel didattico ti mostra l'essenziale: niente legacy, niente driver per hardware che non hai, niente codice scritto per motivi storici che nessuno spiega.

## Cosa imparerai

Alla fine di questo percorso avrai un kernel che:

- si avvia su hardware reale o in QEMU
- stampa messaggi sulla porta seriale
- gestisce memoria, interrupt e processi
- esegue programmi in spazio utente
- carica file da un filesystem

Ma soprattutto avrai la capacità di aprire un kernel qualunque e capire perché è fatto in un certo modo.
