# 1. Preparare l'ambiente

Prima di scrivere una riga di kernel, installiamo gli strumenti necessari.

## Toolchain

Il kernel è un programma *freestanding*: nessuna libreria C, nessun runtime. Servono strumenti specifici.

| Strumento | Ruolo | Versione minima |
|-----------|-------|----------------|
| gcc / clang | compilatore C freestanding | 12 / 16 |
| nasm | assembler x86-64 | 2.15 |
| ld (GNU) | linker per ELF64 | binutils 2.38 |
| meson | build system | 1.0 |
| ninja | esecutore build | 1.10 |
| qemu-system-x86 | emulatore di macchina x86-64 | 7.0 |
| xorriso | creatore ISO (serve per avviare) | 1.5 |
| gdb | debugger (opzionale ma utile) | 13 |

## Installazione

**macOS (Homebrew)**

```
brew install nasm qemu meson ninja xorriso gdb
```

**Debian / Ubuntu**

```
apt install build-essential nasm meson ninja-build qemu-system-x86 xorriso gdb
```

**Arch Linux**

```
pacman -S gcc nasm qemu-system-x86 meson ninja xorriso gdb
```

## Scaricare Limine

Limine è il bootloader. Serve per:

1. **compilare**: header `limine.h` con le strutture del protocollo
2. **creare l'immagine**: binario `limine` per installare il bootloader sull'ISO
3. **avviare**: file `limine-cd.bin` per creare un CD avviabile

Scarica l'ultima release da https://github.com/limine-bootloader/limine

```
# Dopo aver scaricato e scompattato:
cd limine-v6.x
make
```

Questo produce `limine.h`, `limine-cd.bin`, `limine-install`.

## Verifica

```
nasm -v               → NASM version 2.15.05
gcc --version         → gcc (GCC) 12.2.0
meson --version       → 1.0.0
qemu-system-x86_64 --version → QEMU emulator version 7.2.0
```

Se vedi i numeri di versione, sei pronto per il capitolo 4.
