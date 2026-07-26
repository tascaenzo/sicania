# 2. Struttura del progetto

Creiamo la struttura delle directory e dei file per il nostro kernel.

```
sicania/
├── limine.conf                  # configurazione del bootloader
├── meson.build                  # build system principale
├── meson.cross                  # opzioni cross-compilazione
├── kernel/
│   ├── meson.build
│   ├── kernel.c                 # entry point C
│   ├── serial.c                 # driver seriale
│   ├── serial.h                 # header seriale
│   ├── limine.h                 # strutture del protocollo Limine
│   └── arch/
│       └── x86_64/
│           ├── meson.build
│           ├── boot/
│           │   └── entry.asm    # entry point assembly
│           └── link.ld          # linker script
```

Creala subito:

```
mkdir -p kernel/arch/x86_64/boot
```

Nei prossimi capitoli scriveremo ogni file, nell'ordine in cui vengono usati durante la build:

1. `limine.conf` → dice a Limine dove trovare il kernel
2. `link.ld` → dice al linker come organizzare il kernel in memoria
3. `entry.asm` → il primo codice eseguito
4. `kernel.c` → il kernel vero e proprio
5. `serial.c` / `serial.h` → per stampare messaggi
6. `meson.build` → per compilare tutto
