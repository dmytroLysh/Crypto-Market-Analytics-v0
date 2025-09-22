# Non-Functional Requirements (NFR) — Crypto Market Analytics Pet Project

This document defines the non-functional requirements for the data pipeline.

---

## 1. Time & Timezones
- All timestamps stored in **UTC**.
- Event fields:
  - `ts_event` = business time from API (`last_updated`).
  - `ts_ingested` = producer ingestion time.
- Dashboards must display time in UTC (localization is optional).

---

## 2. Data Retention
- **Kafka topics**:
  - `crypto.prices.raw` — retain **7 days** (sufficient for replay/testing).
  - `crypto.metrics.rt` — retain **3 days** (short-term monitoring).
- **Lake/DWH**:
  - Raw (Bronze) data retained indefinitely.
  - Aggregated (Silver/Gold) data can be compacted and partitioned by date/hour.

---

## 3. Storage Formats
- Bronze layer: **JSON** or **Parquet** (append-only).  
- Silver/Gold: **Parquet** for analytics.  
- DWH: **Postgres** for v0 (stretch goal: ClickHouse).  

---

## 4. Partitioning & Performance
- Partition data in Lake/DWH by:
  - `dt` (date),
  - `hour`,
  - `coin_id`.
- Enables efficient filtering and aggregations.

---

## 5. Security
- No PII data (only public crypto prices).
- API keys and credentials stored in `.env` file (never committed to repo).
- `.env.sample` provided for local development.

---

## 6. Reliability & Recovery
- Producer retries with backoff in case of API failures.
- Dead-letter queue (DLQ) for invalid events or schema violations.
- DAGs in Airflow configured with retry policy (min. 2 retries).

---

## 7. Scalability
- Designed to support:
  - Up to **50 coins** without redesign.
  - Polling frequency down to **1 minute** if API limits allow.
- Kafka topics support **multiple partitions** (default: 3) to scale consumers.

---

## 8. Resource Estimates
- Expected data volume:
  - 10 coins × 288 polls/day ≈ 2,880 events/day.
  - ~90,000 events/month.
- Size per event ≈ 1–2 KB.
- Total raw data ≈ 100–200 MB/month (very small footprint).

---

## 9. Tooling & Environment
- **Infrastructure**: Docker Compose (Kafka, ZooKeeper, Postgres, Airflow, MinIO).  
- **Orchestration**: Apache Airflow.  
- **Streaming**: Apache Kafka.  
- **Analytics/BI**: Superset or Metabase.  

---

## 10. Monitoring
- Basic monitoring via Kafka UI and Airflow UI.
- Optional: Prometheus + Grafana (stretch goal).
