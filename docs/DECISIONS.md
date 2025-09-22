# Decisions (ADR) — Crypto Market Analytics Pet Project

Below are the key architecture decisions.  
Format of each ADR: **Context → Decision → Consequences**.

---

## ADR-001 — Data Source & Endpoint
**Context.** We need market data for top 10 coins from a free API.  
**Decision.** Use CoinGecko `/api/v3/coins/markets` with parameters  
`vs_currency=usd&ids=<comma-separated-ids>&price_change_percentage=1h,24h,7d`.  
**Consequences.** One request returns all 10 coins; simple field mapping; dependency on their limits/cache.

---

## ADR-002 — Polling Strategy & Quota Guard
**Context.** Free plan allows 10,000 requests/month; 30/minute is not a bottleneck.  
**Decision.** One **batched request every 5 minutes** for all coins. Guardrail logic: if usage >85% of quota → increase interval.  
**Consequences.** ~8,640 requests/month with buffer; “near-RT” is sufficient for a learning project.

---

## ADR-003 — Event Contract & Schema Versioning
**Context.** We need a stable contract between producer and consumers.  
**Decision.** Use **JSON Schema (draft 2020-12)**, file `contracts/event_crypto_price.schema.json`, version `"1.1"`.  
**Consequences.** Easy validation in Python; fast start. Possible migration to Avro + Schema Registry later.

---

## ADR-004 — Required Fields & Time Semantics
**Context.** We have multiple timestamps and currencies.  
**Decision.** Required: `version, source, vs_currency, ts_event, ts_ingested, coin_id, symbol, name, current_price, last_updated`.  
`ts_event = last_updated (UTC)`, `ts_ingested = now() (UTC)`.  
**Consequences.** Clear semantics for freshness/latency; single source of truth = UTC.

---

## ADR-005 — Uniqueness & Idempotency
**Context.** Stable deduplication is required.  
**Decision.** Natural event key = **(`coin_id`, `vs_currency`, `ts_event`)**.  
**Consequences.** Simple idempotent Bronze → Silver loads; convenient for Great Expectations/dbt tests.

---

## ADR-006 — Kafka Usage with 5-minute Polling
**Context.** Polling interval = 5 min, but we want streaming and decoupling.  
**Decision.** Keep **Kafka** as transport and buffer: `crypto.prices.raw` → (consumers: RT metrics/alerts, landing in Lake).  
**Consequences.** Easy extensibility (multiple consumers/jobs), ready to switch to WS/faster stream later.

---

## ADR-007 — Storage Layers & Formats
**Context.** Need fast analytics and efficient storage.  
**Decision.** Lake: **Parquet** (partition by `dt/hour/coin_id`). DWH v0: **Postgres**; ClickHouse as stretch goal.  
**Consequences.** Cheap storage, fast partition scans, simple BI connectivity.

---

## ADR-008 — Topics, Keys & Retention
**Context.** We need transparent Kafka topic structure.  
**Decision.**  
- `crypto.prices.raw` — key: `coin_id`, partitions: 3 (dev), retention: 7d  
- `crypto.metrics.rt` — RT metrics, retention: 3d  
- `crypto.alerts` — threshold-based alerts  
**Consequences.** Stable streaming topology; supports horizontal scaling of consumers.

---

## ADR-009 — Error Handling: Validation & DLQ
**Context.** Data can break (schema drift, nulls, type mismatches).  
**Decision.** JSON Schema validation in producer (first defense line). Invalid events → **DLQ** `crypto.prices.dlq`.  
**Consequences.** Clean `raw` topic for consumers; transparent incident channel; easy inspection of bad records.

---

## ADR-010 — Orchestration & Batch Model
**Context.** Need daily/hourly aggregates and scheduled jobs.  
**Decision.** **Airflow**: DAG Bronze (landing), DAG Silver→Gold (cleaning/aggregates OHLCV), DAG ML (optional). Retry ≥2.  
**Consequences.** Clear dependencies, recovery after failures; simple backfill.

---

## ADR-011 — Data Quality & Monitoring
**Context.** Minimal quality checks required.  
**Decision.** **Great Expectations/dbt tests** in Silver/Gold (price > 0, freshness, unique key, ranges). Basic producer metrics (requests, latency, 429s).  
**Consequences.** Early detection of issues; DQ error reporting; pet-level alerts.

---

## ADR-012 — Security & Secrets
**Context.** No PII, but API keys and DB access exist.  
**Decision.** Store secrets in `.env` (not committed), provide `.env.sample`. No sensitive info in logs.  
**Consequences.** Safe for public repo; simple onboarding.

---

## ADR-013 — IDs Mapping for Assets
**Context.** CoinGecko uses `id`, not `symbol`.  
**Decision.** Explicit ID list:  
`bitcoin, ethereum, ripple, binancecoin, solana, dogecoin, tron, cardano, hyperliquid, sui`.  
**Consequences.** Unambiguous batch request; no symbol collisions.

---

## ADR-014 — Evolution Policy
**Context.** Contract will evolve.  
**Decision.** Minor versions → only add **optional** fields (backward-compatible). Breaking changes → Major version. `CHANGELOG` in `/contracts`.  
**Consequences.** Controlled evolution without breaking consumers; consumers have time to adapt.

---

### How to Add a New ADR
- Create a new section `ADR-XXX: <title>` with three blocks: Context / Decision / Consequences.  
- Link to related ADRs (e.g., schema change → ADR-003/014).  
- Optionally add date and status (Accepted / Proposed / Deprecated).
