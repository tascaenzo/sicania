# 1. x86-64: quello che serve sapere (davvero)

Un processore x86-64 è una macchina a stati. Ha registri, esegue istruzioni, genera eventi. Per scrivere un kernel devi conoscere solo le parti che il kernel usa.

## Registri

La CPU ha 16 registri generali a 64 bit:

```
RAX, RBX, RCX, RDX, RSI, RDI, RBP, RSP
R8,  R9,  R10, R11, R12, R13, R14, R15
```

Più due registri speciali:

- **RIP** (instruction pointer): contiene l'indirizzo della prossima istruzione
- **RFLAGS**: contiene flag di stato (zero, carry, interrupt enable, ecc.)

**RSP** è lo stack pointer. Puntano **RBP** alla base del frame corrente.

## Privilegio

x86-64 ha 4 livelli di privilegio chiamati *ring*. Noi useremo solo:

- **Ring 0** (kernel mode): può tutto
- **Ring 3** (user mode): limitato

Il livello corrente è determinato dai bit CS (code segment). Il kernel imposta il CS per decidere se il codice in esecuzione è privilegiato o no.

## Memoria virtuale

La CPU non accede direttamente alla RAM. Usa la **memoria virtuale**: ogni indirizzo passa attraverso le *page table*, che lo traducono in un indirizzo fisico.

```
indirizzo virtuale → page table → indirizzo fisico → RAM
```

Questo serve a:

- isolare i processi (ognuno ha le sue page table)
- proteggere il kernel (le pagine del kernel sono invisibili ai programmi)
- permettere mapping flessibili (la memoria fisica può essere frammentata)

La tabella ha 4 livelli. Ogni pagina è tipicamente 4096 byte. Le page table sono strutture dati in RAM che il kernel alloca e configura.

## Eventi

La CPU genera eventi che interrompono il flusso normale:

- **eccezioni**: errori sincroni (divisione per zero, page fault, istruzione invalida)
- **interrupt**: eventi asincroni (timer, tastiera, disco)
- **syscall**: richieste volontarie dai programmi utente

Per gestirli, il kernel installa una **IDT** (Interrupt Descriptor Table): una tabella che dice alla CPU quale funzione chiamare per ogni evento.

## Non ti serve altro (per ora)

Non ti serve sapere come funziona la predizione dei salti, la cache L3, l'esecuzione fuori ordine, la microarchitettura. Quello che conta è il **modello architetturale**: ciò che il software vede.

Aggiungeremo dettagli quando serviranno. Per ora, queste basi bastano a scrivere il primo boot.
