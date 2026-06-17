# Neo4j & PostgreSQL LDBC SNB Automation

Questo repository contiene gli script per automatizzare la configurazione, il popolamento e la gestione di database **Neo4j** e **PostgreSQL** in base ai dataset del benchmark [LDBC SNB (Social Network Benchmark)](https://github.com/ldbc/ldbc_snb_datagen_spark).

## Struttura del Repository

L'obiettivo di questo repository è fornire tutto il necessario per mettere in piedi i database con diversi Scale Factor (SF). Per maggiore flessibilità, il codice è stato diviso in due approcci:

- 📂 **`with_makefile/`**: Contiene un'infrastruttura di gestione completa basata su un `Makefile`. È il metodo **raccomandato**, in quanto fornisce dei comandi unificati (`make generate`, `make build`, `make up`, ecc.) per generare i dataset raw, elaborare l'import e gestire l'avvio e spegnimento dei container in un singolo comando.
- 📂 **`without_makefile/`**: Contiene gli script bash originali senza l'orchestrazione del `Makefile`. È l'approccio ideale se si preferisce lanciare manualmente gli script o se si ha bisogno di integrare il setup in una pipeline differente senza appoggiarsi a `make`.

All'interno di ognuna di queste cartelle è presente un `README.md` specifico con le istruzioni dettagliate su come utilizzarli.
