# 2. Cosa rende Sicania diverso

Ci sono già tanti progetti didattici di sistemi operativi. Cosa aggiunge Sicania?

## Confronto con altri approcci

| Proprietà | xv6 | Linux da zero | Sicania |
|-----------|-----|---------------|---------|
| Linguaggio | C | C | C |
| Target | x86 | x86-64 | x86-64 |
| Bootloader | nessuno | Limine | Limine |
| Build system | Makefile custom | custom | Meson/Ninja |
| Test automatici | no | no | sì |
| Libro / specifica | codice | nessuno | specifica .md |
| Separazione doc/codice | doc | no | doc guida codice |

## Didattico ma vero

Sicania non è un giocattolo. Fa cose vere:

- si avvia con un bootloader vero (Limine)
- usa formati standard (ELF, x86-64)
- funziona in QEMU ma anche su hardware reale
- ha interrupt, memoria virtuale, processi, syscall

Le semplificazioni sono dichiarate. Non nascondiamo la complessità, la riduciamo solo dove serve.

## Il libro guida il codice

```
Altri progetti:
  codice prima → documentazione dopo (se va bene)
  La documentazione insegue il codice, spesso arretrata.

Sicania:
  specifica prima → codice dopo
  Il codice realizza la specifica. La specifica non cambia senza讨论.
```

## Build system vero

Usiamo Meson e Ninja, gli stessi strumenti usati da progetti come systemd, Xorg e Mesa. Niente Makefile artigianali.

## Test fin dal primo giorno

Il primo boot è già testato automaticamente. Ogni aggiunta successiva ha un test. Non scriviamo codice che non possiamo verificare.

## Fatto in italiano

La maggior parte della documentazione tecnica sui sistemi operativi è in inglese. Sicania è un progetto italiano, pensato per chi vuole studiare nella propria lingua.
