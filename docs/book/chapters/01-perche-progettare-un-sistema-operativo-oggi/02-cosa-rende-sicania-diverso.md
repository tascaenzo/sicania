# 2. Cosa rende Sicania diverso

Ci sono già tanti progetti didattici di sistemi operativi. Cosa aggiunge Sicania?

## Didattico ma vero

Sicania non è un giocattolo. Fa cose vere:

- si avvia con un bootloader vero (Limine)
- usa formati standard (ELF, x86-64)
- funziona in QEMU ma anche su hardware reale
- ha interrupt, memoria virtuale, processi, syscall

Le semplificazioni sono dichiarate. Non nascondiamo la complessità, la riduciamo solo dove serve.

## Il libro guida il codice

In molti progetti il codice viene prima e la documentazione dopo. In Sicania il libro è la fonte principale: ogni decisione è spiegata nel capitolo che la introduce.

Questo non significa che il libro sia una specifica ast ratta. Significa che quando leggi il codice, sai già perché è stato scritto così.

## Build system vero

Usiamo Meson e Ninja, gli stessi strumenti usati da progetti come systemd, Xorg e Mesa. Niente Makefile artigianali.

## Test fin dal primo giorno

Il primo boot è già testato automaticamente. Ogni aggiunta successiva ha un test. Non scriviamo codice che non possiamo verificare.

## Fatto in italiano

La maggior parte della documentazione tecnica sui sistemi operativi è in inglese. Sicania è un progetto italiano, pensato per chi vuole studiare nella propria lingua.
