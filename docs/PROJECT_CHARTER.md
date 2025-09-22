# Project Charter — Crypto Market Analytics (v0)

## Vision & Goal
Build an end-to-end open-source data pipeline to ingest crypto market data (free APIs),
process it in real time (Kafka + streaming) and batch (Lake/DWH), deliver analytics
(gainers/losers, anomalies, trends) and a basic ML signal, visualized via a dashboard.

## Use-Cases
- Real-time indicators (price, MA, %change), alerts on anomalies.
- Daily/weekly trends, volatility, top gainers/losers.
- Simple next-period prediction / bullish-bearish classification.

## In-Scope
- Market data via free APIs (e.g., CoinGecko). Polling; no WS at v0.
- Kafka topics, Data Lake (Parquet), DWH (Postgres/ClickHouse), Airflow, Superset/Grafana.

## Out of Scope (v0)
- On-chain metrics, trading bots, portfolio PnL — Stretch Goals.

## Deliverables
- Kafka topics + contracts, Lake/DWH schemas, Airflow DAGs, BI dashboard, ML baseline.

## Stakeholders
- Owner/DE: Dmytro Lyshtva ; Users: self/analyst.

## Constraints
- Free API rate limits; local dev via Docker Compose.

## Milestones
- Epic 1 (Charter & Scope) → Epic 3/4 (Infra & Producer) → Epic 5/6/8 (RT + Lake + DWH) → Epic 10/11 (BI + ML)
