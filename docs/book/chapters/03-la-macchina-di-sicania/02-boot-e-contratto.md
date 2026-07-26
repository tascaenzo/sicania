# 2. Boot e contratto Limine

All'accensione, la CPU esegue il firmware (UEFI), che carica il bootloader (Limine), che carica il kernel. Ogni passaggio riduce il livello di astrazione e aumenta il controllo che abbiamo sulla macchina.

## Chi fa cosa

```
accensione
    ↓
firmware (UEFI): inizializza l'hardware, cerca il bootloader
    ↓
Limine: carica il kernel in memoria, prepara le strutture, salta all'entry point
    ↓
kernel: prende il controllo, non lo lascia mai più
```

## Il contratto Limine

Limine non carica il kernel e basta. Prima di saltare all'entry point, prepara:

- **CPU in long mode a 64 bit**, ring 0
- **paging attivo** con mapping per il codice del kernel
- **stack temporaneo** (usabile ma non definitivo)
- **mappa della memoria** (quali regioni di RAM sono libere)
- **boot info**: una struttura con firma, versione, e tutti i dati sopra

Il kernel riceve tutto in un puntatore passato nei registri.

## Come comunichiamo con Limine

Nel kernel definiamo *richieste* (requests) usando strutture specifiche. Limine le trova guardando una sezione speciale dell'ELF. Ogni richiesta dice: "voglio la mappa della memoria", "voglio il framebuffer", ecc.

```
kernel ELF
  ├─ sezione .limine_reqs (contiene i puntatori alle richieste)
  │
  ├─ richiesta: memory map  → Limine scrive qui i dati
  ├─ richiesta: kernel address
  └─ richiesta: framebuffer (opzionale)
```

Nel codice, una richiesta è una struct con un campo `id` che identifica il tipo di informazione richiesta. Limine riempie il campo `response` con i dati.

## Entry point

L'entry point del kernel è un simbolo esportato nell'ELF. Limine legge il symbol `_start` (o quello configurato) e ci salta. A questo punto la CPU è già in 64 bit con paging attivo.

## Cosa riceviamo esattamente

All'ingresso del kernel:

| Cosa | Stato |
|------|-------|
| Modalità CPU | long mode 64 bit, ring 0 |
| Paging | attivo, kernel mappato |
| Stack | sì, temporaneo, 16KB circa |
| Interrupt esterni | disabilitati |
| Boot info | puntatore in RSI (Limine protocol v6) |

Il nostro primo compito: salvare il puntatore, verificare che sia valido, e iniziare la nostra inizializzazione.

## Strumenti necessari

Per compilare e avviare Sicania ti servono:

```
meson, ninja      → build system
gcc/clang         → compilatore C
nasm              → assembler x86-64
ld                → linker
qemu-system-x86_64 → emulatore
xorriso           → creare ISO avviabili (opzionale)
```

Nel prossimo capitolo installiamo tutto e scriviamo il primo boot.
