# 8. Build system

Il build system si occupa di compilare i file assembly e C, linkarli insieme e produrre un ELF x86-64 valido per il bootloader.

## Specifiche del build system

Il sistema di build deve:

1. **Compilare l'assembly**: `entry.asm` → `.o` (formato ELF64, oggetto x86-64)
2. **Compilare il C**: `kernel.c`, `serial.c` → `.o` (freestanding, nessuna libreria standard)
3. **Linkare il tutto**: oggetti + linker script → `sicania.elf`
4. **Produrre un ELF eseguibile**: formato ELF64 x86-64, con entry point `_start`

## Opzioni di compilazione C richieste

| Opzione | Motivo |
|---------|--------|
| `-ffreestanding` | nessuna libreria C standard disponibile |
| `-mno-red-zone` | la red zone (128 byte sotto RSP) è pericolosa con interrupt |
| `-mno-mmx -mno-sse -mno-sse2` | non usare istruzioni SIMD/FPU (non salviamo XMM) |
| `-nostdlib` | non linkare libc |
| `-Wall -Wextra` | tutti i warning |
| `-O2` | ottimizzazione (si può usare anche `-Os`) |

## Opzioni di linking richieste

| Opzione | Motivo |
|---------|--------|
| `-T link.ld` | usa il linker script custom |
| `-nostdlib` | non linkare startup code/libc |
| `-z max-page-size=0x1000` | allinea sezioni a 4 KB |

## File coinvolti

| File | Contenuto | Compilatore/Strumento |
|------|-----------|----------------------|
| `arch/x86_64/boot/entry.asm` | entry point assembly | nasm (elf64) |
| `kernel.c` | kernel main | gcc/clang (freestanding) |
| `serial.c` | driver seriale | gcc/clang (freestanding) |
| `serial.h` | header seriale | incluso dal C |
| `arch/x86_64/link.ld` | linker script | ld |

## Output

Il prodotto finale è un file `sicania.elf`, ELF64 x86-64, che Limine carica in memoria.

## Perché Meson

Meson è il build system scelto per Sicania per questi motivi:

- dichiarativo (si descrive cosa compilare, non come)
- genera file Ninja ottimizzati (compilazione parallela)
- usato da progetti reali (systemd, Mesa, Xorg)
- supporto nativo per cross-compilazione
- gestione automatica delle dipendenze

## Alternative

Lo stesso risultato si può ottenere con un Makefile. Meson aggiunge complessità iniziale ma semplifica la gestione quando il progetto cresce (decine di file, cross-compilazione, test automatizzati).

## Relazioni

```
nasm (entry.asm) ─┐
gcc (kernel.c) ───┼─ ld + link.ld → sicania.elf
gcc (serial.c) ───┘
```

## Rischi

- Se `-mno-red-zone` manca, interrupt o eccezioni possono corrompere lo stack
- Se `-nostdlib` manca, il linker cerca funzioni libc inesistenti e fallisce
- Se `-z max-page-size=0x1000` manca, il linker può usare allineamento 2 MB, incompatibile con le page table minime
