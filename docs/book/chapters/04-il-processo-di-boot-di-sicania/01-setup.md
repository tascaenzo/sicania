# 1. Preparare l'ambiente

Prima di scrivere una riga di kernel, installiamo gli strumenti necessari.

## Cosa ti serve

```
gcc o clang    → compilatore C
nasm           → assembler x86-64
ld             → linker GNU
meson          → build system
ninja          → backend di meson
qemu-system-x86_64 → emulatore
xorriso        → creazione ISO avviabili (facoltativo)
gdb            → debugger
```

## Installazione

**Su macOS con Homebrew:**

```
brew install nasm qemu meson ninja xorriso gdb
```

**Su Debian/Ubuntu:**

```
apt install build-essential nasm meson ninja-build qemu-system-x86 xorriso gdb
```

**Su Arch Linux:**

```
pacman -S gcc nasm qemu-system-x86 meson ninja xorriso gdb
```

## Scaricare Limine

Limine è il bootloader che caricherà il nostro kernel. Serve il binario `limine` per creare immagini avviabili e l'header `limine.h` per le strutture dati.

Vai su https://github.com/limine-bootloader/limine e scarica l'ultima release.

## Verifica

Controlla che tutto funzioni:

```
nasm -v
gcc --version
meson --version
qemu-system-x86_64 --version
```

Se vedi i numeri di versione, sei pronto.
