# 4. Verifica

Come osservare che la GDT funziona e il TSS è caricato correttamente.

## Verifica con GDB

### 1. Verificare il GDTR

Dopo `gdt_load()`:

```gdb
(gdb) info registers gdtr
gdtr 0xffffffff80101000 0x0027
```

Il primo valore è l'indirizzo base della GDT, il secondo è il limite (39 = 0x27). Se i valori sono zero o spazzatura, `gdt_load()` non è stata chiamata.

### 2. Ispezionare la GDT in memoria

```gdb
(gdb) x/5gx 0xffffffff80101000
0xffffffff80101000:     0x0000000000000000   (NULL)
0xffffffff80101008:     0x00A09A0000000000   (codice ring 0)
0xffffffff80101010:     0x0000920000000000   (dati ring 0)
0xffffffff80101018:     0x0000000000000067   (TSS, primi 8 byte)
0xffffffff80101020:     0x00000000........   (TSS, secondi 8 byte)
```

Decodifica della entry codice:

```
0x00A09A0000000000
  ││││││││││││││││
  │││││││││││││││└── Limit (byte 0-1) = 0x0000
  ││││││││││││││└─── Base (byte 2-3) = 0x0000
  ││││││││││││└──── Base (byte 4) = 0x00
  │││││││││││└───── Access byte = 0x9A
  ││││││││││└────── Flags + Limit alti = 0xA0
  │││││││││└─────── Base (byte 7) = 0x00
  └─────────────── Byte 4-7 (prima parte a 64 bit)

Access byte 0x9A = 10011010
  P=1, DPL=00, S=1, E=1, C=0, R=1, A=0
Flags + Limit = 0xA0 = 10100000
  G=1, D=0, L=1, AVL=0, Limit(4 bit) = 0000
```

### 3. Verificare il TR (Task Register)

```gdb
(gdb) info registers tr
tr 0x18 0xffffffff80101018 0x0067 0x89
```

- `tr` contiene il selector TSS (0x18) dopo `ltr`
- Il secondo valore è l'indirizzo base della TSS
- Il terzo è il limite (0x67 = 103)
- Il quarto (0x89) è l'access byte = TSS disponibile, presente, DPL=0

Se `tr` mostra 0x00 o valori corrotti, `ltr` non è stata eseguita.

### 4. Verificare stack IST

```gdb
(gdb) print tss_entry.ist
$1 = {0, 0xffffffff80102000, 0, 0, 0, 0, 0}
```

IST[0] (IST1) dovrebbe avere l'indirizzo dello stack di emergenza. Gli altri IST sono 0.

## Verifica con la seriale

Aggiungi messaggi di diagnostica nel flusso:

```
Sicania: kernel avviato!
Inizializzo GDT...
GDT: entries OK
GDT: caricata (lgdt eseguita)
GDT: segmenti ricaricati
GDT: TSS caricata (ltr eseguita)
GDT pronta.
```

Se vedi tutti i messaggi, la GDT è funzionante. Se il kernel crasha dopo un messaggio specifico, sai esattamente dove.

## Test provocato

Per verificare che gli stack IST funzionino, puoi causare un double fault volontario:

```c
void test_double_fault(void)
{
    __asm__ volatile("int $0x00");  // #DE (divide error)
    // Se IST funziona, la CPU usa lo stack IST1 per #DE
    // Se IST non funziona, #DE → cerca stack → #DF → cerca stack → TF
}
```

Questo è un test per AFTER aver installato la IDT, ma la verifica della GDT va fatta prima.

## Cosa abbiamo dimostrato

Dopo questo capitolo, il kernel:

1. ha una GDT valida sotto il nostro controllo
2. ha un TSS con uno stack IST per i fault
3. può caricare la propria GDT senza dipendere da Limine
4. è pronto per la IDT

## Rischi non coperti

| Rischio | Perché non lo gestiamo ora |
|---------|--------------------------|
| SMP e GDT per-CPU | Sicania è single-core nel primo boot |
| Segmenti user (ring 3) | Non abbiamo processi user |
| I/O map nella TSS | Non servono permessi porta I/O speciali |
| Modifica dinamica GDT | Non serve finché non abbiamo hotplug CPU |
