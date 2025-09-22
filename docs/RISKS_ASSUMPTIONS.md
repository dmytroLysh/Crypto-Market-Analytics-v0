# Risks & Assumptions — Crypto Market Analytics Pet Project

This document describes potential risks for the project and how to mitigate them.  
It also lists assumptions made during design.

---

## 1. Risks

### R1. API Rate Limits
- **Description:** Free CoinGecko API allows max ~10,000 requests/month.  
- **Impact:** If polling is too frequent, we may exceed the quota.  
- **Mitigation:** 
  - Use a single batched request for all 10 coins.  
  - Fixed polling interval = 5 minutes.  
  - Implement retry with exponential backoff.

---

### R2. API Downtime or Network Failures
- **Description:** External API may be unavailable or respond with errors.  
- **Impact:** Gaps in data, reduced data freshness.  
- **Mitigation:** 
  - Producer retries with backoff.  
  - Use Dead Letter Queue (DLQ) for failed events.  
  - Monitor producer logs.

---

### R3. Schema Drift
- **Description:** API response structure may change (fields added, renamed, removed).  
- **Impact:** Producer may break or produce invalid events.  
- **Mitigation:** 
  - Validate all events against JSON Schema.  
  - Store invalid events in DLQ.  
  - Version contracts (`v1.1`, `v1.2`, …).

---

### R4. Data Gaps and Quality Issues
- **Description:** Missing timestamps, negative prices, null values.  
- **Impact:** Wrong aggregations, broken ML models.  
- **Mitigation:** 
  - Apply Great Expectations / dbt tests in Silver layer.  
  - Reject invalid data.  
  - Document data quality metrics.

---

### R5. Pipeline Component Failures
- **Description:** Kafka, Airflow, or DWH may crash.  
- **Impact:** No ingestion, broken workflows.  
- **Mitigation:** 
  - Run via Docker Compose (easy restart).  
  - Configure Airflow retries.  
  - Keep small dataset → recovery is easy (re-run DAG).

---

### R6. Limited Resources (Local Machine)
- **Description:** Running Kafka + Airflow + Postgres locally may be heavy.  
- **Impact:** High CPU/memory usage, slow development.  
- **Mitigation:** 
  - Use minimal Docker Compose setup.  
  - Disable non-critical services when not needed.  
  - Keep retention small (7 days).

---

## 2. Assumptions

- Free API plan is sufficient for learning purposes.  
- 5-minute polling interval fits into 10,000 requests/month.  
- Project will handle 10 coins initially (scalable to 50).  
- Data volume is small (~200 MB/month) and can be handled by Postgres.  
- UTC timestamps are acceptable for all use cases.  
- No sensitive or PII data → no strict compliance requirements.  
