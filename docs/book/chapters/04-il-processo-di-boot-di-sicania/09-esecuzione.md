# 9. Eseguire con QEMU

Abbiamo il kernel compilato. Ora lo avviamo.

## Boot rapido (ISO)

Per creare un'immagine ISO avviabile con Limine:

```bash
# Prepara la directory per l'ISO
mkdir -p iso/boot
cp build/kernel/sicania.elf iso/boot/
cp limine.conf iso/

# Crea l'ISO
xorriso -as mkisofs -b limine-cd.bin \
    -no-emul-boot -boot-load-size 4 -boot-info-table \
    -o sicania.iso iso/

# Installa il bootloader Limine sull'ISO
./limine-install sicania.iso
```

I file `limine-cd.bin` e `limine-install` si trovano nella directory di Limine che hai scaricato.

## Avviare con QEMU

```bash
qemu-system-x86_64 -cdrom sicania.iso -serial stdio -m 256M
```

Se tutto funziona, vedrai:

```
Sicania: kernel avviato con successo!
```

## Spiegazione delle opzioni QEMU

- **`-cdrom sicania.iso`**: avvia dall'ISO appena creato
- **`-serial stdio`**: reindirizza la porta seriale sul terminale. È il nostro "schermo" per ora.
- **`-m 256M`**: dà 256 MB di RAM alla macchina virtuale

## Script rapido

Crea un file `run.sh` per non dover riscrivere i comandi:

```bash
#!/bin/bash
make -C build
mkdir -p iso/boot
cp build/kernel/sicania.elf iso/boot/
cp limine.conf iso/
xorriso -as mkisofs -b limine-cd.bin \
    -no-emul-boot -boot-load-size 4 -boot-info-table \
    -o sicania.iso iso/ 2>/dev/null
qemu-system-x86_64 -cdrom sicania.iso -serial stdio -m 256M
```

Rendilo eseguibile: `chmod +x run.sh`. Poi basta `./run.sh`.

## Cosa osservare

Il kernel si avvia, inizializza la seriale, stampa il messaggio e poi entra in un loop di `hlt`. La CPU resta ferma, consumando il minimo, in attesa del primo interrupt (che non arriva perché non abbiamo ancora abilitato nessuna fonte di interrupt).

Per uscire da QEMU: premi `Ctrl+A` seguito da `X`, o chiudi la finestra.
