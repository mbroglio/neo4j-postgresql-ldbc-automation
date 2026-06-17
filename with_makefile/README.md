# Setup Automazione con Makefile (Raccomandato)

Questa cartella fornisce un orchestratore basato su `Makefile` per generare i dataset LDBC SNB e impostare l'ambiente Docker con **Neo4j** e **PostgreSQL**.

## Installazione Iniziale
Se è la prima volta che utilizzi l'ambiente su questa macchina, puoi installare tutte le dipendenze necessarie (Docker, git, python3, pandas, psycopg, ecc.) eseguendo:
```bash
make setup
```

## Utilizzo dei Comandi

Tutti i comandi principali del `Makefile` accettano un parametro opzionale **`SF`** (Scale Factor), che definisce la mole dei dati. Se non specificato, il valore di default è `SF=0.1`.

### 1. Generazione dei Dati (Raw CSV)
Scarica e avvia il container per la generazione del dataset grezzo LDBC. I dati verranno creati in una cartella `out-sf<SF>/`.
```bash
make generate SF=0.1
# oppure, per dataset da ~3GB:
make generate SF=1
```

### 2. Creazione ed Importazione nei DB (Build)
Elabora i CSV appena generati (ad es. correggendo gli header e formattando le stringhe per PostgreSQL) e carica i dati all'interno dei volumi di Neo4j e PostgreSQL.
```bash
make build SF=0.1
```

### 3. Avvio dei Database
Avvia i container tramite Docker Compose utilizzando i dati appena importati ed applica automaticamente indici e constraint su Neo4j.
```bash
make up SF=0.1
```

Per fermare i container (i dati nei volumi non verranno persi):
```bash
make down
```

### Scorciatoie e Pulizia
- **`make reset SF=0.1`**: Esegue in cascata `down`, `build` e `up`. Molto utile quando si vuole passare rapidamente da uno scale factor all'altro o reimportare i database da zero.
- **`make clean`**: Ferma i container ed elimina i volumi Docker (perdita dei DB), ma mantiene intatti i CSV generati con `make generate`.
- **`make deep-clean`**: Rimuove i volumi Docker e cancella anche i CSV grezzi generati.
