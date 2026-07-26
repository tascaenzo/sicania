# Roadmap del libro Sicania

## Obiettivo

Questa roadmap definisce il percorso con cui il libro e il sistema operativo crescono insieme.

Sicania non viene presentato soltanto in una parte finale: accompagna l'intera trattazione come caso di studio reale. Ogni parte introduce la teoria necessaria, confronta le alternative, motiva le scelte del progetto e conduce a un incremento osservabile e verificabile del sistema.

## Parte I — Perché costruire un sistema operativo

- Perché progettare un sistema operativo nel 2026
- Ruolo e responsabilità di un sistema operativo
- Evoluzione dei sistemi operativi
- Struttura di un calcolatore moderno
- Privilegi, protezione e concorrenza
- Architetture monolitiche, microkernel e ibride
- Obiettivi e non-obiettivi di Sicania

**Risultato:** il lettore comprende il problema che Sicania vuole affrontare e i vincoli generali del progetto.

## Parte II — Piattaforma e strumenti

- Architettura x86-64
- Modalità di esecuzione e livelli di privilegio
- Formato ELF e processo di linking
- Compilazione freestanding
- Meson e Ninja
- QEMU come macchina di riferimento
- GDB e debugging a basso livello
- Limine e il contratto con il bootloader

**Risultato:** ambiente riproducibile capace di compilare, avviare e analizzare un programma freestanding.

## Parte III — La nascita di Sicania

- Struttura iniziale del repository
- Entry point del kernel
- Informazioni ricevute da Limine
- Primo kernel minimale
- Output seriale
- Logging iniziale
- Panic e arresto controllato
- Debug del boot con GDB
- Primo test automatico di avvio

**Risultato:** Sicania si avvia in QEMU, produce output seriale riconoscibile e può essere diagnosticato e testato.

## Parte IV — CPU, eccezioni e tempo

- Stato della CPU e contesto di esecuzione
- GDT e TSS
- IDT
- Eccezioni x86-64
- Interrupt hardware
- Controller degli interrupt
- Timer
- Dispatch degli eventi
- Diagnostica dei fault

**Risultato:** il kernel gestisce eccezioni e interrupt in modo controllato e dispone di una sorgente temporale.

## Parte V — Memoria

- Mappa della memoria fisica
- Allocazione delle pagine fisiche
- Paging x86-64
- Spazio virtuale del kernel
- Permessi e protezione
- Allocazione dinamica nel kernel
- Proprietà, ciclo di vita e invarianti dei dati
- Test degli allocatori

**Risultato:** Sicania dispone di una gestione della memoria fisica e virtuale osservabile e verificabile.

## Parte VI — Concorrenza ed esecuzione

- Contesti di esecuzione
- Thread del kernel
- Context switch
- Scheduler
- Preemption e timer
- Sincronizzazione
- Race condition e deadlock
- Processi e spazi di indirizzamento
- Caricamento di programmi ELF

**Risultato:** Sicania esegue più contesti, introduce processi isolati e carica il primo programma utente.

## Parte VII — Confine tra kernel e spazio utente

- Livelli di privilegio
- Entrata e uscita dal kernel
- Syscall
- ABI di Sicania
- Validazione degli argomenti
- Gestione degli errori
- Copia sicura tra spazi di indirizzamento
- Versionamento delle interfacce

**Risultato:** un programma utente comunica con il kernel attraverso un'interfaccia definita e testata.

## Parte VIII — Comunicazione e servizi fondamentali

- Primitive di IPC
- Messaggi, code e notifiche
- Memoria condivisa
- Sincronizzazione tra processi
- Modello dei servizi
- Evoluzione possibile verso un'architettura ibrida

**Risultato:** processi e servizi possono comunicare attraverso primitive esplicite.

## Parte IX — File, dispositivi e persistenza

- Modello dei file
- Namespace e pathname
- Virtual File System
- File descriptor
- Filesystem in memoria
- Storage a blocchi
- Primo filesystem persistente
- Modello dei driver
- Scoperta e inizializzazione dei dispositivi
- Interrupt e trasferimento dei dati nei driver

**Risultato:** Sicania espone file e dispositivi attraverso interfacce uniformi e può conservare dati persistenti.

## Parte X — Networking

- Driver di rete
- Buffer e pacchetti
- Ethernet
- ARP
- IPv4 e IPv6
- ICMP
- UDP e TCP
- Socket e API utente
- Routing, errori e sicurezza

**Risultato:** Sicania comunica in rete tramite una prima API socket utilizzabile dallo spazio utente.

## Parte XI — Sicurezza, affidabilità ed evoluzione

- Modello delle minacce
- Isolamento e privilegi
- Validazione degli input
- Controllo delle risorse
- Autorizzazione
- Hardening della memoria
- Fault containment
- Testing, fuzzing e analisi statica
- Benchmark e ottimizzazione
- Compatibilità e versionamento
- Evoluzione verso architetture ibride o microkernel

**Risultato:** il sistema viene valutato come progetto completo, con limiti, rischi e direzioni evolutive esplicite.

## Appendici

- Riferimento rapido x86-64
- Formato ELF
- ABI e convenzioni di chiamata
- Tabelle di eccezioni e interrupt
- Comandi QEMU e GDB
- Toolchain e ambiente riproducibile
- Glossario
- Bibliografia commentata

## Regola di avanzamento

Una parte non è completa soltanto perché il testo è stato scritto o il codice compila. È completa quando il lettore può:

1. comprendere il problema;
2. ricostruire la decisione progettuale;
3. compilare ed eseguire l'incremento;
4. osservarne il comportamento;
5. verificarlo con test o esperimenti riproducibili.
