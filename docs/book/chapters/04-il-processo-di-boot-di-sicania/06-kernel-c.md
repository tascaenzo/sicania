# 6. Il kernel in C

Il cuore del kernel è una funzione C chiamata dall'assembly. Per ora fa una cosa sola: inizializza la seriale, stampa un messaggio, ferma la CPU.

## Funzioni

### `kernel_main()`
- **Scopo**: entry point C del kernel. Chiamata subito dopo l'entry assembly.
- **Parametri**: nessuno (per ora)
- **Restituisce**: void (non deve mai tornare)
- **Operazioni**:
  1. chiamare `serial_init()` per configurare la UART
  2. chiamare `serial_write_string(...)` per stampare un messaggio di avvenuto avvio
  3. entrare in un loop infinito con `hlt`
- **Dipende da**: stack valido, `.bss` azzerato
- **Chiama**: `serial_init`, `serial_write_string`
- **Usata da**: entry assembly (`_start`)

### Perché un loop con `hlt`?

`hlt` ferma la CPU fino al prossimo interrupt. Consuma meno energia e produce meno calore di un loop `while(1) {}` puro. Quando avremo interrupt (timer, tastiera), la CPU si risveglierà e potremo fare cose utili. Per ora, senza interrupt, la CPU resta ferma per sempre.

### Perché nessun parametro?

Il bootloader passa informazioni di boot (mappa memoria, moduli, ecc.). Per ora le ignoriamo. Quando serviranno, `kernel_main` riceverà un puntatore a `boot_info` e lo userà per inizializzare il resto del kernel.

## Specifiche di sistema richieste

Prima che `kernel_main` venga chiamato, il runtime C deve garantire:

| Condizione | Chi la garantisce |
|------------|-------------------|
| Stack valido | entry assembly |
| `.bss` azzerato | bootloader / linker script |
| GF=0 (direction flag) | entry assembly |
| Interrupt disabilitati | entry assembly |
| Nessuna red zone usata | flag del compilatore |
| Nessun SIMD/FPU usato | flag del compilatore |

## Relazioni

```
_start
  └─ kernel_main
       ├─ serial_init()
       └─ serial_write_string()
            └─ serial_write_char()
```

## Rischi

- Se `.bss` non è azzerato, le variabili globali (come lo stack) contengono dati spazzatura. Il linker script e il bootloader devono garantire l'azzeramento.
- Se `serial_init()` non viene chiamata prima di `serial_write_string()`, la UART non è configurata e i caratteri potrebbero non arrivare a destinazione.
- Se la funzione ritorna, l'assembly deve fermare la CPU. Non c'è un sistema operativo a cui tornare.
