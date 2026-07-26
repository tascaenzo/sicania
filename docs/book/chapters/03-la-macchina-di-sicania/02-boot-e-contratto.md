# 2. Boot e contratto Limine

All'accensione, la CPU esegue il firmware (UEFI), che carica il bootloader (Limine), che carica il kernel. Ogni passaggio riduce il livello di astrazione e aumenta il controllo che abbiamo sulla macchina.

## Catena di boot

```
ACCENSIONE
    │
    ▼
FIRMWARE (UEFI)
    │  inizializza CPU, RAM, dispositivi di base
    │  cerca il bootloader (nel disco/ISO)
    │  salta a Limine
    ▼
LIMINE
    │  cerca limine.conf nella radice dell'immagine
    │  trova il kernel (sicania.elf)
    │  carica l'ELF in memoria
    │  prepara page table, stack, strutture dati
    │  salta all'entry point _start
    ▼
KERNEL (Sicania)
    │  prende il controllo
    │  non lo lascia mai più
    ▼
[il resto del libro]
```

## Stato della CPU all'ingresso del kernel

Quando Limine salta a `_start`, la CPU è in questo stato preciso:

```
Registro / Stato      Valore                           Note
────────────────────  ──────────────────────────────   ─────────────────
Modalità              long mode 64 bit                 EFER.LME = 1
Privilegio            ring 0                           CPL = 0
Paging                attivo, 4 livelli                CR0.PG = 1
Interrupt (IF)        disabilitati                     CLI già eseguito
Direction flag (DF)   0 (forward)                      CLD già eseguito
Stack                 valido, ~16KB, allineato 16B     RSP valido ma temporaneo
Boot info             in RSI (protocollo v6)           struttura versionata
Red zone              NON usabile                      kernel compilato -mno-red-zone
SIMD/FPU              NON usabile                      kernel compilato -mno-sse
```

```
CPU all'entry point:

    ┌──────────────────────┐
    │  long mode, ring 0   │
    │  paging ON           │
    │  IF=0  DF=0          │
    │  RSP = stack tempor.  │
    │  RSI = boot_info      │
    └──────────────────────┘
              │
              ▼
        _start (entry.asm)
              │
              ▼
        kernel_main (kernel.c)
```

## Il contratto Limine: richieste e risposte

Il kernel comunica con Limine attraverso un meccanismo di *richieste*. Il kernel definisce delle strutture in una sezione speciale dell'ELF (`.limine_reqs`). Limine le trova, le elabora, e scrive i risultati nei campi `response` di ogni richiesta.

```
kernel ELF
│
├── sezione .text        (codice)
├── sezione .data        (dati)
├── sezione .rodata      (costanti)
├── sezione .bss         (zero-inizializzato)
│
└── sezione .limine_reqs  (puntatori alle richieste)
       │
       ├── puntatore → struct limine_memmap_request
       │                 ├── id = LIMINE_MEMMAP_REQUEST
       │                 ├── revision = 0
       │                 └── response = NULL (riempito da Limine)
       │
       ├── puntatore → struct limine_kernel_addr_request
       │                 ├── id = LIMINE_KERNEL_ADDR_REQUEST
       │                 ├── revision = 0
       │                 └── response = NULL (riempito da Limine)
       │
       └── ... altre richieste
```

```
FLUSSO:
1. Limine carica il kernel ELF
2. Scansiona la sezione .limine_reqs
3. Per ogni puntatore, legge la richiesta (id, revision)
4. Prepara i dati richiesti (mappa memoria, indirizzi, ...)
5. Scrive il puntatore a response nella struct
6. Salta a _start

Il kernel ora può leggere:
  memmap_request.response  → mappa della memoria
  kernel_addr.response     → dove è stato caricato
  ...
```

## Mappa della memoria

Una delle informazioni più importanti che Limine passa al kernel è la **mappa della memoria**: l'elenco di tutte le regioni di RAM fisica, classificate per tipo.

```
Mappa memoria (esempio)
┌────────────────────┬──────────────┬────────────────┐
│ Intervallo         │ Tipo         │ Uso            │
├────────────────────┼──────────────┼────────────────┤
│ 0x00000000 - 0x1000│ Riservata    │ ACPI / BIOS    │
│ 0x1000   - 0x9FC00│ Usabile      │ RAM libera      │
│ 0x9FC00  - 0xA0000│ Riservata    │ EBDA           │
│ 0x100000 - 0x7FE0000│ Usabile     │ RAM libera      │
│ ...                │ ...          │ ...             │
│ 0xF0000  - 0x100000│ Bootloader   │ kernel/initrd   │
│ ...                │ ...          │ ...             │
└────────────────────┴──────────────┴────────────────┘
```

Il kernel usa questa mappa per sapere dove può allocare memoria fisica.

## Il nostro primo boot in sintesi

```
limine.conf ──→ dice a Limine dov'è il kernel
                     │
link.ld ────────────→ dice al linker come organizzare l'ELF
                     │
entry.asm ──────────→ primo codice eseguito (stack + chiamata C)
                     │
kernel.c ───────────→ inizializza seriale, stampa messaggio, hlt
                     │
meson.build ────────→ compila tutto
                     │
qemu ───────────────→ esegue il kernel
                     │
grep di test ───────→ verifica che "Sicania" compaia nell'output
```

## Strumenti necessari

Per compilare e avviare Sicania ti servono:

```
meson, ninja      → build system
gcc/clang         → compilatore C
nasm              → assembler x86-64
ld                → linker
qemu-system-x86_64 → emulatore
xorriso           → creare ISO avviabili (opzionale)
gdb               → debugger (opzionale)
```

Nel prossimo capitolo installiamo tutto e specifichiamo ogni componente del primo boot.
