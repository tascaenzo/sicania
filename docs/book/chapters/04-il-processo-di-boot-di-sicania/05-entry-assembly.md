# 5. Entry point assembly

L'entry point è la prima istruzione eseguita dal kernel. È il punto in cui il bootloader salta. Deve essere scritto in assembly perché il C non può impostare lo stack pointer.

## Funzioni

### `_start`
- **Scopo**: punto di ingresso del kernel. Chiamato da Limine.
- **Parametri**: nessuno (RSI contiene boot info secondo il protocollo)
- **Restituisce**: non ritorna mai
- **Operazioni richieste**:
  1. impostare RSP a `stack_top` (stack del kernel)
  2. azzerare DF (`cld`)
  3. chiamare `kernel_main`
  4. se `kernel_main` torna, disabilitare interrupt e fermare la CPU in un loop
- **Dipende da**: Limine che salta all'indirizzo corretto
- **Chiama**: `kernel_main`

### Stack
- **Nome**: `stack_bottom` / `stack_top`
- **Scopo**: area di memoria usata come stack dal codice C
- **Dimensione prevista**: 16 KB
- **Collocazione**: sezione `.bss` (zero-inizializzata dal bootloader)
- **Cresce**: verso il basso (RSP decrementa)

### Convenzioni ABI all'ingresso

| Proprietà | Valore atteso |
|-----------|---------------|
| Modalità CPU | long mode 64 bit |
| Privilegio | ring 0 |
| Interrupt | disabilitati |
| Direction flag | 0 (forward) |
| Paging | attivo, kernel mappato |
| Stack pointer | valido ma temporaneo |
| Boot info | disponibile (protocollo Limine) |

## Perché serve

Il C ha bisogno di uno stack valido. Il bootloader ne fornisce uno temporaneo, ma non appena possibile il kernel deve passare a uno stack proprio (più grande, sotto il suo controllo, con guard page in futuro).

L'entry point è anche il punto in cui si normalizzano i flag della CPU (DF) e si mette la CPU in uno stato noto prima di chiamare codice complesso.

In futuro, l'entry point farà anche:

- salvare il puntatore a boot_info prima che altri registri vengano sovrascritti
- eseguire una validazione minima del contratto di boot
- inizializzare la diagnostica più precoce possibile

## Relazioni

```
_start → kernel_main
        → [panic se kernel_main ritorna]
```

## Rischi

- Stack disallineato: il compilatore genera codice che assume RSP allineato a 16 byte. Se l'allineamento è sbagliato, le istruzioni SSE (future) o le chiamate di funzione possono fallire.
- Direction flag impostato: `cld` va sempre eseguito perché il compilatore assume DF=0.
- Red zone: la red zone (128 byte sotto RSP) non va usata nel kernel perché interrupt ed eccezioni possono sovrascriverla. Il compilatore va configurato con `-mno-red-zone`. Ma questa è una specifica per il compilatore, non per l'entry point.
