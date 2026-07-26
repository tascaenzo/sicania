# 2. Kernel e userland

Il kernel è la parte del sistema operativo che gira in modalità privilegiata. Ha accesso a tutto: registri della CPU, memoria fisica, dispositivi.

I programmi normali girano in *userland* (spazio utente). Non possono eseguire istruzioni privilegiate, non possono accedere direttamente all'hardware, non possono leggere la memoria del kernel o di altri processi.

## I tre livelli

```
┌─────────────────────────────────────┐
│         USERLAND                    │
│  (programmi, librerie, shell)       │
│  modalità: ring 3                   │
│  NON può: istruzioni privilegiate   │
│           accesso diretto hardware   │
│           memoria del kernel         │
├─────────────────────────────────────┤
│         KERNEL                      │
│  (scheduler, driver, memoria, ...)  │
│  modalità: ring 0                   │
│  PUÒ: tutto                         │
├─────────────────────────────────────┤
│         HARDWARE                    │
│  CPU, RAM, disco, rete, ...        │
└─────────────────────────────────────┘
         ↑
    transizioni controllate
    (syscall, interrupt, eccezioni)
```

## Cosa fa il kernel

```
 kernel
 ├── boot e inizializzazione
 │    riceve il controllo, prepara la macchina
 │
 ├── memoria
 │    gestisce pagina fisica e virtuale
 │
 ├── interrupt ed eccezioni
 │    reagisce a eventi hardware ed errori CPU
 │
 ├── processi e thread
 │    crea, schedula, termina attività
 │
 ├── syscall
 │    servizi ai programmi utente
 │
 ├── driver
 │    comunica con dispositivi
 │
 └── diagnostica
      log, panic, arresto controllato
```

## Cosa NON fa il kernel

Il kernel è la parte minima e privilegiata. Molte cose che associ a un "sistema operativo" in realtà girano in userland:

```
Userland:
  shell, comandi, servizi
  libreria C standard
  server grafico
  server di rete
  filesystem (in alcune architetture)
  driver (in alcune architetture)
```

Sicania inizialmente terrà quasi tutto nel kernel (architettura monolitica), ma i sottosistemi saranno progettati con confini chiari. In futuro, potremo spostare servizi in userland senza riscrivere tutto.

## Perché questa distinzione conta

| Aspetto | Kernel | Userland |
|---------|--------|----------|
| Libreria C | nessuna (no printf, no malloc) | hai la libc |
| Protezione memoria | nessuna | la memoria è protetta |
| Errore | la macchina si blocca | segmentation fault, non crash |
| Accesso hardware | diretto | via syscall |

Quando scriverai codice per Sicania, devi sapere in che livello stai operando. La programmazione kernel è così formativa proprio perché toglie ogni rete di sicurezza.
