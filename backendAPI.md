# API Documentation

## Overview

This API powers the **Forex Trade Assistant** application. It exposes endpoints for requesting trade signals, trade recommendations, updating trade performance, recording trades, fetching news, and sending notifications. All endpoints are rooted at `/api`.

All requests from third-party clients must include an `x-api-key` header with your assigned key.


## Endpoints

### `GET /api/signal`  
### `POST /api/signal`
Retrieve a trading signal for a single currency pair.

**Query Parameters / Body Fields**
- `pair` (string) – Currency pair (e.g. `EUR/USD`). Use `AUTO` to let the server pick a pair from the selected category.
- `category` (string) – One of `Forex`, `Commodities`, `Indices`, `Stocks`, or `Crypto`. Used when `pair` is `AUTO`.
- `duration` (string) – Candle timeframe such as `1min`, `5min`, `15min`, `30min`, `1h`, or `4h`.
- `balance` (number) – Account balance used to calculate lot size.
- `risk` (number) – Risk percentage (1–5).

Either GET parameters or a JSON body may be used. CORS headers allow cross‑origin requests.

**Response**
```json
{
  "id": 1712345678901,
  "pair": "EUR/USD",
  "duration": "1h",
  "balance": 1000,
  "risk": 2,
  "stake": 0.20,
  "type": "Buy Market",
  "validFor": "4h",
  "entry": 1.12345,
  "sl": 1.12000,
  "tp": 1.13000,
  "reason": "Pair shows bullish momentum on the last candles",
  "confidence": 8.2,
  "robot": "🟢",
  "sentiment": "Go",
  "marketSentiment": "55% bullish and 45% bearish",
  "source": "ai"
}
```

### `POST /api/trade`
Analyze multiple pairs and return ranked trade recommendations.

**Body Fields**
- `balance` (number, required) – Account balance.
- `category` (string, optional) – Limit analysis to pairs within this category. Supported values match `/api/signal`.

**Response**
```json
{
  "recommendations": [
    {
      "id": 1712345678910,
      "pair": "GBP/USD",
      "type": "Sell Limit",
      "entry": 1.25000,
      "sl": 1.26000,
      "tp": 1.23500,
      "validFor": "4h",
      "confidence": 7.5,
      "robot": "🟢",
      "sentiment": "Go",
      "stake": 0.30,
      "reason": "News sentiment is bearish and EMA 50 is below EMA 200",
      "marketSentiment": "60% bearish and 40% bullish",
      "source": "ai"
    }
  ]
}
```

### `POST /api/update-trades`
Update open trades with the latest price and calculate profit/loss.

**Body Fields**
- `trades` (array) – Array of trade objects previously returned by `/api/signal` or `/api/trade`.

Each trade object must contain `pair`, `entry`, and `stake`. The response returns the same objects with `current` price and `pnl` fields added.

### `POST /api/journal`
Record a trade or update its result.

**Body Fields**
- `action` – `"append"` to add a trade or `"update"` to record a result.
- `trade` – Trade object when `action` is `append`.
- `id` – Trade ID when updating.
- `status` – `"won"` or `"lost"` when updating.

### `POST /api/deriv`
Submit a committed trade directly to Deriv.

The server forwards the trade using Deriv's WebSocket API.

**Body Fields**
- `trade` – Trade object returned by `/api/signal` or `/api/trade`.
 The response confirms the trade was accepted.

### `POST /api/notify`
Send a plain text message to a Telegram chat.

**Body Fields**
- `text` (string) – Message to send.

### `GET /api/news`
Fetch recent news headlines for a query.

**Query Parameters**
- `q` (string) – Search term (e.g. `EURUSD`).

**Response**
```json
{
  "news": [
    { "title": "Market update", "url": "https://example.com" }
  ]
}
```

## Usage Notes
- Trade recommendations use a confidence score from 1–10. Values ≥7 are considered a "Go" signal.
- All numeric prices are in the quote currency (e.g. USD). Lot sizes are computed from `balance` and `risk`.

## Example Workflow
1. POST `/api/signal` with a selected `pair`, `duration`, and risk settings.
2. Display the returned trade on the website's form for user confirmation.
3. When the user clicks **Commit**, send the trade to `/api/deriv` and record it in `/api/journal`.
4. Periodically call `/api/update-trades` to refresh P/L.

## Front-end Usage Example

```js
async function getSignal() {
  const res = await fetch("https://your-domain.com/api/signal?pair=EUR/USD&duration=1h&balance=1000&risk=2", {
    headers: { 'x-api-key': 'your_app_key' }
  });
  const trade = await res.json();
  console.log(trade);
}
```


This documentation helps integrate the API into a separate front-end website. Any combination of pair, category, duration and risk is supported via `/api/signal`.
