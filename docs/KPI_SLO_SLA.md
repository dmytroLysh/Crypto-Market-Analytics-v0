# KPI / SLO / SLA — Crypto Market Analytics Pet Project

This document defines the learning goals (KPI) and Service Level Objectives (SLO) for the pet project.  
A formal Service Level Agreement (SLA) does not exist — these are internal objectives only.

---

## 1. Freshness
- **Stream (Kafka):** an event appears in Kafka no later than **5 minutes** after the `last_updated` timestamp from the API.  
- **Batch (DWH):** daily aggregates (OHLCV) must be ready by **10:00 UTC the following day** at the latest.

## 2. Latency
- An event that has landed in Kafka must be processed and available in the DWH / dashboard within **1 minute**.

## 3. Availability
- **Pipeline services (Kafka, Airflow, DWH):** available ≥ **90%** of the time on average per week.  
- **Dashboards:** user can view charts ≥ **90%** of the time.

## 4. Data Quality
- Valid events (price > 0, market_cap ≥ 0, correct UTC timestamp) make up ≥ **95%** of all events.  
- Events not matching the contract must go into a DLQ (dead-letter queue).

## 5. Reliability
- Airflow DAGs succeed in ≥ **90%** of runs on average per week.  
- In case of a failure, the task must automatically retry (retry policy enabled).

---

## Note
These targets are realistic for a learning-oriented pet project and do not require production-grade SLA (99.9%).  
They help train Data Engineer thinking: measure, control, and iteratively improve pipeline quality.
