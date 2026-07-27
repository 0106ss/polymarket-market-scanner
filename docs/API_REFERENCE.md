# Official API reference used by v0.1.0

Verification date: **2026-07-27 (Asia/Shanghai)**.

The implementation was checked against Polymarket's official documentation index and official documentation pages. Direct retrieval of `https://docs.polymarket.com/llms.txt` timed out from the development host during the first attempt; the official indexed pages were still available through the documentation search backend. Live endpoint verification is reported separately and is never replaced by fixture data.

## Gamma API

Base: `https://gamma-api.polymarket.com`

- `GET /markets/keyset`
- Parameters used: `limit` (1–100), `closed=false`, `ascending=false`, and opaque `after_cursor`
- Response: `{ "markets": [...], "next_cursor": "..." }`
- Important fields: `id`, `conditionId`, `question`, `slug`, `category`, `endDate`, `active`, `closed`, `acceptingOrders`, `enableOrderBook`, `negRisk`, `outcomes`, `clobTokenIds`, `liquidityNum`, `volumeNum`, `feesEnabled`
- Arrays may arrive as JSON strings. Token mapping is by the shared outcome/token array index.

The older list endpoint supports offset pagination; v0.1.0 follows the current keyset endpoint because its cursor is stable and offset is explicitly rejected there.

## CLOB public read API

Base: `https://clob.polymarket.com`

- `POST /books`: body is an array of `{ "token_id": "..." }`, up to 500 tokens. Returns `asset_id`, `market`, `timestamp`, `hash`, `bids`, `asks`, `tick_size`, `min_order_size`, `neg_risk`, and `last_trade_price`.
- `GET /markets/{condition_id}`: current CLOB market metadata. Fee descriptor `fd.r` is used only when present and valid.
- No authentication headers or trading endpoints are used.

Official orderbook errors include 400 invalid payload/token, 404 missing orderbook, 429 throttling, and 5xx service failures. The client retries timeouts, network errors, 429, and 5xx at most three attempts with exponential backoff. Other 4xx responses fail immediately.

## Market WebSocket

Endpoint: `wss://ws-subscriptions-clob.polymarket.com/ws/market`

Initial subscription:

```json
{"assets_ids":["TOKEN_ID"],"type":"market","custom_feature_enabled":true}
```

Dynamic subscription messages use `operation: "subscribe"` or `"unsubscribe"` with `assets_ids`. The client sends the text message `PING` every ten seconds without a message and accepts `PONG`. Documented event types are `book`, `price_change`, `last_trade_price`, `tick_size_change`, `best_bid_ask`, `new_market`, and `market_resolved`; the last three require `custom_feature_enabled`.

## Fees

Official taker formula:

```text
fee = shares × feeRate × price × (1 - price)
```

Makers are not charged. Fee-enabled status is market-specific. Fee precision is five decimal places with half-up rounding in this project. A missing or invalid live fee rate produces `FEE_UNKNOWN`; zero is used only when the market explicitly reports fees disabled.

## Geoblock

`GET https://polymarket.com/api/geoblock` returns `blocked`, `ip`, `country`, and `region`. The application deliberately discards the IP before persistence/display. This check is informational because the project cannot trade; no bypass logic exists.

## Rate limits

Official documentation currently lists Cloudflare throttling and, among others, Gamma `/markets` at 300 requests per 10 seconds. This scanner refreshes market discovery at a default 60-second interval, batches books, limits concurrent HTTP work, and backs off on 429.
