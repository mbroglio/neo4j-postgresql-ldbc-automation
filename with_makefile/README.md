# Makefile-Orchestrated Lab Setup (Recommended)

This folder provides a GNU `Makefile` orchestrator to generate LDBC SNB datasets and manage the Docker environment for **Neo4j** and **PostgreSQL**.

---

## 🛠️ Initial Installation

On a fresh Linux machine, install all dependencies (Docker, git, python3, pandas, psycopg, etc.):
```bash
make setup
```

---

## 💻 Available Commands

All primary Makefile targets accept an optional **`SF`** (Scale Factor) argument. If omitted, the default is `SF=0.1`.

### 1. Data Generation (Raw CSV)
Spins up the official `ldbc/datagen-standalone` container to generate raw synthetic graph CSVs into `out-sf<SF>/`:
```bash
make generate SF=0.1
# Or for a ~1GB graph dataset:
make generate SF=1
```

### 2. Database Build & Import
Processes raw CSVs (patches Neo4j headers, merges Spark chunks for PostgreSQL) and ingests the data into dedicated Docker volumes:
```bash
make build SF=0.1
```

### 3. Launch Databases
Starts the containerized services via Docker Compose and automatically applies indexes/constraints:
```bash
make up SF=0.1
```

To stop containers (volume data is preserved):
```bash
make down
```

---

## 🧹 Shortcuts & Housekeeping
- **`make reset SF=0.1`**: Executes `down`, `build`, and `up` in sequence. Ideal for switching scale factors or rebuilding from scratch.
- **`make clean`**: Stops containers and deletes Docker database volumes (data loss inside databases), while preserving raw generated CSVs.
- **`make deep-clean`**: Removes Docker volumes AND deletes the generated `out-sf*/` CSV directories.
