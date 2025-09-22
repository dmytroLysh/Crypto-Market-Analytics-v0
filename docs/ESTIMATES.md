# Estimates — Crypto Market Analytics Pet Project

This document provides rough time and resource estimates for all epics of the project.  
The goal is to understand the relative effort required for each stage.  
Estimates are made for a solo developer (1 person).

---

## Epic 1 — Foundations & Contracts
**Goal:** Define scope, contracts, NFRs, minimal infra.

| Task | Estimate |
|------|----------|
| Documentation (charter, scope, NFR, KPI, risks, decisions) | 8h |
| Contracts (schema v1.1 + examples) | 2h |
| Infra skeleton (docker-compose + .env.sample) | 4h |
| Producer PoC (API fetch + validation + Kafka publish) | 4h |
| DLQ setup (basic) | 2h |
| **Total Epic 1** | **20h (~2–3 days)** |

---

## Epic 2 — Streaming Ingestion
**Goal:** Build producer service + Kafka ingestion pipeline.

| Task | Estimate |
|------|----------|
| Producer service (stable, retry logic, logging, monitoring) | 6h |
| Kafka topics creation (`raw`, `metrics.rt`, `alerts`) | 2h |
| Consumer prototype (parsing & validation) | 4h |
| Unit tests & schema validation in pipeline | 2h |
| **Total Epic 2** | **14h (~2 days)** |

---

## Epic 3 — Batch Ingestion & Orchestration
**Goal:** Airflow DAGs for batch ingestion and storage in Lake/DWH.

| Task | Estimate |
|------|----------|
| Airflow setup (DAG skeleton, connections, variables) | 4h |
| Batch DAG for daily aggregates (OHLCV) | 4h |
| Landing → Bronze ingestion | 2h |
| Bronze → Silver (transform, partitioning) | 4h |
| Silver → Gold (aggregates, marts) | 4h |
| **Total Epic 3** | **18h (~2–3 days)** |

---

## Epic 4 — Data Warehouse & Modeling
**Goal:** Create structured DWH with star-schema for analytics.

| Task | Estimate |
|------|----------|
| DWH schema design (stg/ods/dm) | 4h |
| Implement staging tables | 2h |
| Fact & dimension tables (crypto assets, prices) | 4h |
| Data quality checks (GE/dbt tests) | 4h |
| **Total Epic 4** | **14h (~2 days)** |

---

## Epic 5 — Real-Time Analytics & Alerts
**Goal:** Build RT processing (moving averages, %change, anomalies).

| Task | Estimate |
|------|----------|
| Consumer for metrics calculation (MA, %change) | 4h |
| Alerting rules (thresholds, anomalies) | 4h |
| Kafka → Metrics → Alert pipeline | 4h |
| Testing & monitoring | 2h |
| **Total Epic 5** | **14h (~2 days)** |

---

## Epic 6 — Visualization & BI
**Goal:** Dashboards for exploration and monitoring.

| Task | Estimate |
|------|----------|
| Setup Superset/Metabase | 2h |
| Connect to DWH | 2h |
| Create base dashboards (prices, %change, volumes) | 4h |
| Create RT dashboards (moving averages, alerts) | 4h |
| Polish visualizations & documentation | 2h |
| **Total Epic 6** | **14h (~2 days)** |

---

## Epic 7 — ML & Forecasting (Stretch Goal)
**Goal:** Train simple models for price forecasting.

| Task | Estimate |
|------|----------|
| Data prep for ML (Silver/Gold export) | 4h |
| Baseline model (ARIMA/Prophet) | 6h |
| Evaluate metrics (MAPE, RMSE) | 4h |
| Deploy notebook & document results | 2h |
| **Total Epic 7** | **16h (~2 days)** |

---

## Summary of All Epics
| Epic | Hours | Days |
|------|-------|------|
| Epic 1 — Foundations & Contracts | 20h | 2–3 |
| Epic 2 — Streaming Ingestion | 14h | 2 |
| Epic 3 — Batch Ingestion | 18h | 2–3 |
| Epic 4 — Data Warehouse | 14h | 2 |
| Epic 5 — Real-Time Analytics | 14h | 2 |
| Epic 6 — Visualization & BI | 14h | 2 |
| Epic 7 — ML & Forecasting | 16h | 2 |
| **Total** | **110h** | **~15–16 days** |

---

## Assumptions
- Solo developer, 4–6 focused hours/day.  
- Estimates exclude long debugging or deep research.  
- Total timeline for MVP (Epic 1–6) = ~3 weeks of part-time work.  
- ML (Epic 7) is optional stretch.
