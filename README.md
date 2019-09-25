# Public API Endpoints
## Terminology
* `base currency` refers to the asset that is the `volume` of a symbol.
* `quote currency` refers to the asset that is the `price` of a symbol.
* `amount=price*volume`, `amount` measured by `quote currency`.

## Market Data endpoints
### Exchange information
```
GET https://coinchase.com/api/x/public/exchangeInfo
```
Current exchange trading rules and symbol information

**Parameters:**
NONE

**Response:**
```javascript
{
    "timezone": "+08",
    "serverTime": 1567687793952369,
    "pairs":
    [
        {
            "name": "BTC/USDT",
            "base_currency": "BTC",
            "quote_currency": "USDT",
            "base_currency_precision": 6, // decimal precision of base currency
            "quote_currency_precision": 2, // decimal precision of quote currency
            "status": true, // true: trading false: suspend
            "maker_fee": "0.001", // maker fee 0.1%
            "taker_fee": "0.001", // taker fee 0.1%
            "min_price": "0.01",
            "max_price": "10000000000",
            "min_volume": "0.002",
            "max_volume": "10000000000",
            "issue_price": "9970",
            "issue_time": 1569204000
        }
    ]
}
```

### Recent trades list
```
GET https://coinchase.com/api/x/public/trades
```
Get recent trades (up to last 500).

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
pair | STRING | YES |
limit | INT | NO | Default 100; max 500.

**Response:**
```javascript
Example: /api/x/public/trades?pair=BTC/USDT&limit=100

[
  {
      "id": 1169584632568483840,
      "created_at": 1567685680893075,
      "price": "0.092",
      "volume": "2",
      "initiative": 1 // 1: initiative buy, 2: initiative sell
  }
]
```

### order book
```
GET https://coinchase.com/api/x/public/orderbook
```

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
pair | STRING | YES |
limit | INT | NO | Default 10 Valid limits:[10, 100]

**Response:**
```javascript
Example: /api/x/public/orderbook?pair=BTC/USDT

{
  "bids": [
    [
      "4.00000000",     // price
      "431.00000000"    // volume
    ]
  ],
  "asks": [
    [
      "4.00000200",
      "12.00000000"
    ]
  ]
}
```

### 24hr ticker price change statistics
```
GET https://coinchase.com/api/x/public/ticker/24hr
```
24 hour rolling window price change statistics. **Careful** when accessing this with no `pair`.

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
pair | STRING | NO |

* If the pair is not sent, tickers for all pairs will be returned in a map.

**Response:**
```javascript
Example: /api/x/public/ticker/24hr?pair=BTC/USDT

{
    "start_price": "27.84", // Price before 24 hours
    "current_price": "26.87", // current price
    "percent": "-0.0348", // -3.48%
    "high": "27.33", // high price
    "low": "26.75", // low price
    "amount": "4449349.207", // USDT
    "volume": "164679.4", // BTC
    "sort": 3,
    "base_full_name": "base_full_name",
    "count": 7368  // Trades executed count
}
```
OR
```javascript
Example: /api/x/public/ticker/24hr

{
    "BTC/USDT": 
    {
        "amount": "22.64606206",
        "base_full_name": "Bitcoin",
        "current_price": "0.00000038",
        "high": "0.0000004",
        "low": "0.00000037",
        "percent": "-0.0952",
        "sort": 7,
        "start_price": "0.00000042",
        "volume": "59193602",
        "count": 76
    },
    "ETH/USDT": 
    {
        "amount": "1040779.9498851",
        "base_full_name": "Ethereum",
        "current_price": "0.0893",
        "high": "0.0894",
        "low": "0.0891",
        "percent": "0",
        "sort": 1,
        "start_price": "0.0893",
        "volume": "11662164.5",
        "count": 190
    },
    ...
}
```