# 2. Kernel e userland

Il kernel è la parte del sistema operativo che gira in modalità privilegiata. Ha accesso a tutto: registri della CPU, memoria fisica, dispositivi.

I programmi normali girano in *userland* (spazio utente). Non possono eseguire istruzioni privilegiate, non possono accedere direttamente all'hardware, non possono leggere la memoria del kernel o di altri processi.

```
┌─────────────────────┐
│   programmi utente   │  ← userland (non privilegiato)
├─────────────────────┤
│       kernel         │  ← modalità privilegiata
├─────────────────────┤
│       hardware       │
└─────────────────────┘
```

## Cosa fa il kernel

Il kernel di Sicania si occupa almeno di:

- **inizializzazione**: riceve il controllo dal bootloader, prepara la macchina
- **memoria**: gestisce la memoria fisica e virtuale
- **interrupt ed eccezioni**: reagisce a eventi hardware e errori della CPU
- **processi e thread**: crea, schedula e termina attività
- **syscall**: fornisce servizi ai programmi utente
- **driver**: comunica con i dispositivi
- **diagnostica**: produce log e gestisce i guasti

## Cosa NON fa il kernel

Il kernel è la parte minima e privilegiata. Molte cose che associ a un "sistema operativo" in realtà girano in userland:

- shell e comandi
- librerie (libc, ecc.)
- server grafico
- servizi di rete
- filesystem (in alcune architetture)
- driver (in alcune architetture)

Sicania inizialmente terrà quasi tutto nel kernel (architettura monolitica), ma i sottosistemi saranno progettati con confini chiari. In futuro, potremo spostare servizi in userland senza riscrivere tutto.

## Questa distinzione è importante perché

Quando scriverai codice per Sicania, devi sapere se stai scrivendo kernel o userland. Nel kernel non hai librerie standard, non hai `malloc`, non hai `printf`, non hai protezione dalla memoria. Se sbagli, non ricevi un errore: la macchina si blocca.

È proprio questo che rende la programmazione di kernel così formativa.
