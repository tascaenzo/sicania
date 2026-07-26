# 10. Debug con GDB

Quando un kernel non funziona, non hai errori a schermo: la macchina si blocca e basta. GDB ti permette di vedere cosa sta succedendo dentro la CPU.

## Avviare QEMU in modalità debug

```bash
qemu-system-x86_64 -cdrom sicania.iso -serial stdio -m 256M -s -S
```

- **`-s`**: apre una porta GDB su localhost:1234
- **`-S`**: ferma la CPU all'avvio (aspetta che GDB si connetta)

## Connettersi con GDB

In un altro terminale:

```bash
gdb build/kernel/sicania.elf
```

Dentro GDB:

```gdb
target remote localhost:1234
```

Ora hai il controllo completo della macchina virtuale.

## Comandi utili

```gdb
# Continuare l'esecuzione
continue

# Fermarsi
Ctrl+C

# Mettere un breakpoint
break kernel_main

# Vedere lo stack
backtrace

# Vedere i registri
info registers

# Eseguire riga per riga
stepi     # un'istruzione alla volta
next      # una riga di C alla volta

# Stampare variabili
print my_variable

# Esaminare memoria
x/16gx 0x100000    # 16 parole da 8 byte all'indirizzo 0x100000
```

## Sessione tipica

```gdb
$ gdb build/kernel/sicania.elf
(gdb) target remote localhost:1234
(gdb) break kernel_main
(gdb) continue
Breakpoint 1, kernel_main () at kernel.c:10
(gdb) info registers
rsp            0xffffffff80101000   ...
(gdb) next
serial_init();
(gdb) step
(gdb) print/x port[0]
$1 = 0x1
(gdb) continue
```

## Debug seriale

Anche senza GDB, puoi vedere cosa stampa il kernel con `-serial stdio`. Se il kernel si blocca prima di inizializzare la seriale, non vedrai niente. In quel caso, GDB è l'unico modo per capire dove si è fermato.

## Punto di arresto d'emergenza

Se non sai dove sta fallendo il kernel, metti un breakpoint su `_start`:

```gdb
(gdb) break *0xffffffff80100000   # o l'indirizzo del tuo entry point
(gdb) continue
```

Se il breakpoint scatta, il kernel parte. Se no, il problema è prima del kernel (Limine, ISO, ecc.).
