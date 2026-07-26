# 4. Il linker script

Il linker script dice a `ld` come organizzare il kernel in memoria: quali sezioni, in che ordine, a quali indirizzi.

## `kernel/arch/x86_64/link.ld`

```ld
OUTPUT_FORMAT(elf64-x86-64)
ENTRY(_start)

SECTIONS
{
    . = 0xFFFFFFFF80000000 + 0x100000;

    .text : {
        *(.text .text.*)
    }

    .rodata : {
        *(.rodata .rodata.*)
    }

    .data : {
        *(.data .data.*)
    }

    .bss : {
        *(COMMON)
        *(.bss .bss.*)
    }
}
```

Cosa fa ogni parte:

- `OUTPUT_FORMAT(elf64-x86-64)`: produciamo un ELF a 64 bit
- `ENTRY(_start)`: il punto di ingresso è il simbolo `_start`
- `. = 0xFFFFFFFF80000000 + 0x100000`: il kernel viene caricato all'indirizzo virtuale `0xFFFFFFFF80100000`. Questo è un indirizzo del *higher half* (la metà alta dello spazio virtuale), tipico dei kernel x86-64. Lo spazio utente starà negli indirizzi bassi, il kernel in quelli alti.
- Le sezioni `.text`, `.rodata`, `.data`, `.bss` contengono rispettivamente codice, costanti, dati e dati zero-inizializzati

Il `.bss` è importante: contiene le variabili globali non inizializzate (come lo stack). Il bootloader deve azzerare questa sezione prima di saltare al kernel.

Limine legge l'ELF del kernel e usa il linker script per capire dove mappare ogni sezione in memoria virtuale.
