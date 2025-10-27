# Master API Reference - OpenAlgo Trading Platform

## Overview

OpenAlgo API is a comprehensive REST API that enables you to build custom trading applications with multi-broker integration. This master reference contains all endpoints, parameters, and examples you need to create a complete trading platform.

**Base URL Formats:**
- Local: `http://127.0.0.1:5000/api/v1/`
- Ngrok: `https://<your-ngrok-domain>.ngrok-free.app/api/v1/`
- Custom: `https://<your-custom-domain>/api/v1/`

**Authentication:** All endpoints require an API key sent in the request body.

---

## Quick Start Guide

### 1. Test Connection
Use `/ping` to verify your API key and connection.

### 2. Check Account
Use `/funds`, `/holdings`, `/positionbook` to get account details.

### 3. Place Orders
Use `/placeorder` for basic orders, `/placesmartorder` for position-aware orders, `/basketorder` for multiple orders.

### 4. Get Market Data
Use `/quotes` for current prices, `/depth` for order book, `/history` for historical data.

### 5. Manage Orders
Use `/modifyorder`, `/cancelorder`, `/closeposition` to manage active trades.

---

## Table of Contents

1. [Account APIs](#account-apis) - Check connectivity, funds, positions, holdings
2. [Order APIs](#order-apis) - Place, modify, cancel orders
3. [Data APIs](#data-apis) - Market data, quotes, historical data
4. [WebSocket API](#websocket-api) - Real-time market data streaming
5. [Constants & Configuration](#constants--configuration) - Valid values for parameters
6. [Error Handling](#error-handling) - HTTP status codes and error responses
7. [Rate Limiting](#rate-limiting) - API request limits

---

## Account APIs

### Ping - Test Connectivity

**Endpoint:** `POST /api/v1/ping`

**Purpose:** Verify API connectivity and validate your API key.

**Request:**
```json
{
  "apikey": "your_api_key"
}
```

**Response:**
```json
{
  "status": "success",
  "data": {
    "message": "pong",
    "broker": "upstox"
  }
}
```

**Use Case:** Check if API is reachable and your key is valid before making other calls.

---

### Funds - Get Account Balance

**Endpoint:** `POST /api/v1/funds`

**Purpose:** Retrieve available cash, margin, and utilization details from your trading account.

**Request:**
```json
{
  "apikey": "your_api_key"
}
```

**Response:**
```json
{
  "status": "success",
  "data": {
    "availablecash": "18083.01",
    "collateral": "0.00",
    "m2mrealized": "0.00",
    "m2munrealized": "0.00",
    "utiliseddebits": "0.00"
  }
}
```

**Fields:**
- `availablecash`: Money available for trading
- `collateral`: Pledged securities value
- `m2mrealized`: Realized profit/loss
- `m2munrealized`: Unrealized profit/loss
- `utiliseddebits`: Margin utilized

---

### Holdings - Get Stock Holdings

**Endpoint:** `POST /api/v1/holdings`

**Purpose:** Get all stocks you own (delivery positions).

**Request:**
```json
{
  "apikey": "your_api_key"
}
```

**Response:**
```json
{
  "status": "success",
  "data": {
    "holdings": [
      {
        "symbol": "BSLNIFTY",
        "exchange": "NSE",
        "quantity": 1,
        "product": "CNC",
        "pnl": 3.27,
        "pnlpercent": 13.04
      }
    ],
    "statistics": {
      "totalinvvalue": 32.17,
      "totalholdingvalue": 36.46,
      "totalprofitandloss": 4.29,
      "totalpnlpercentage": 13.34
    }
  }
}
```

**Use Case:** View long-term investments and their current profit/loss.

---

### PositionBook - Get Open Positions

**Endpoint:** `POST /api/v1/positionbook`

**Purpose:** Get all open trading positions (intraday and overnight).

**Request:**
```json
{
  "apikey": "your_api_key"
}
```

**Response:**
```json
{
  "status": "success",
  "data": [
    {
      "symbol": "RELIANCE",
      "exchange": "NSE",
      "product": "MIS",
      "quantity": 1,
      "average_price": "0.00"
    },
    {
      "symbol": "INFY",
      "exchange": "NSE",
      "product": "MIS",
      "quantity": -1,
      "average_price": "0.00"
    }
  ]
}
```

**Notes:**
- Positive quantity = Long position (bought)
- Negative quantity = Short position (sold)

---

### Orderbook - Get Order History

**Endpoint:** `POST /api/v1/orderbook`

**Purpose:** Get all orders placed (pending, executed, rejected).

**Request:**
```json
{
  "apikey": "your_api_key"
}
```

**Response:** Returns list of all orders with status, quantity, price details.

---

### Tradebook - Get Executed Trades

**Endpoint:** `POST /api/v1/tradebook`

**Purpose:** Get all executed trades for the day.

**Request:**
```json
{
  "apikey": "your_api_key"
}
```

**Response:** Returns list of completed trades with execution details.

---

### Analyzer Status - Check Analyzer State

**Endpoint:** `POST /api/v1/analyzerstatus`

**Purpose:** Check if the API analyzer is enabled or disabled.

**Request:**
```json
{
  "apikey": "your_api_key"
}
```

**Response:** Returns current analyzer status.

---

### Analyzer Toggle - Enable/Disable Analyzer

**Endpoint:** `POST /api/v1/analyzertoggle`

**Purpose:** Turn the API analyzer on or off.

**Request:**
```json
{
  "apikey": "your_api_key",
  "enabled": true
}
```

**Response:** Confirms analyzer state change.

---

## Order APIs

### PlaceOrder - Place Single Order

**Endpoint:** `POST /api/v1/placeorder`

**Purpose:** Place a buy or sell order with specified parameters.

**Request:**
```json
{
  "apikey": "your_api_key",
  "strategy": "My Strategy",
  "symbol": "RELIANCE",
  "exchange": "NSE",
  "action": "BUY",
  "quantity": "1",
  "pricetype": "MARKET",
  "product": "MIS",
  "price": "0",
  "trigger_price": "0",
  "disclosed_quantity": "0"
}
```

**Response:**
```json
{
  "status": "success",
  "orderid": "240307000614705"
}
```

**Parameters:**
- `apikey`: Your API key (required)
- `strategy`: Strategy name for tracking (required)
- `symbol`: Trading symbol like "RELIANCE", "NIFTY" (required)
- `exchange`: NSE, NFO, BSE, BFO, CDS, BCD, MCX, NCDEX (required)
- `action`: BUY or SELL (required)
- `quantity`: Number of shares/contracts (required)
- `pricetype`: MARKET, LIMIT, SL, SL-M (optional, default: MARKET)
- `product`: MIS (intraday), CNC (delivery), NRML (futures) (optional, default: MIS)
- `price`: Limit price (optional, default: 0)
- `trigger_price`: Stop loss trigger price (optional, default: 0)
- `disclosed_quantity`: Hidden quantity (optional, default: 0)

**Use Cases:**
- Market orders: Set `pricetype="MARKET"`, `price="0"`
- Limit orders: Set `pricetype="LIMIT"`, `price="desired_price"`
- Stop loss orders: Set `pricetype="SL"`, `price="limit_price"`, `trigger_price="trigger_price"`
- Stop loss market: Set `pricetype="SL-M"`, `trigger_price="trigger_price"`

---

### PlaceSmartOrder - Position-Aware Order

**Endpoint:** `POST /api/v1/placesmartorder`

**Purpose:** Place orders that automatically adjust based on existing positions to match target position size.

**Request:**
```json
{
  "apikey": "your_api_key",
  "strategy": "Smart Strategy",
  "symbol": "IDEA",
  "exchange": "NSE",
  "action": "BUY",
  "quantity": "100",
  "position_size": "200",
  "pricetype": "MARKET",
  "product": "MIS"
}
```

**Response:**
```json
{
  "status": "success",
  "orderid": "240307000616990"
}
```

**How It Works:**

| Current Position | Action | Quantity | Target Position Size | What OpenAlgo Does |
|-----------------|--------|----------|---------------------|-------------------|
| 0 | BUY | 100 | 0 | Buy 100 |
| -100 | BUY | 100 | 100 | Buy 200 (reverse + achieve target) |
| 100 | BUY | 100 | 100 | No action (already at target) |
| 100 | BUY | 100 | 200 | Buy 100 more |
| 0 | SELL | 100 | 0 | Sell 100 |
| +100 | SELL | 100 | -100 | Sell 200 (close + achieve target) |

**Use Case:** Build strategies that maintain specific position sizes without manual position tracking.

---

### BasketOrder - Place Multiple Orders

**Endpoint:** `POST /api/v1/basketorder`

**Purpose:** Place multiple orders in a single API call.

**Request:**
```json
{
  "apikey": "your_api_key",
  "strategy": "Basket Strategy",
  "orders": [
    {
      "symbol": "RELIANCE",
      "exchange": "NSE",
      "action": "BUY",
      "quantity": "1",
      "pricetype": "MARKET",
      "product": "MIS"
    },
    {
      "symbol": "INFY",
      "exchange": "NSE",
      "action": "SELL",
      "quantity": "1",
      "pricetype": "MARKET",
      "product": "MIS"
    }
  ]
}
```

**Response:**
```json
{
  "status": "success",
  "results": [
    {
      "symbol": "RELIANCE",
      "orderid": "24120900343249",
      "status": "success"
    },
    {
      "symbol": "INFY",
      "orderid": "24120900343250",
      "status": "success"
    }
  ]
}
```

**Use Case:** Execute pair trades, spreads, or portfolios in one shot.

---

### SplitOrder - Split Large Orders

**Endpoint:** `POST /api/v1/splitorder`

**Purpose:** Automatically split large orders into smaller chunks to comply with exchange limits or reduce market impact.

**Request:**
```json
{
  "apikey": "your_api_key",
  "strategy": "Split Strategy",
  "symbol": "YESBANK",
  "exchange": "NSE",
  "action": "SELL",
  "quantity": "105",
  "splitsize": "20",
  "pricetype": "MARKET",
  "product": "MIS"
}
```

**Response:**
```json
{
  "status": "success",
  "total_quantity": 105,
  "split_size": 20,
  "results": [
    {
      "order_num": 1,
      "orderid": "24120900343417",
      "quantity": 20,
      "status": "success"
    },
    {
      "order_num": 2,
      "orderid": "24120900343419",
      "quantity": 20,
      "status": "success"
    },
    {
      "order_num": 6,
      "orderid": "24120900343416",
      "quantity": 5,
      "status": "success"
    }
  ]
}
```

**Example:** 105 shares split by 20 = 5 orders of 20 + 1 order of 5

**Use Case:** Trade large quantities without breaching freeze limits or moving the market.

---

### ModifyOrder - Modify Existing Order

**Endpoint:** `POST /api/v1/modifyorder`

**Purpose:** Change price, quantity, or order type of a pending order.

**Request:**
```json
{
  "apikey": "your_api_key",
  "strategy": "Modify Strategy",
  "orderid": "240307000562466",
  "symbol": "RELIANCE",
  "exchange": "NSE",
  "action": "BUY",
  "product": "MIS",
  "pricetype": "LIMIT",
  "price": "2500.00",
  "quantity": "2",
  "trigger_price": "0",
  "disclosed_quantity": "0"
}
```

**Response:**
```json
{
  "status": "success",
  "orderid": "240307000562466"
}
```

**Use Case:** Adjust limit orders as market moves, change quantity, or convert to different order type.

---

### CancelOrder - Cancel Single Order

**Endpoint:** `POST /api/v1/cancelorder`

**Purpose:** Cancel a specific pending order.

**Request:**
```json
{
  "apikey": "your_api_key",
  "strategy": "Cancel Strategy",
  "orderid": "1000000123665912"
}
```

**Response:**
```json
{
  "status": "success",
  "orderid": "1000000123665912"
}
```

**Use Case:** Cancel orders that are no longer needed.

---

### CancelAllOrder - Cancel All Open Orders

**Endpoint:** `POST /api/v1/cancelallorder`

**Purpose:** Cancel all pending orders at once.

**Request:**
```json
{
  "apikey": "your_api_key",
  "strategy": "Emergency Stop"
}
```

**Response:** Confirms all orders cancelled.

**Use Case:** Emergency stop, end of day cleanup, strategy shutdown.

---

### ClosePosition - Close All Positions

**Endpoint:** `POST /api/v1/closeposition`

**Purpose:** Square off all open positions immediately.

**Request:**
```json
{
  "apikey": "your_api_key",
  "strategy": "Close All"
}
```

**Response:**
```json
{
  "status": "success",
  "message": "All Open Positions SquaredOff"
}
```

**Use Case:** Exit all trades quickly, emergency stop, end of day square off.

---

### OrderStatus - Check Order Status

**Endpoint:** `POST /api/v1/orderstatus`

**Purpose:** Get current status of a specific order.

**Request:**
```json
{
  "apikey": "your_api_key",
  "orderid": "240307000614705"
}
```

**Response:** Returns detailed order status (pending, executed, rejected, cancelled).

---

### OpenPosition - Get Open Position for Symbol

**Endpoint:** `POST /api/v1/openposition`

**Purpose:** Get position details for a specific symbol.

**Request:**
```json
{
  "apikey": "your_api_key",
  "symbol": "RELIANCE",
  "exchange": "NSE"
}
```

**Response:** Returns position quantity and details for that symbol.

---

## Data APIs

### Quotes - Get Live Quotes

**Endpoint:** `POST /api/v1/quotes`

**Purpose:** Get current market price and basic data for a symbol.

**Request:**
```json
{
  "apikey": "your_api_key",
  "symbol": "WIPRO",
  "exchange": "NSE"
}
```

**Response:**
```json
{
  "status": "success",
  "data": {
    "ltp": 265.93,
    "open": 269,
    "high": 270,
    "low": 265.32,
    "prev_close": 268.52,
    "volume": 4214304,
    "oi": 106860000,
    "bid": 265.84,
    "ask": 265.92
  }
}
```

**Fields:**
- `ltp`: Last traded price (current price)
- `open`: Opening price of the day
- `high`: Highest price of the day
- `low`: Lowest price of the day
- `prev_close`: Previous day closing price
- `volume`: Total shares/contracts traded
- `oi`: Open interest (for derivatives)
- `bid`: Best bid price
- `ask`: Best ask price

**Use Case:** Get current price before placing orders, monitor price movements.

---

### Depth - Get Market Depth

**Endpoint:** `POST /api/v1/depth`

**Purpose:** Get order book with best 5 bid and ask prices.

**Request:**
```json
{
  "apikey": "your_api_key",
  "symbol": "NIFTY31JUL25FUT",
  "exchange": "NFO"
}
```

**Response:**
```json
{
  "status": "success",
  "data": {
    "ltp": 25741.1,
    "bids": [
      {"price": 25741, "quantity": 150},
      {"price": 25740, "quantity": 375},
      {"price": 25739.9, "quantity": 600}
    ],
    "asks": [
      {"price": 25741.1, "quantity": 3675},
      {"price": 25744.9, "quantity": 150},
      {"price": 25745, "quantity": 600}
    ],
    "totalbuyqty": 789825,
    "totalsellqty": 386175,
    "volume": 3561150,
    "oi": 15056100
  }
}
```

**Use Case:** Analyze liquidity, find best execution prices, understand order flow.

---

### History - Get Historical Data

**Endpoint:** `POST /api/v1/history`

**Purpose:** Get historical OHLC (candlestick) data for backtesting or charting.

**Request:**
```json
{
  "apikey": "your_api_key",
  "symbol": "NIFTY31JUL25FUT",
  "exchange": "NFO",
  "interval": "1m",
  "start_date": "2025-06-26",
  "end_date": "2025-06-27"
}
```

**Response:**
```json
{
  "status": "success",
  "data": [
    {
      "timestamp": 1750909500,
      "open": 25302,
      "high": 25302.1,
      "low": 25272.3,
      "close": 25292,
      "volume": 1042,
      "oi": 5401650
    }
  ]
}
```

**Intervals:** Use `/intervals` API to get supported intervals like "1m", "5m", "15m", "1h", "1d".

**Use Case:** Backtest strategies, create charts, analyze historical patterns.

---

### Intervals - Get Supported Intervals

**Endpoint:** `POST /api/v1/intervals`

**Purpose:** Get list of valid candle intervals for historical data.

**Request:**
```json
{
  "apikey": "your_api_key"
}
```

**Response:** Returns available intervals (1m, 5m, 15m, 1h, 1d, etc.) supported by your broker.

**Use Case:** Check valid intervals before requesting historical data.

---

### Symbol - Get Symbol Details

**Endpoint:** `POST /api/v1/symbol`

**Purpose:** Get detailed information about a trading symbol.

**Request:**
```json
{
  "apikey": "your_api_key",
  "symbol": "RELIANCE",
  "exchange": "NSE"
}
```

**Response:** Returns symbol details, lot size, tick size, instrument type.

---

### Search - Search Symbols

**Endpoint:** `POST /api/v1/search`

**Purpose:** Search for trading symbols by name or partial match.

**Request:**
```json
{
  "apikey": "your_api_key",
  "query": "NIFTY 25000 JUL CE",
  "exchange": "NFO"
}
```

**Response:**
```json
{
  "status": "success",
  "message": "Found 6 matching symbols",
  "data": [
    {
      "symbol": "NIFTY17JUL2525000CE",
      "name": "NIFTY",
      "exchange": "NFO",
      "token": "47275",
      "expiry": "17-JUL-25",
      "strike": 25000,
      "lotsize": 75,
      "instrumenttype": "OPTIDX",
      "tick_size": 0.05
    }
  ]
}
```

**Use Cases:**
- Find option contracts by strike and expiry
- Search equity symbols
- Get instrument tokens
- Verify symbol availability

---

### Expiry - Get Expiry Dates

**Endpoint:** `POST /api/v1/expiry`

**Purpose:** Get available expiry dates for futures and options.

**Request:**
```json
{
  "apikey": "your_api_key",
  "symbol": "NIFTY",
  "exchange": "NFO"
}
```

**Response:** Returns list of expiry dates for the symbol.

**Use Case:** Find available expiries before trading options/futures.

---

### Ticker - Subscribe to Live Updates

**Endpoint:** `POST /api/v1/ticker`

**Purpose:** Get real-time price updates for symbols (uses WebSocket internally).

**See WebSocket API section for detailed streaming implementation.**

---

## WebSocket API

### Real-Time Market Data Streaming

**WebSocket URL:**
- Development: `ws://127.0.0.1:8765`
- Production HTTP: `wss://yourdomain.com/ws`
- Production HTTPS: `wss://sub.yourdomain.com/ws`

**Purpose:** Stream live market data with lower latency than REST API.

### Authentication

**Connect and send authentication:**
```json
{
  "action": "authenticate",
  "api_key": "your_api_key"
}
```

**Response:** Server confirms authentication or closes connection.

---

### Data Modes

| Mode | Name | Data Included |
|------|------|---------------|
| 1 | LTP | Last traded price + timestamp |
| 2 | Quote | OHLC + volume + change % |
| 3 | Depth | Order book (5-50 levels) |

---

### Subscribe to Symbol

**LTP Mode (Mode 1):**
```json
{
  "action": "subscribe",
  "symbol": "RELIANCE",
  "exchange": "NSE",
  "mode": 1
}
```

**Quote Mode (Mode 2):**
```json
{
  "action": "subscribe",
  "symbol": "RELIANCE",
  "exchange": "NSE",
  "mode": 2
}
```

**Depth Mode (Mode 3):**
```json
{
  "action": "subscribe",
  "symbol": "RELIANCE",
  "exchange": "NSE",
  "mode": 3,
  "depth_level": 5
}
```

---

### Data Format

**LTP Update:**
```json
{
  "type": "market_data",
  "mode": 1,
  "topic": "RELIANCE.NSE",
  "data": {
    "symbol": "RELIANCE",
    "exchange": "NSE",
    "ltp": 1424.0,
    "timestamp": "2025-05-28T10:30:45.123Z"
  }
}
```

**Quote Update:**
```json
{
  "type": "market_data",
  "mode": 2,
  "topic": "RELIANCE.NSE",
  "data": {
    "symbol": "RELIANCE",
    "exchange": "NSE",
    "ltp": 1424.0,
    "change": 6.0,
    "change_percent": 0.42,
    "volume": 100000,
    "open": 1415.0,
    "high": 1432.5,
    "low": 1408.0,
    "close": 1418.0,
    "timestamp": "2025-05-28T10:30:45.123Z"
  }
}
```

**Depth Update:**
```json
{
  "type": "market_data",
  "mode": 3,
  "depth_level": 5,
  "topic": "RELIANCE.NSE",
  "data": {
    "symbol": "RELIANCE",
    "exchange": "NSE",
    "ltp": 1424.0,
    "depth": {
      "buy": [
        {"price": 1423.9, "quantity": 50, "orders": 3}
      ],
      "sell": [
        {"price": 1424.1, "quantity": 47, "orders": 2}
      ]
    },
    "timestamp": "2025-05-28T10:30:45.123Z"
  }
}
```

---

### Unsubscribe

```json
{
  "action": "unsubscribe",
  "symbol": "RELIANCE",
  "exchange": "NSE",
  "mode": 2
}
```

---

### Heartbeat

- Server sends `ping` every 30 seconds
- Client must respond with `pong`
- Connection closes if no response

---

### WebSocket Best Practices

1. **Always authenticate first** before subscribing
2. **Handle reconnection**: Re-authenticate and re-subscribe after disconnect
3. **Respond to pings**: Keep connection alive
4. **Use appropriate mode**: Mode 1 for price, Mode 2 for analysis, Mode 3 for liquidity
5. **Limit subscriptions**: Don't subscribe to too many symbols simultaneously

---

## Constants & Configuration

### Exchanges

| Code | Description |
|------|-------------|
| NSE | NSE Equity (stocks) |
| NFO | NSE Futures & Options |
| CDS | NSE Currency |
| BSE | BSE Equity |
| BFO | BSE Futures & Options |
| BCD | BSE Currency |
| MCX | MCX Commodity |
| NCDEX | NCDEX Commodity |

---

### Product Types

| Code | Description | Use Case |
|------|-------------|----------|
| MIS | Margin Intraday Square off | Intraday trading with higher leverage |
| CNC | Cash & Carry | Delivery (hold overnight for equity) |
| NRML | Normal | Futures & Options (can hold overnight) |

---

### Price Types

| Code | Description | When to Use |
|------|-------------|-------------|
| MARKET | Market Order | Immediate execution at best available price |
| LIMIT | Limit Order | Execute at specified price or better |
| SL | Stop Loss Limit | Trigger at stop price, execute at limit price |
| SL-M | Stop Loss Market | Trigger at stop price, execute at market |

---

### Actions

| Code | Description |
|------|-------------|
| BUY | Buy (go long) |
| SELL | Sell (go short or exit long) |

---

## Error Handling

### HTTP Status Codes

| Code | Meaning | What to Do |
|------|---------|------------|
| 200 | Success | Request processed successfully |
| 400 | Bad Request | Check your parameters, fix the request |
| 401 | Unauthorized | Invalid API key, check authentication |
| 403 | Forbidden | No permission, check API key permissions |
| 429 | Rate Limit | Slow down, you're sending too many requests |
| 500 | Server Error | Server issue, retry after some time |

---

### Error Response Format

```json
{
  "status": "error",
  "message": "Description of what went wrong"
}
```

**Common Errors:**
- "Invalid openalgo apikey" → Wrong API key
- "Invalid symbol" → Symbol not found or wrong format
- "Insufficient funds" → Not enough balance
- "Order rejected" → Order parameters invalid
- "Rate limit exceeded" → Too many requests

---

### Best Practices for Error Handling

1. **Always check status**: Look for `"status": "success"` or `"status": "error"`
2. **Log errors**: Save error messages for debugging
3. **Implement retry logic**: Retry on 429 and 500 errors with exponential backoff
4. **Validate before sending**: Check parameters locally before API call
5. **Handle edge cases**: Out of market hours, circuit limits, freeze limits

---

## Rate Limiting

### Why Rate Limits Exist

Rate limits prevent abuse, ensure fair usage, and protect the system from overload.

---

### Rate Limits by API Type

#### Login Rate Limits
- 5 attempts per minute
- 25 attempts per hour

#### Order APIs (place, modify, cancel)
- 10 requests per second
```
/placeorder
/modifyorder
/cancelorder
```

#### Smart Order API
- 2 requests per second (more complex processing)
```
/placesmartorder
```

#### General APIs (market data, account info)
- 50 requests per second
```
/quotes, /depth, /history, /funds, /holdings, etc.
```

#### Webhook APIs
- 100 requests per minute
```
/strategy/webhook/*
/chartink/webhook/*
```

#### Strategy Management
- 200 requests per minute
```
/strategy/new, /strategy/delete, etc.
```

---

### What Happens When Limit Exceeded

**Response:**
```
HTTP 429 Too Many Requests
Retry-After: <seconds>
```

The server tells you how long to wait before retrying.

---

### How to Stay Within Limits

1. **Batch requests**: Use `/basketorder` instead of multiple `/placeorder`
2. **Cache data**: Don't fetch same data repeatedly
3. **Use WebSocket**: For real-time data instead of polling REST APIs
4. **Implement queue**: Queue orders and send with delays
5. **Exponential backoff**: On 429, wait, then retry with increasing delays

---

### Configuration

Rate limits can be customized in `.env` file:
```env
API_RATE_LIMIT="50 per second"
ORDER_RATE_LIMIT="10 per second"
SMART_ORDER_RATE_LIMIT="2 per second"
WEBHOOK_RATE_LIMIT="100 per minute"
STRATEGY_RATE_LIMIT="200 per minute"
```

---

## Building a Trading Platform - Complete Workflow

### Step 1: Setup & Authentication

```python
import requests

API_KEY = "your_api_key"
BASE_URL = "http://127.0.0.1:5000/api/v1"

# Test connection
def test_connection():
    response = requests.post(f"{BASE_URL}/ping", 
                           json={"apikey": API_KEY})
    return response.json()
```

---

### Step 2: Get Account Information

```python
def get_funds():
    response = requests.post(f"{BASE_URL}/funds",
                           json={"apikey": API_KEY})
    return response.json()

def get_positions():
    response = requests.post(f"{BASE_URL}/positionbook",
                           json={"apikey": API_KEY})
    return response.json()
```

---

### Step 3: Get Market Data

```python
def get_quote(symbol, exchange):
    response = requests.post(f"{BASE_URL}/quotes",
                           json={
                               "apikey": API_KEY,
                               "symbol": symbol,
                               "exchange": exchange
                           })
    return response.json()

def search_symbol(query, exchange):
    response = requests.post(f"{BASE_URL}/search",
                           json={
                               "apikey": API_KEY,
                               "query": query,
                               "exchange": exchange
                           })
    return response.json()
```

---

### Step 4: Place Orders

```python
def place_order(symbol, exchange, action, quantity):
    response = requests.post(f"{BASE_URL}/placeorder",
                           json={
                               "apikey": API_KEY,
                               "strategy": "My Strategy",
                               "symbol": symbol,
                               "exchange": exchange,
                               "action": action,
                               "quantity": str(quantity),
                               "pricetype": "MARKET",
                               "product": "MIS"
                           })
    return response.json()

# Example: Buy 1 share of RELIANCE
result = place_order("RELIANCE", "NSE", "BUY", 1)
print(f"Order ID: {result['orderid']}")
```

---

### Step 5: Monitor Positions

```python
def monitor_position(symbol, exchange):
    response = requests.post(f"{BASE_URL}/openposition",
                           json={
                               "apikey": API_KEY,
                               "symbol": symbol,
                               "exchange": exchange
                           })
    return response.json()
```

---

### Step 6: Exit Trades

```python
def close_all_positions():
    response = requests.post(f"{BASE_URL}/closeposition",
                           json={
                               "apikey": API_KEY,
                               "strategy": "Emergency Exit"
                           })
    return response.json()

def cancel_order(orderid):
    response = requests.post(f"{BASE_URL}/cancelorder",
                           json={
                               "apikey": API_KEY,
                               "strategy": "Cancel",
                               "orderid": orderid
                           })
    return response.json()
```

---

### Complete Strategy Example

```python
import requests
import time

class TradingBot:
    def __init__(self, api_key, base_url):
        self.api_key = api_key
        self.base_url = base_url
    
    def test_connection(self):
        """Test if API is working"""
        response = requests.post(f"{self.base_url}/ping",
                               json={"apikey": self.api_key})
        return response.json()["status"] == "success"
    
    def get_price(self, symbol, exchange):
        """Get current price of symbol"""
        response = requests.post(f"{self.base_url}/quotes",
                               json={
                                   "apikey": self.api_key,
                                   "symbol": symbol,
                                   "exchange": exchange
                               })
        data = response.json()
        if data["status"] == "success":
            return data["data"]["ltp"]
        return None
    
    def place_order(self, symbol, exchange, action, quantity):
        """Place market order"""
        response = requests.post(f"{self.base_url}/placeorder",
                               json={
                                   "apikey": self.api_key,
                                   "strategy": "Simple Bot",
                                   "symbol": symbol,
                                   "exchange": exchange,
                                   "action": action,
                                   "quantity": str(quantity),
                                   "pricetype": "MARKET",
                                   "product": "MIS"
                               })
        return response.json()
    
    def get_position(self, symbol, exchange):
        """Get current position quantity"""
        response = requests.post(f"{self.base_url}/openposition",
                               json={
                                   "apikey": self.api_key,
                                   "symbol": symbol,
                                   "exchange": exchange
                               })
        data = response.json()
        if data["status"] == "success" and data["data"]:
            return data["data"][0].get("quantity", 0)
        return 0
    
    def run_simple_strategy(self):
        """Example: Buy when price drops, sell when it rises"""
        symbol = "RELIANCE"
        exchange = "NSE"
        
        # Test connection
        if not self.test_connection():
            print("API connection failed")
            return
        
        # Get current price
        price = self.get_price(symbol, exchange)
        print(f"Current price: {price}")
        
        # Check position
        position = self.get_position(symbol, exchange)
        print(f"Current position: {position}")
        
        # Simple logic: if no position, buy 1 share
        if position == 0:
            result = self.place_order(symbol, exchange, "BUY", 1)
            if result["status"] == "success":
                print(f"Buy order placed: {result['orderid']}")
        else:
            print("Already have position")

# Usage
bot = TradingBot(
    api_key="your_api_key",
    base_url="http://127.0.0.1:5000/api/v1"
)
bot.run_simple_strategy()
```

---

## Advanced Topics

### Using Smart Orders for Position Management

```python
def smart_order(symbol, exchange, target_qty):
    """
    Automatically adjust position to target quantity
    Positive target = Long position
    Negative target = Short position
    Zero target = Close position
    """
    response = requests.post(f"{BASE_URL}/placesmartorder",
                           json={
                               "apikey": API_KEY,
                               "strategy": "Smart Strategy",
                               "symbol": symbol,
                               "exchange": exchange,
                               "action": "BUY" if target_qty >= 0 else "SELL",
                               "quantity": str(abs(target_qty)),
                               "position_size": str(target_qty),
                               "pricetype": "MARKET",
                               "product": "MIS"
                           })
    return response.json()

# Example: Want to hold 100 shares
# Currently have 50, smart order will buy 50 more
smart_order("RELIANCE", "NSE", target_qty=100)

# Example: Want to close position
smart_order("RELIANCE", "NSE", target_qty=0)
```

---

### Using Basket Orders for Pairs Trading

```python
def place_pair_trade(symbol1, symbol2, exchange):
    """Buy one symbol and sell another simultaneously"""
    response = requests.post(f"{BASE_URL}/basketorder",
                           json={
                               "apikey": API_KEY,
                               "strategy": "Pairs Trading",
                               "orders": [
                                   {
                                       "symbol": symbol1,
                                       "exchange": exchange,
                                       "action": "BUY",
                                       "quantity": "1",
                                       "pricetype": "MARKET",
                                       "product": "MIS"
                                   },
                                   {
                                       "symbol": symbol2,
                                       "exchange": exchange,
                                       "action": "SELL",
                                       "quantity": "1",
                                       "pricetype": "MARKET",
                                       "product": "MIS"
                                   }
                               ]
                           })
    return response.json()
```

---

### Historical Data Analysis

```python
def get_historical_data(symbol, exchange, days=30):
    """Get historical candles for analysis"""
    from datetime import datetime, timedelta
    
    end_date = datetime.now().strftime("%Y-%m-%d")
    start_date = (datetime.now() - timedelta(days=days)).strftime("%Y-%m-%d")
    
    response = requests.post(f"{BASE_URL}/history",
                           json={
                               "apikey": API_KEY,
                               "symbol": symbol,
                               "exchange": exchange,
                               "interval": "1d",
                               "start_date": start_date,
                               "end_date": end_date
                           })
    return response.json()

# Calculate moving average
def calculate_sma(data, period=20):
    """Simple Moving Average"""
    closes = [candle["close"] for candle in data]
    if len(closes) < period:
        return None
    return sum(closes[-period:]) / period
```

---

### WebSocket Implementation (Python)

```python
import websocket
import json
import threading

class MarketDataStream:
    def __init__(self, api_key, ws_url="ws://127.0.0.1:8765"):
        self.api_key = api_key
        self.ws_url = ws_url
        self.ws = None
        
    def on_message(self, ws, message):
        """Handle incoming market data"""
        data = json.loads(message)
        if data.get("type") == "market_data":
            symbol = data["data"]["symbol"]
            ltp = data["data"]["ltp"]
            print(f"{symbol}: {ltp}")
    
    def on_error(self, ws, error):
        print(f"Error: {error}")
    
    def on_close(self, ws, close_status_code, close_msg):
        print("Connection closed")
    
    def on_open(self, ws):
        """Authenticate and subscribe when connected"""
        # Authenticate
        auth_msg = {
            "action": "authenticate",
            "api_key": self.api_key
        }
        ws.send(json.dumps(auth_msg))
        
        # Subscribe to symbols
        subscribe_msg = {
            "action": "subscribe",
            "symbol": "RELIANCE",
            "exchange": "NSE",
            "mode": 1
        }
        ws.send(json.dumps(subscribe_msg))
    
    def start(self):
        """Start WebSocket connection"""
        self.ws = websocket.WebSocketApp(
            self.ws_url,
            on_open=self.on_open,
            on_message=self.on_message,
            on_error=self.on_error,
            on_close=self.on_close
        )
        
        # Run in separate thread
        ws_thread = threading.Thread(target=self.ws.run_forever)
        ws_thread.daemon = True
        ws_thread.start()

# Usage
stream = MarketDataStream(api_key="your_api_key")
stream.start()
```

---

## Security Best Practices

### 1. Protect Your API Key
- Never commit API keys to version control
- Use environment variables: `os.getenv("OPENALGO_API_KEY")`
- Don't share API keys publicly

### 2. Use HTTPS in Production
- Always use `https://` URLs in production
- Never send API keys over unencrypted connections

### 3. Implement Proper Error Handling
```python
try:
    response = requests.post(url, json=data, timeout=10)
    response.raise_for_status()
    return response.json()
except requests.exceptions.Timeout:
    print("Request timed out")
except requests.exceptions.RequestException as e:
    print(f"Request failed: {e}")
```

### 4. Rate Limit Your Application
```python
import time

class RateLimiter:
    def __init__(self, max_calls, period):
        self.max_calls = max_calls
        self.period = period
        self.calls = []
    
    def wait_if_needed(self):
        now = time.time()
        # Remove old calls
        self.calls = [c for c in self.calls if now - c < self.period]
        
        if len(self.calls) >= self.max_calls:
            sleep_time = self.period - (now - self.calls[0])
            if sleep_time > 0:
                time.sleep(sleep_time)
        
        self.calls.append(time.time())

# Usage: Max 10 orders per second
limiter = RateLimiter(max_calls=10, period=1)

for order in orders:
    limiter.wait_if_needed()
    place_order(order)
```

### 5. Validate Data Locally
```python
def validate_order(symbol, action, quantity):
    """Validate order before sending to API"""
    if action not in ["BUY", "SELL"]:
        raise ValueError("Action must be BUY or SELL")
    
    if quantity <= 0:
        raise ValueError("Quantity must be positive")
    
    if not symbol or not symbol.isalnum():
        raise ValueError("Invalid symbol")
    
    return True
```

---

## Troubleshooting Guide

### Problem: "Invalid API key"
**Solution:** 
- Verify API key is correct
- Check if API key is active
- Ensure no extra spaces in API key

### Problem: "Rate limit exceeded"
**Solution:**
- Slow down requests
- Implement rate limiting in your code
- Use WebSocket for real-time data instead of polling

### Problem: "Order rejected"
**Solution:**
- Check if market is open
- Verify sufficient funds
- Check if symbol is correct
- Validate order parameters

### Problem: "Symbol not found"
**Solution:**
- Use `/search` API to find correct symbol
- Check exchange is correct
- Verify symbol format (use uppercase)

### Problem: WebSocket disconnects
**Solution:**
- Implement reconnection logic
- Respond to ping messages
- Check network stability
- Re-authenticate after reconnect

### Problem: Historical data returns empty
**Solution:**
- Check date range is valid
- Verify interval is supported (use `/intervals`)
- Ensure market was open on those dates
- Check if symbol existed during that period

---

## API Collections

Pre-built API collections for testing:
- Postman Collection: Available in docs
- Python Examples: See examples above
- cURL Examples: Provided in individual endpoint docs

---

## Summary - Quick Reference

### Essential Endpoints

| Task | Endpoint | Method |
|------|----------|--------|
| Test connection | `/ping` | POST |
| Get balance | `/funds` | POST |
| Get positions | `/positionbook` | POST |
| Get current price | `/quotes` | POST |
| Place order | `/placeorder` | POST |
| Close all positions | `/closeposition` | POST |
| Cancel order | `/cancelorder` | POST |
| Get historical data | `/history` | POST |
| Search symbols | `/search` | POST |

### Parameter Quick Reference

**Required in all requests:** `apikey`

**Common order parameters:**
- `symbol`: Trading symbol
- `exchange`: NSE, NFO, BSE, BFO, CDS, MCX
- `action`: BUY, SELL
- `quantity`: Number (as string)
- `pricetype`: MARKET, LIMIT, SL, SL-M
- `product`: MIS, CNC, NRML

### Response Format

**Success:**
```json
{"status": "success", "data": {...}}
```

**Error:**
```json
{"status": "error", "message": "error description"}
```

---

## Additional Resources

- **Full Documentation:** https://docs.openalgo.in
- **API Collections:** Available in documentation
- **Support:** Community forums and GitHub issues
- **Updates:** Check changelog for API updates

---

## Conclusion

This master API reference contains everything you need to build a complete trading platform with OpenAlgo:

✅ **Account Management** - Monitor funds, positions, holdings
✅ **Order Execution** - Place, modify, cancel orders with multiple strategies
✅ **Market Data** - Real-time quotes, depth, historical data
✅ **Real-Time Streaming** - WebSocket for live market data
✅ **Error Handling** - Proper status codes and error messages
✅ **Rate Limiting** - Fair usage policies
✅ **Security** - Best practices for API key management
✅ **Examples** - Complete working code in Python

**Next Steps:**
1. Get your API key from OpenAlgo platform
2. Test with `/ping` endpoint
3. Try placing a test order with small quantity
4. Build your strategy using the examples
5. Implement proper error handling and rate limiting
6. Deploy your trading bot

**Remember:**
- Always test with small quantities first
- Implement proper risk management
- Monitor your positions regularly
- Keep API keys secure
- Stay within rate limits
- Handle errors gracefully

Happy Trading! 🚀
