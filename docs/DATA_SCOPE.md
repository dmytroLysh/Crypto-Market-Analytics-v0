# Data Scope — Crypto Market Analytics Pet Project

This document defines the scope of data ingestion for the project.

---

## 1. Data Source
- **Provider:** [CoinGecko API](https://www.coingecko.com/en/api/documentation)  
- **Endpoint:** `/api/v3/coins/markets`  
- **Query Parameters:**
  - `vs_currency=usd`
  - `ids=bitcoin,ethereum,ripple,binancecoin,solana,dogecoin,tron,cardano,hyperliquid,sui`
  - `price_change_percentage=1h,24h,7d`

---

## 2. Assets (Coins)
The project monitors the following cryptocurrencies:
1. Bitcoin (`bitcoin`, BTC)  
2. Ethereum (`ethereum`, ETH)  
3. XRP (`ripple`, XRP)  
4. BNB (`binancecoin`, BNB)  
5. Solana (`solana`, SOL)  
6. Dogecoin (`dogecoin`, DOGE)  
7. TRON (`tron`, TRX)  
8. Cardano (`cardano`, ADA)  
9. Hyperliquid (`hyperliquid`, HYPE)  
10. Sui (`sui`, SUI)

---

## 3. Polling Frequency
- **Every 5 minutes** (one batched request for all 10 coins).  
- This ensures:
  - Fits within the free plan limit (10,000 requests/month).  
  - ~8,640 requests/month total usage.  
  - Leaves ~15% headroom for retries.

---

## 4. Fields Collected
From the `/coins/markets` response, the following fields are ingested (one event per coin):

- `id` → `coin_id`  
- `symbol`  
- `name`  
- `image` (URI)  
- `current_price`  
- `market_cap`  
- `market_cap_rank`  
- `fully_diluted_valuation`  
- `total_volume`  
- `high_24h`, `low_24h`  
- `price_change_24h`, `price_change_percentage_24h`  
- `market_cap_change_24h`, `market_cap_change_percentage_24h`  
- `circulating_supply`, `total_supply`, `max_supply`  
- `ath`, `ath_change_percentage`, `ath_date`  
- `atl`, `atl_change_percentage`, `atl_date`  
- `roi` (object or null)  
- `last_updated`  
- `price_change_percentage_1h_in_currency`  
- `price_change_percentage_24h_in_currency`  
- `price_change_percentage_7d_in_currency`

**Additional producer-enriched fields:**
- `version` (schema version, e.g. "1.1")  
- `source` ("coingecko")  
- `vs_currency` (e.g. "usd")  
- `ts_event` (mapped from `last_updated`)  
- `ts_ingested` (UTC timestamp from producer)  

---

## 5. Example Payload (Normalized Event)
Example for Bitcoin:

```json
{
  "version": "1.1",
  "source": "coingecko",
  "vs_currency": "usd",
  "ts_event": "2025-09-22T16:29:42.340Z",
  "ts_ingested": "2025-09-22T16:30:00Z",
  "coin_id": "bitcoin",
  "symbol": "btc",
  "name": "Bitcoin",
  "image": "https://coin-images.coingecko.com/coins/images/1/large/bitcoin.png?1696501400",
  "current_price": 112870,
  "market_cap": 2248935693399,
  "market_cap_rank": 1,
  "fully_diluted_valuation": 2248935693399,
  "total_volume": 61217048203,
  "high_24h": 115674,
  "low_24h": 112293,
  "price_change_24h": -2578.8213134552207,
  "price_change_percentage_24h": -2.23373,
  "market_cap_change_24h": -51173076881.07861,
  "market_cap_change_percentage_24h": -2.22481,
  "circulating_supply": 19923296.0,
  "total_supply": 19923296.0,
  "max_supply": 21000000.0,
  "ath": 124128,
  "ath_change_percentage": -9.11371,
  "ath_date": "2025-08-14T00:37:02.582Z",
  "atl": 67.81,
  "atl_change_percentage": 166272.27136,
  "atl_date": "2013-07-06T00:00:00.000Z",
  "roi": null,
  "last_updated": "2025-09-22T16:29:42.340Z",
  "price_change_percentage_1h_in_currency": 0.12839583785544073,
  "price_change_percentage_24h_in_currency": -2.2337279063537703,
  "price_change_percentage_7d_in_currency": -1.7151559983659204
}
