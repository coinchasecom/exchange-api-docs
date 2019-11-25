# API Access
To protect API communication from unauthorized change, all non-public API calls are required to be signed.

#### Access URLs
- Http API: https://api.coinchase.com<br>
- Websocket Market Data: wss://api.coinchase.com/x/v1/ws

#### Create API Key
- https://coinchase.com/me/user_api

#### Authentication
API requests are highly likely to be tampered with during transmission over the internet. To ensure that requests are not changed, private interfaces other than public interfaces (basic information, market data) must use your API Key for signature authentication to verify Whether the parameter or parameter value has changed during transmission. Each API Key requires appropriate permissions to access the appropriate interface. Every newly created API Key needs to be assigned permissions. The types of permissions are divided into: read, trade, and coin. Before using the interface, please check the permission type of each interface and confirm that your API Key has the appropriate permissions.

A legitimate request consists of the following parts:

- Method request address: access server address api.coinchase.com, such as api.coinchase.com/x/v1/order/buy.

- API Access Key (AccessKey): The Access Key in the API Key you requested.

- Timestamp: The time (UTC time) at which you made the request. Such as: 1494490926. Including this value in the query request helps prevent third parties from intercepting your request.

- Mandatory and optional parameters: Each method has a set of required and optional parameters for defining API calls. You can view these parameters and their meanings in the description of each method. Please note that for GET requests, the parameters that come with each method need to be signed. For POST requests, the parameters of each method are not signed, that is, only the AccessKeyId and SignatureMethod are required for the POST request. , SignatureVersion, Timestamp four parameters, other parameters are placed in the body.

- Signature: The value calculated by the signature to ensure that the signature is valid and has not been tampered with.


# Market Data (Public)
`base currency` refers to the asset that is the `volume` of a symbol.<br>
`quote currency` refers to the asset that is the `price` of a symbol.<br>
`amount=price*volume`, `amount` measured by `quote currency`.
#### Exchange information
Current exchange trading rules and symbol information

**Method:**
GET

**Path:**
/x/v1/public/exchangeInfo

**Request Parameters:**
None

**Response:**
```
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
#### Recent trades list
Get recent trades (up to last 500).

**Method:**
GET

**Path:**
/x/v1/public/trades

**Request Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
pair | STRING | YES |
limit | INT | NO | Default 100; max 500.

**Response:**
```
Example: /x/v1/public/trades?pair=BTC/USDT&limit=1

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

#### order book

**Method:**
GET

**Path:**
/x/v1/public/orderbook

**Request Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
pair | STRING | YES |
limit | INT | NO | Default 10 Valid limits:[10, 100]

**Response:**
```
Example: /x/v1/public/orderbook?pair=BTC/USDT

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
#### 24hr ticker price change statistics

**Method:**
GET

**Path:**
/x/v1/public/ticker/24hr

**Request Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
pair | STRING | NO |

**Response:**
```
Example: /x/v1/public/ticker/24hr?pair=BTC/USDT

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
```
Example: /x/v1/public/ticker/24hr

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

# Trading (Private)
