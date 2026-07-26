# 3. Configurazione di Limine

Limine ha bisogno di sapere dove si trova il kernel e con quale protocollo caricarlo. Queste informazioni si scrivono in un file di configurazione.

## Specifiche del file `limine.conf`

| Proprietà | Valore | Descrizione |
|-----------|--------|-------------|
| Timeout | 0 | avvia immediatamente, senza menu interattivo |
| Voce di boot | `Sicania` | nome visualizzato nel menu |
| Protocollo | `limine` | protocollo nativo Limine |
| Percorso kernel | `boot:/sicania.elf` | posizione del kernel nell'immagine |

### TIMEOUT

Il timeout dice a Limine quanto aspettare prima di avviare la voce predefinita. Con `0`, parte subito. In futuro, quando avremo più voci di boot (kernel normale, modalità provvisoria, test), potremo aumentarlo.

### PROTOCOL

Limine supporta più protocolli: `limine` (nativo), `multiboot2`, `stivale`, ecc. Noi usiamo `limine`, il protocollo nativo, che dà accesso a tutte le funzionalità del bootloader (mappa memoria, moduli, framebuffer, ecc.).

### KERNEL_PATH

Indica dove trovare il kernel ELF dentro l'immagine ISO. Il percorso è relativo alla radice dell'immagine. `boot:/sicania.elf` significa che il file `sicania.elf` va messo in `boot/` dentro l'ISO.

### Perché serve

Senza questo file, Limine non sa che cosa caricare. Il bootloader cerca `limine.conf` nella radice del supporto di avvio (ISO, disco, immagine). Se non lo trova, mostra un errore.

## Cosa succede al boot

1. Il firmware (UEFI) carica Limine
2. Limine cerca `limine.conf`
3. Legge la voce `:Sicania`
4. Trova il kernel a `boot:/sicania.elf`
5. Lo carica in memoria, prepara le strutture dati
6. Salta all'entry point `_start`

## Relazioni

```
limine.conf
  └─ dice a Limine dove trovare il kernel
       └─ kernel ELF → caricato in RAM
            └─ entry point _start → eseguito
```

## Rischi

- Se il percorso non corrisponde alla struttura dell'ISO, Limine non trova il kernel
- Se il protocollo non corrisponde a quello atteso dal kernel, il boot fallisce
- Se il timeout è troppo alto, lo sviluppo diventa lento (dovendo aspettare ogni volta)
