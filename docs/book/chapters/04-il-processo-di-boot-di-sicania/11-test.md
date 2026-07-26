# 11. Test automatico del boot

Vogliamo un test che verifichi automaticamente che il kernel si avvii e stampi il messaggio corretto.

## Test con QEMU in modalità non interattiva

QEMU può eseguire in modalità test e uscire con un codice specifico:

```bash
#!/bin/bash
# test_boot.sh

qemu-system-x86_64 -cdrom sicania.iso \
    -serial stdio \
    -m 256M \
    -display none \
    -no-reboot \
    -d cpu_reset 2>&1 | grep -q "Sicania: kernel avviato"
```

- **`-display none`**: nessuna finestra grafica
- **`-no-reboot`**: non riavviare in caso di panic (QEMU si ferma)
- **`grep -q`**: controlla se il messaggio appare nell'output seriale

Se il test trova il messaggio, esce con 0 (successo). Altrimenti con 1 (fallimento).

## Versione con timeout

Avviare un ISO richiede tempo. Aggiungiamo un timeout per sicurezza:

```bash
#!/bin/bash
# test_boot.sh
set -euo pipefail

timeout 10 qemu-system-x86_64 -cdrom sicania.iso \
    -serial stdio \
    -m 256M \
    -display none \
    -no-reboot \
    2>&1 | grep -q "Sicania: kernel avviato con successo"

echo "TEST PASSATO: kernel avviato correttamente"
```

## Cosa verifica il test

1. Limine carica il kernel
2. L'entry point assembly parte
3. `kernel_main()` viene eseguito
4. La seriale viene inizializzata
5. Il messaggio viene stampato correttamente

Se una di queste cose non funziona, il test fallisce.

## Integrazione nel build system

Puoi aggiungere un target `test` a Meson. In `kernel/meson.build`:

```python
run_target('test',
    command: ['../test_boot.sh'],
    depends: [elf],
)
```

Poi:

```bash
meson setup build
ninja -C build test
```

## Cosa abbiamo dimostrato

Con meno di 100 righe di codice, abbiamo un kernel che:

1. Viene caricato da un bootloader vero (Limine)
2. Inizializza l'hardware (porta seriale)
3. Produce output osservabile
4. Può essere eseguito, debugato e testato automaticamente

Questa è la base su cui costruiremo tutto il resto.
