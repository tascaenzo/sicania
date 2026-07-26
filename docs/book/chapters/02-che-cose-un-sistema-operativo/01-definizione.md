# 1. Che cos'è un sistema operativo

La domanda sembra semplice, ma "sistema operativo" significa cose diverse a seconda di chi parla.

Per chi usa il computer, è Windows o macOS. Per uno sviluppatore, è il kernel più le librerie di sistema. Per un progetto come Sicania, dobbiamo essere più precisi.

## Definizione operativa

Un sistema operativo è il livello software che:

1. **gestisce le risorse hardware** (CPU, memoria, dispositivi)
2. **costruisce astrazioni** (processi, file, socket) che i programmi possono usare
3. **applica protezione e isolamento** tra attività diverse

Senza sistema operativo, ogni programma dovrebbe parlare direttamente con l'hardware. Conflitti, crash e accessi incontrollati sarebbero la norma.

## Gestore di risorse

L'hardware ha capacità finite: un solo processore, una quantità limitata di RAM, pochi dischi. Il sistema operativo decide chi usa cosa, quando e per quanto tempo.

Questo significa:

- **CPU**: decide quale processo eseguire in ogni istante (scheduling)
- **Memoria**: assegna pagine ai processi e impedisce che leggano memoria altrui
- **Dispositivi**: coordina l'accesso a dischi, rete, tastiera

## Macchina estesa

L'hardware è scomodo da programmare direttamente. Il sistema operativo costruisce astrazioni più semplici:

- un **processo** sembra avere una CPU tutta per sé (ma è il kernel che la condivide)
- un **file** sembra una sequenza di byte (ma è memorizzato su un disco a settori)
- la **memoria virtuale** sembra uno spazio continuo e privato (ma è mappata su pagine fisiche)

## Protezione

Un sistema operativo deve impedire che un programma danneggi un altro o il sistema stesso. Per farlo usa:

- **privilegi**: il kernel gira in modalità privilegiata, i programmi no
- **isolamento**: ogni processo ha il suo spazio di indirizzamento
- **validazione**: ogni richiesta al kernel viene controllata prima di essere eseguita

Queste tre funzioni (risorse, astrazioni, protezione) sono il nucleo di qualunque sistema operativo, incluso Sicania.
