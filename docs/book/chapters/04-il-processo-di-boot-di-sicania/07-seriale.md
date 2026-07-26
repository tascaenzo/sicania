# 7. Driver seriale

La porta seriale (UART 16550) è il nostro primo e più importante strumento diagnostico. Permette di stampare messaggi dal kernel verso il terminale dell'host, senza bisogno di uno schermo o di driver grafici.

## Funzioni

### `serial_init()`
- **Scopo**: configura la UART 16550 per la comunicazione
- **Parametri**: nessuno
- **Restituisce**: void
- **Operazioni**:
  1. disabilitare interrupt della seriale
  2. impostare baud rate (115200)
  3. configurare formato: 8 bit, nessuna parità, 1 stop bit
  4. abilitare FIFO
  5. attivare DTR e RTS (segnali di controllo modem)
- **Dipende da**: indirizzo I/O `0x3F8` accessibile in QEMU
- **Usata da**: `kernel_main`

### `serial_write_char(c)`
- **Scopo**: scrive un singolo byte sulla porta seriale
- **Parametri**: `c` — carattere da scrivere
- **Restituisce**: void
- **Operazioni**:
  1. attendere che il bit "transmitter holding register empty" sia attivo
  2. scrivere il carattere nel registro dati
- **Dipende da**: `serial_init()` già eseguita
- **Usata da**: `serial_write_string`

### `serial_write_string(str)`
- **Scopo**: scrive una stringa terminata da `\0` sulla seriale
- **Parametri**: `str` — puntatore alla stringa
- **Restituisce**: void
- **Operazioni**:
  1. iterare sulla stringa
  2. chiamare `serial_write_char` per ogni carattere
- **Dipende da**: `serial_init()` già eseguita
- **Usata da**: `kernel_main`

## Indirizzi di I/O della UART

La 16550 occupa 8 porte di I/O a partire da `0x3F8` (prima seriale su QEMU e hardware standard). I registri principali:

| Offset | Nome | Funzione |
|--------|------|----------|
| 0 | DATA | scrittura/lettura byte |
| 1 | IER | interrupt enable |
| 2 | IIR/FCR | interrupt ID / FIFO control |
| 3 | LCR | line control (configura formato) |
| 4 | MCR | modem control |
| 5 | LSR | line status (bit 5 = pronto) |

## Perché la seriale

La seriale è il metodo più semplice e affidabile per ottenere output da un kernel nelle fasi iniziali:

- richiede solo 2 registri (indirizzo base + 8 byte)
- funziona senza interrupt (polling)
- non serve uno scheduler o un filesystem
- QEMU la reindirizza su `stdio` con `-serial stdio`
- GDB può interfacciarsi direttamente con la seriale

## Relazioni

```
serial_init()
  └─ registri UART a 0x3F8

serial_write_string(str)
  └─ serial_write_char(c)
       └─ registro UART offset 0
```

## Rischi

- Il polling (`while (!(port[5] & 0x20))`) blocca la CPU. Questo va bene ora perché abbiamo una sola CPU e niente concorrenza. In futuro, dovremo usare interrupt o timeout.
- La seriale è lenta (rispetto alla RAM). Scrivere molti log può essere un collo di bottiglia.
- L'indirizzo `0x3F8` assume hardware standard. Su macchine reali o virtuali diverse, potrebbe essere a un indirizzo differente.
