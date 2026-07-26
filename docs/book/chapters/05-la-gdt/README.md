# Capitolo 5 — La GDT

## Obiettivo

Specificare i componenti necessari per inizializzare la GDT del kernel, necessaria per il funzionamento della CPU in long mode e per supportare la gestione degli interrupt tramite IST (Interrupt Stack Table).

## Prerequisiti

- [Capitolo 4](../04-il-processo-di-boot-di-sicania/README.md) completato
- kernel che si avvia e stampa sulla seriale
- conoscenza base dei registri segmento x86-64

## Unità

1. [Perché serve la GDT](01-perche-gdt.md)
2. [Formato dei descrittori](02-formato-gdt.md)
3. [Funzioni](03-funzioni-gdt.md)
4. [Verifica](04-verifica-gdt.md)

## Dipendenze

```
GDT
  ├── serve a: CPU (obbligatoria per long mode)
  ├── serve a: IDT (fornisce stack per interrupt via TSS/IST)
  ├── usata da: kernel_main (dopo seriale, prima di IDT)
  └── dipende da: (nessuna struttura kernel, solo CPU)
```
