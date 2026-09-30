# Neo4j & PostgreSQL LDBC SNB Automation

[![Neo4j](https://img.shields.io/badge/Neo4j-5.x-008CC1.svg?logo=neo4j&logoColor=white)](https://neo4j.com/)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-16-336791.svg?logo=postgresql&logoColor=white)](https://www.postgresql.org/)
[![Docker](https://img.shields.io/badge/Docker-Compose-2496ED.svg?logo=docker&logoColor=white)](https://www.docker.com/)
[![LDBC SNB](https://img.shields.io/badge/Benchmark-LDBC_SNB-orange.svg)](https://ldbcouncil.org/benchmarks/snb/)
[![Make](https://img.shields.io/badge/Orchestrator-GNU_Make-black.svg)](https://www.gnu.org/software/make/)

> Automated toolset to provision, sanitize, ingest, and deploy **Neo4j** and **PostgreSQL** databases populated with synthetic graph data from the **LDBC Social Network Benchmark (SNB)** at arbitrary Scale Factors (SF).

---

## 📋 Table of Contents
- [Overview](#-overview)
- [Repository Structure](#-repository-structure)
- [Quick Start: Makefile Workflow (Recommended)](#-quick-start-makefile-workflow-recommended)
- [Alternative: Standalone Bash Workflow](#-alternative-standalone-bash-workflow)
- [How Data Ingestion Works](#-how-data-ingestion-works)
- [Database Access & Credentials](#-database-access--credentials)
- [Related Repositories](#-related-repositories)
- [Author](#-author)

---

## 🎯 Overview

Setting up comparable testbeds across heterogeneous database engines (Graph vs. Relational) is fraught with operational challenges:
- Neo4j bulk importer (`neo4j-admin database import full`) requires exact `:START_ID` and `:END_ID` header tags and pre-calculated node/relationship groupings.
- PostgreSQL requires merging distributed Spark chunk partitions (`part-*.csv`), stripping BI-specific metadata columns, and enforcing rigid foreign key constraints.

This repository automates the entire lifecycle:
1. **Generating raw synthetic data** via the official `ldbc/datagen-standalone` container.
2. **Patching CSV headers** dynamically with zero memory overhead.
3. **Bulk-loading data** into isolated Docker volumes for both Neo4j and PostgreSQL.
4. **Deploying and managing containers** via declarative Compose orchestration.

---

## 📁 Repository Structure

```text
├── README.md                          # Main documentation (English)
│
├── with_makefile/                     # Recommended Makefile-orchestrated setup
│   ├── Makefile                       # High-level commands (setup, generate, build, up, clean)
│   ├── docker-compose.yml             # Container definitions
│   ├── docker-compose-cluster.yml     # Multi-node cluster configuration
│   ├── build-databases.sh             # Master build and ingestion pipeline
│   ├── postgres_prep.py               # PostgreSQL DDL and chunk consolidator
│   ├── patch_headers.py               # Header transformer for Neo4j import
│   ├── neo4j_setup.cypher             # Cypher index constraints
│   └── README.md                      # Detailed Makefile usage instructions
│
└── without_makefile/                  # Pure Bash execution scripts (no make dependency)
    ├── build-databases.sh             # Self-contained build script
    ├── docker-compose.yml             # Standalone Compose file
    ├── postgres_prep.py               # Ingestion preprocessor
    └── README.md                      # Standalone Bash walkthrough
```

---

## 🚀 Quick Start: Makefile Workflow (Recommended)

### 1. Install Prerequisites
Installs Docker, Python3, and required adapters (`psycopg`):
```bash
cd with_makefile
make setup
```

### 2. Generate Synthetic LDBC SNB Data
Generate data using the desired Scale Factor (default: `SF=0.1` ~100MB; `SF=1` ~1GB; `SF=10` ~10GB):
```bash
make generate SF=0.1
```

### 3. Build & Ingest Data into Databases
Patches headers, merges Spark chunks, and executes bulk ingestion into Docker volumes:
```bash
make build SF=0.1
```

### 4. Launch Databases
Starts the containerized services and automatically sets up Neo4j indexes:
```bash
make up SF=0.1
```

### 5. Teardown & Reset
- **Stop containers** (preserves volume data):
  ```bash
  make down
  ```
- **Reset environment** (`down` -> `build` -> `up`):
  ```bash
  make reset SF=0.1
  ```
- **Clean volumes** (keeps raw CSVs):
  ```bash
  make clean
  ```
- **Deep clean** (removes volumes and raw CSV datasets):
  ```bash
  make deep-clean
  ```

---

## 🐚 Alternative: Standalone Bash Workflow

If you prefer executing without GNU Make, use the `without_makefile/` directory:
```bash
cd without_makefile
# Follow step-by-step instructions in without_makefile/README.md
```

---

## ⚙️ How Data Ingestion Works

1. **Neo4j Bulk Import**:
   - `patch_headers.py` adds Neo4j header specifications (`:START_ID`, `:END_ID`, `:TYPE`).
   - Uses an ephemeral container running `neo4j-admin database import full` for high-throughput disk-to-disk ingestion.
   - Applies index constraints via `neo4j_setup.cypher`.
2. **PostgreSQL Bulk Import**:
   - `postgres_prep.py` consolidates Spark partitions (`part-*.csv`) and filters non-interactive columns.
   - Pipes sanitized CSVs into PostgreSQL via `COPY` commands enforcing relational schemas and B-Tree indexes.

---

## 🔌 Database Access & Credentials

| Database | Service | Port | Database Name | Username | Password |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Neo4j** | Graph DBMS | `7687` (Bolt) / `7474` (HTTP Browser) | `neo4j` | `neo4j` | `password` |
| **PostgreSQL** | Relational DBMS | `5432` | `ldbcsnb` (or `ldbcsf01`) | `postgres` | `mysecretpassword` |

---

## 🔗 Related Repositories

- 📊 **[mbroglio/graph-databases_neo4j_analysis](https://github.com/mbroglio/graph-databases_neo4j_analysis)**: The full benchmark evaluation suite utilizing these automated database instances.

---

## 👤 Author

Developed by **Matteo Broglio** for automated database benchmarking and reproducible data engineering research.
