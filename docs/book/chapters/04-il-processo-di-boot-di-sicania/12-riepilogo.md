# 12. Riepilogo

In questo capitolo hai scritto un kernel funzionante.

Cosa hai creato:

- un file ELF x86-64 che si avvia con Limine
- un entry point in assembly che imposta lo stack
- un kernel in C con driver seriale
- un test automatico che verifica l'avvio

Cosa hai imparato:

- come funziona il boot con Limine
- come si imposta uno stack in assembly
- come si scrive un driver seriale minimale
- come si usa QEMU per eseguire e debugare un kernel
- come si testa automaticamente un sistema operativo

## Prossimi passi

Ora che Sicania si avvia e comunica, possiamo aggiungere:

- logging strutturato con livelli di severità
- un panic che stampa diagnostica e ferma la macchina
- la GDT e la TSS per gestire i livelli di privilegio
- la IDT per gestire eccezioni e interrupt
- il timer per avere una fonte temporale

Ogni passo è un capitolo del libro. Ogni capitolo produce un incremento avviabile, osservabile e testabile.
