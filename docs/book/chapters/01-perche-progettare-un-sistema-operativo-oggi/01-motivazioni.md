# 1. Perché scrivere un sistema operativo

Studiare i sistemi operativi sui libri di teoria è come studiare nuoto su una lavagna. Impari parole come *scheduler*, *paginazione*, *syscall*, ma non senti mai l'acqua.

Questo libro funziona al contrario: scriverai un sistema operativo funzionante, partendo da zero. Ogni concetto viene introdotto quando serve a scrivere la prossima riga di codice.

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
- carca file da un filesystem

Ma soprattutto avrai la capacità di aprire un kernel qualunque e capire perché è fatto in un certo modo.

## Come funziona questo libro

Ogni capitolo segue la stessa struttura:

1. **Problema**: cosa stiamo cercando di fare
2. **Codice**: lo scriviamo subito
3. **Spiegazione**: cosa fa quel codice e perché
4. **Verifica**: compila, esegui, guarda il risultato

Non ci sono capitoli di sola teoria. Se non scrivi codice, non stai imparando.
