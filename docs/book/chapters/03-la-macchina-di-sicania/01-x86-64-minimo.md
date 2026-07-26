# 1. x86-64: quello che serve sapere (davvero)

Un processore x86-64 è una macchina a stati. Ha registri, esegue istruzioni, genera eventi. Per scrivere un kernel devi conoscere solo le parti che il kernel usa.

## Modello di esecuzione

```
                        CPU
       ┌─────────────────────────────────┐
       │  Registri generali (16 × 64 bit)│
       │  RAX RBX RCX RDX RSI RDI       │
       │  RBP RSP  R8-R15               │
       ├─────────────────────────────────┤
       │  RIP → prossima istruzione      │
       │  RFLAGS → flag di stato         │
       ├─────────────────────────────────┤
       │  CR3 → radice page table        │
       │  IDTR → base IDT                │
       └─────────────────────────────────┘
                 │
    ┌────────────┴────────────┐
    │                         │
  esegue                    genera
 istruzioni                eventi
    │                  (eccezioni,
    │                   interrupt,
    │                   syscall)
    ▼                         │
  RAM ──── page table ────────┘
```

## Registri

La CPU ha 16 registri generali a 64 bit. Si usano per operazioni aritmetiche, puntatori, parametri:

```
63                               0
┌────────────────────────────────┐
│            RAX                 │  accumulatore
├────────────────────────────────┤
│            RBX                 │  base
├────────────────────────────────┤
│            RCX                 │  contatore
├────────────────────────────────┤
│            RDX                 │  dati
├────────────────────────────────┤
│            RSI                 │  sorgente (stringhe, argomento syscall)
├────────────────────────────────┤
│            RDI                 │  destinazione (argomento syscall)
├────────────────────────────────┤
│            RBP                 │  base frame
├────────────────────────────────┤
│            RSP ←── STACK       │  stack pointer
├────────────────────────────────┤
│          R8 - R15              │  extra
└────────────────────────────────┘
```

### Registri speciali

| Registro | Descrizione |
|----------|-------------|
| RIP (instruction pointer) | indirizzo della prossima istruzione |
| RFLAGS | bandiere: ZF, CF, IF (interrupt), DF |
| CR0, CR2, CR3, CR4 | registri di controllo |
| CR3 | radice delle page table (dice dove sono le tabelle) |
| IDTR | base e limite della IDT |
| GDTR | base e limite della GDT |

## Privilegio: i ring

x86-64 ha 4 livelli di privilegio chiamati *ring*. Noi useremo solo 2:

```
                    Più privilegio
                    ▲
              ┌─────┴─────┐
              │  Ring 0   │ ← KERNEL (può tutto)
              ├───────────┤
              │  Ring 1   │ ← inutilizzato
              ├───────────┤
              │  Ring 2   │ ← inutilizzato
              ├───────────┤
              │  Ring 3   │ ← USER (limitato)
              └───────────┘
                    │
                    ▼ Meno privilegio
```

Il livello corrente è determinato dai 2 bit bassi del segmento CS. Il kernel imposta CS per decidere il privilegio.

Transizioni possibili:

```
Ring 3 → Ring 0: syscall, interrupt, eccezione
Ring 0 → Ring 3: iretq, sysret
```

## Memoria virtuale

La CPU non accede direttamente alla RAM. Ogni indirizzo passa attraverso le *page table*:

```
Indirizzo virtuale (64 bit)
  ┌────┬────┬────┬────┬──────────┐
  │ L4 │ L3 │ L2 │ L1 │ offset  │
  └─┬──┴─┬──┴─┬──┴─┬──┴────┬────┘
    │    │    │    │       │
    ▼    ▼    ▼    ▼       ▼
  PML4  PDP   PD    PT    pagina
  (512) (512) (512) (512) (4096 byte)
    │     │     │     │
    └─────┴─────┴─────┴──→ RAM (indirizzo fisico)
```

A ogni livello, la CPU prende `n` bit dall'indirizzo virtuale, li usa come indice in una tabella, legge il puntatore al livello successivo, e continua fino a trovare l'indirizzo fisico.

```
indirizzo virtuale                   indirizzo fisico
     0xFFFF800010004000    ────→         0x200000
            │                                ▲
            ▼                                │
      PML4[511] ──→ PDP[0] ──→ PD[0] ──→ PT[4] ──→ frame 0x200000
```

### Perché la memoria virtuale

| Problema | Soluzione |
|----------|----------|
| due processi allo stesso indirizzo | ogni processo ha le proprie page table → isolamento |
| programma vede frammentazione fisica | il kernel mappa memoria contigua virtualmente |
| kernel deve essere protetto da user space | pagine kernel non accessibili da ring 3 |

## Eventi

La CPU genera eventi che interrompono il flusso normale:

| Evento | Causa | Sincrono? |
|--------|-------|-----------|
| eccezione (fault) | divisione per zero, page fault | sì (dovuto a un'istruzione) |
| interrupt | timer, scheda di rete, tastiera | no (arriva quando arriva) |
| syscall | chiamata volontaria da un programma | sì |

Per gestirli, il kernel installa una IDT (Interrupt Descriptor Table):

```
IDT
┌──────┐
│ 0    │ → #DE (division error)
├──────┤
│ 1    │ → #DB (debug)
├──────┤
│ ...  │
├──────┤
│ 13   │ → #GP (general protection)
├──────┤
│ 14   │ → #PF (page fault)
├──────┤
│ ...  │
├──────┤
│ 32   │ → IRQ0 (timer)
├──────┤
│ 33   │ → IRQ1 (tastiera)
├──────┤
│ ...  │
└──────┘
         │
         ▼
     handler: funzione C o assembly
```

## Non ti serve altro (per ora)

Non ti serve sapere come funziona la predizione dei salti, la cache L3, l'esecuzione fuori ordine, la microarchitettura. Quello che conta è il **modello architetturale**: ciò che il software vede.

Aggiungeremo dettagli quando serviranno. Per ora, queste basi bastano a scrivere il primo boot.
