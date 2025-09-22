# Crypto Market Analytics — Data Engineering Pet Project

This is a **Data Engineering pet project** designed to simulate a real-world streaming + batch data platform for cryptocurrency market analytics.  
The goal is to practice end-to-end skills: ingestion, contracts, streaming (Kafka), orchestration (Airflow), data modeling, and BI dashboards.

---

## 🚀 Project Overview
- **Source:** [CoinGecko API](https://www.coingecko.com/en/api/documentation) `/coins/markets`
- **Assets:** Top 10 cryptocurrencies  
  (BTC, ETH, XRP, BNB, SOL, DOGE, TRX, ADA, HYPE, SUI)
- **Pipeline:**
  - Producer polls API every 5 minutes → pushes to Kafka (`crypto.prices.raw`)
  - Validation against JSON Schema (v1.1), invalid events → DLQ
  - Consumers process RT metrics & alerts
  - Airflow DAGs orchestrate batch ingestion into Lake/DWH
  - BI dashboards for exploration and monitoring

---

## 📂 Repository Structure
.
├── contracts/                # Event schemas & examples
│   ├── event_crypto_price.schema.json
│   └── EXAMPLES/
│       ├── example_btc.json
│       └── example_eth.json
├── docs/                     # Documentation
│   ├── PROJECT_CHARTER.md
│   ├── KPI_SLO_SLA.md
│   ├── DATA_SCOPE.md
│   ├── NFR.md
│   ├── RISKS_ASSUMPTIONS.md
│   ├── ESTIMATES.md
│   └── DECISIONS.md
├── infra/                    # Infrastructure setup
│   ├── docker-compose.yml
│   └── .env.sample
├── producer/                 # API fetcher + Kafka producer
│   └── main.py
└── README.md

---

## 📑 Documentation

- [Project Charter](./docs/PROJECT_CHARTER.md) — goal, scope, use cases, in/out.  
- [KPI / SLO / SLA](./docs/KPI_SLO_SLA.md) — freshness, latency, availability, data quality.  
- [Data Scope](./docs/DATA_SCOPE.md) — assets, polling frequency, fields, example payload.  
- [Non-Functional Requirements](./docs/NFR.md) — retention, formats, UTC, security.  
- [Risks & Assumptions](./docs/RISKS_ASSUMPTIONS.md) — risks and mitigations.  
- [Estimates](./docs/ESTIMATES.md) — effort and resource estimates for all epics.  
- [Decisions (ADR)](./docs/DECISIONS.md) — 10+ key architecture decisions.  

---

## 🛠️ Infrastructure

- **Streaming:** Apache Kafka (with ZooKeeper)  
- **Orchestration:** Apache Airflow  
- **Storage:** MinIO (S3-compatible) + Postgres (stretch: ClickHouse)  
- **Contracts:** JSON Schema (v1.1) in `/contracts`  
- **Visualization:** Superset or Metabase (optional)  

---

## 📊 Data Flow

1. **Ingestion:** Producer polls CoinGecko API → Kafka (`crypto.prices.raw`).  
2. **Validation:** JSON Schema ensures contract compliance; invalid events → `crypto.prices.dlq`.  
3. **Streaming:** Consumers calculate RT metrics, anomalies, alerts.  
4. **Batch:** Airflow DAGs write Bronze → Silver → Gold in Lake/DWH.  
5. **BI:** Dashboards query Gold layer.

---

## ⚡ Quick Start

---

✅ Status
	•	Epic 1 (Foundations & Contracts)
	•	Epic 2 (Streaming Ingestion)
	•	Epic 3 (Batch Ingestion & Orchestration)
	•	Epic 4 (Data Warehouse & Modeling)
	•	Epic 5 (Real-Time Analytics & Alerts)
	•	Epic 6 (Visualization & BI)
	•	Epic 7 (ML & Forecasting — Stretch)
 
---
📌 Notes
	•	Data volume: ~90k events/month (~200 MB).
	•	Polling interval: 5 minutes (batched for 10 coins).
	•	Unique event key: (coin_id, vs_currency, ts_event).

---
