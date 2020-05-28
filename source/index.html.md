---
title: Binance API Documentation
language_tabs: # must be one of https://git.io/vQNgJ
  #- shell
  #- javascript
  #- jason

toc_footers:
  - <a href='https://www.binance.com/en/futures'>Binance Futures</a>

includes:

search: true

---


# Change Log



<font size=4>**2020-05-18**</font>

* New parameter `closePosition` for endpoint  `POST /fapi/v1/order`:   
	If a `STOP_MARKET` or `TAKE_PROFIT_MARKET` order with `closePosition=true` is triggered，all of the current long position( if `SELL` order) or current short position( if `BUY` order) will be closed.
	 
* New field `closePosition` in response to endpoints:
	* `POST /fapi/v1/order`
	* `POST /fapi/v1/batchOrders`
	* `GET /fapi/v1/order`
	* `DELETE /fapi/v1/order`
	* `DELETE /fapi/v1/batchOrders`
	* `GET /fapi/v1/openOrder`
	* `GET /fapi/v1/openOrders`
	* `GET /fapi/v1/allOrders`

---


<font size=4>**2020-05-18**</font>

* Some orders that were cancelled/expired will be removed gradually from API endpoints, but they are still available from Web UI.
	* Orders that meet criteria
		* order status is `CANCELED` or `EXPIRED`, **AND** 
		* order has NO filled trade, **AND**
		* created time + 30 days < current time
	* These endpoints are affected:
		* `GET /fapi/v1/order`
		* `GET /fapi/v1/allOrders`

---

<font size=4>**2020-05-15**</font>

* New fields in payloads of  `<symbol>@bookTicker` and `!bookTicker`:
	* `E` for event time
	* `T` for transactiontime

---



<font size=4>**2020-05-14**</font>

* New field `time` for transaction time in response to endpoints：
	* `GET /fapi/v1/ticker/price`
	* `GET /fapi/v1/ticker/bookTicker`
	* `GET /fapi/v1/openInterest`  


---


<font size=4>**2020-05-11**</font>

* New endpoint `POST /fapi/v1/s/countdownCancelAll` to cancel all open orders of the specified symbol at the end of the specified countdown.   
This rest endpoint means to ensure your open orders are canceled in case of an outage. The endpoint should be called repeatedly as heartbeats so that the existing countdown time can be canceled and repalced by a new one.


---



<font size=4>**2020-05-06**</font>

REST

* Endpoint ``GET /fapi/v1/leverageBracket`` is changed as "USER-DATA". It need to be signed, and timestamp is needed. 


WEB SOCKET USER DATA STREAM

* Please notice: event `ACCOUNT_UPDATE` in USER-DATA-STREAM will be pushed with only account balance or relative position when "FUNDING FEE" occurs.
	* When "FUNDING FEE" occurs in a **crossed position**, `ACCOUNT_UPDATE` will be pushed with only the balance `B`(including the "FUNDING FEE" asset only), without any position `P` message. 
	* When "FUNDING FEE" occurs in an **isolated position**, `ACCOUNT_UPDATE` will be pushed with only the balance `B`(including the "FUNDING FEE" asset only) and the relative position message `P`( including the isolated position on which the "FUNDING FEE" occurs only, without any other position message). 

---

<font size=4>**2020-04-25**</font>


* New fields in USER DATA STREAM event `ORDER_TRADE_UPDATE `:
	* `cp` stands for Close-All conditional order
	* `AP` for Activation Price with `TRAILING_STOP_MARKET` order
	* `cr` for Callback Rate with `TRAILING_STOP_MARKET` order

* New USER DATA STREAM event `MARGIN_CALL`.

---





<font size=4>**2020-04-17**</font>


* New parameter `newOrderRespType` for response type in endpoint ``POST /fapi/v1/order``.   
`ACK` and `RESULT` are supported. And for `newOrderRespType= RESULT`:
	* `MARKET` order: the final FILLED result of the order will be return directly.
	* `LIMIT` order with sepcial `timeInForce`: the final status result of the order(FILLED or EXPIRED) will be returned directly.

---



<font size=4>**2020-04-14**</font>

WEB SOCKET STREAM

* WebSocket connections have a limit of 5 incoming messages per second. A message is considered:
    * A PING frame
    * A PONG frame
    * A JSON control message (e.g. subscribe, unsubscribe)
* A connection that goes beyond the limit will be disconnected; IPs that are repeatedly disconnected may be banned.
* A single connection can listen to a maximum of 1024 streams.

---


<font size=4>**2020-04-09**</font>

* New endpoint of futures trading data: ``GET /futures/data/takerlongshortRatio`` 


---


<font size=4>**2020-04-08**</font>

* New endpoint ``GET /fapi/v1/positionSide/dual`` to get current position mode.
* New endpoint ``POST /fapi/v1/batchOrders`` to place multiple orders.


---



<font size=4>**2020-04-06**</font>

* Please notice: event `ACCOUNT_UPDATE` in USER-DATA-STREAM will not be pushed without update of account balances or positions.
	* `ACCOUNT_UPDATE` will be pushed only when update happens on user's account, including changes on balances, positions, or margin type.
	* Unfilled orders or cancelled orders will not make the event `ACCOUNT_UPDATE` pushed, since there's no change on positions.
	* Only positions of symbols with non-zero isolatd wallet or non-zero position amount will be pushed in the "position" part of the event `ACCOUNT_UPDATE`.

* New endpoint `POST /fapi/v1/positionSide/dual` to change position mode: Hedge Mode or One-way Mode.
* New parameter `positionSide` in the following endpoints：
	* `POST /fapi/v1/order`
	* `POST /fapi/v1/positionMargin`
* New field `positionSide` in the responses to the following endpoints：
	* `POST /fapi/v1/order`
	* `GET /fapi/v1/order`
	* `DELETE /fapi/v1/order`
	* `DELETE /fapi/v1/batchOrders`
	* `GET /fapi/v1/openOrder`
	* `GET /fapi/v1/openOrders`
	* `GET /fapi/v1/allOrders`
	* `GET /fapi/v1/account`
	* `GET /fapi/v1/positionMargin/history`
	* `GET /fapi/v1/positionRisk`
	* `GET /fapi/v1/userTrades`

*  New field `ps` for "position side"in USER_DATA_STREAM events `ACCOUNT_UPDATE` and  `ORDER_TRADE_UPDATE`.

---



<font size=4>**2020-03-30**</font>

* New endpoints of futures trading data: 
   * ``GET /futures/data/openInterestHist``
	* ``GET /futures/data/topLongShortAccountRatio``
	* ``GET /futures/data/topLongShortPositionRatio``
	* ``GET /futures/data/globalLongShortAccountRatio``


<font size=4>**2020-02-26**</font>

* New order type: `TRAILING_STOP_MARKET`

---


<font size=4>**2020-02-20**</font>

* New endpoint to query specific current open order: ``GET /fapi/v1/openOrder``

---



<font size=4>**2020-02-17**</font>

* Update time changed as 1000ms for streams `<symbol>@ticker` and `!ticker@arr`
* New diff depth data with 500ms updates: `<symbol>@depth@500ms`
* New partial depth data with 500ms updates: `<symbol>@depth<level>@500ms`

---


<font size=4>**2020-02-12**</font>

* New [SDK and Code Demonstration](#sdk-and-code-demonstration) on Java

* Faster mark price websocket data with 1s updates: 
	`<symbol>@markPrice@1s` and  `!markPrice@arr@1s` 


---


<font size=4>**2020-02-05**</font>

* New market data endpoint`GET /fapi/v1/leverageBracket` to check notional and leverage brackets. 


---

<font size=4>**2020-01-19**</font>

* "cumQty" is going to be removed from the responses to `DELETE /fapi/v1/order`, `DELETE /fapi/v1/batchOrders` and other `order` relatived endpoints in the coming weeks.   
  Please use "executedQty" instead.

--- 


<font size=4>**2020-01-17**</font>

* New [SDK and Code Demonstration](#sdk-and-code-demonstration) on Python


---
<font size=4>**2020-01-06**</font>

* Faster diff data with real time updates: `<symbol>@depth@0ms`

---
<font size=4>**2020-01-03**</font>

* New endpoints related to isolated position：
	* ``POST /fapi/v1/marginType``
	* ``POST /fapi/v1/positionMargin``
	* ``GET /fapi/v1/positionMargin/history``

* New field in response to ``GET /fapi/v1/positionRisk`` related to isolated position:
	* `marginType`
	* `isolatedMargin`
	
* New field in response to ``GET /fapi/v1/account``related to isolated position: `isolated`

* New field in event `ACCOUNT_UPDATE`:
	* "cw" for cross wallet
	* "mt" for margin type
	* "iw" for isolated wallet (if isolated)


---

<font size=4>**2019-12-19**</font>

* New endpoint ``GET /fapi/v1/openInterest`` to get present open interest of a specific symbol.

---


<font size=4>**2019-12-18**</font>

* New event type in user data stream：`listenKeyExpired`.

---
<font size=4>**2019-12-12**</font>

* New endpoint ``DELETE /fapi/v1/allOpenOrders`` to cancel all open orders of a specific symbol.
* New endpoint``DELETE /fapi/v1/batchOrders `` to cancel a list of open orders.
* `reduceOnly` has been supported in orders with type:
	*  `TAKE_PROFIT`
	*  `TAKE_PROFIT_MARKET`
	*  `STOP`
	*  `STOP_MARKET`

---
<font size=4>**2019-11-29**</font>

* New endpoint ``GET /fapi/v1/allForceOrders`` to get all liquidation orders.
* New websocket streams:
	* ``<symbol>@forceOrder``for liquidation order streams
	* ``!forceOrder@arr`` for all market liquidation order streams


---

<font size=4>**2019-11-25**</font>

* `GET /fapi/v1/account` has new field: `positions`
* Added new field `time` for order creation time in:
	* `GET /fapi/v1/openOrders`
	* `GET /fapi/v1/order`
	* `GET /fapi/v1/allOrders`

---
<font size=4>**2019-11-15**</font>

* New websocket streams：
	* `!miniTicker@arr`: All market 24hr mini-tickers stream.
	*  `!ticker@arr`: : All market 24hr tickers stream.


---

<font size=4>**2019-11-12**</font>

* WSS now supports live subscribing/unsubscribing to streams.

---

<font size=4>**2019-11-05**</font>

* New order type: 
	* `STOP_MARKET`，
	* `TAKE_PROFIT_MARKET`.
* New parameter `workingType` in ``POST /fapi/v1/order``:    
order with stop price can be triggered by “CONTRACT_PRICE” or “MARK_PRICE”
* New keys in USER-DATA-STREAMS：
	* in `ORDER_TRADE_UPDATE`:
		* “T” as trasaction time
		* “wt” as workingType
	* in `ACCOUNT_UPDATE`: 
		* “T” as trasaction time


---


<font size=4>**2019-10-28**</font>

* New rest endpoint for income flow history `GET /fapi/v1/income`

---


<font size=4>**2019-10-25**</font>

* Added "up" in event `ACCOUNT_UPDATE` in user data stream: the unrealized PnL of the position.
* Added "R" in event `ORDER_TRADE_UPDATE` in user data stream, showing if the trade is reduce only.

---
<font size=4>**2019-10-24**</font>

* New WebSocket streams for booktickers added: `<symbol>@bookTicker` and `!bookTicker`. 
* New WebSocket streams for partial orderbook added: `<symbol>@depth<levels>` and `<symbol>@depth<levels>@100ms`
* Faster diff data with 100ms updates: `<symbol>@depth@100ms`
* Added ``Update Speed``: to `Websocket Market Streams`

---
<font size=4>**2019-10-18**</font>

* New endpoint `POST /fapi/v1/leverage` for changing user's initial leverage in specific symbol market.
* Added "leverage" for current initial leverage and "maxNotionalValue" for notional value limit of current initial leverage in response to `GET /fapi/v1/positionRisk`.
* `reduceOnly` now is supported in the `MARKET` orders.

---
<font size=4>**2019-10-14**</font>

* Added ``GET /fapi/v1/fundingRate`` for getting funding fee rate history.

---
<font size=4>**2019-10-11**</font>

* Added "m" in event `ORDER_TRADE_UPDATE` in user data stream, showing if the trade is the maker side.

---
<font size=4>**2019-10-08**</font>

* New order parameter ``reduceOnly`` for ``LIMIT`` orders.
* New order type ``TAKE_PROFIT``.

---
<font size=4>**2019-09-20**</font>

* New retured values in response to GET /fapi/v1/account:      
``maxWithdrawAmount``, ``openOrderInitialMargin``, ``positionInitialMargin``

* New retured values in response to GET /fapi/v1/positionRisk:     
 ``liquidationPrice``



# General Info

## SDK and Code Demonstration

**Disclaimer:**

* The following SDKs are provided by partners and users, and are **not officially** produced. They are only used to help users become familiar with the API endpoint. Please use it with caution and expand R&D according to your own situation.
* Binance does not make any commitment to the safety and performance of the SDKs, nor will be liable for the risks or even losses caused by using the SDKs.

### Python3

To get the provided SDK for Binance Futures,    
please visit [https://github.com/Binance-docs/Binance_Futures_python](https://github.com/Binance-docs/Binance_Futures_python),   
or use the command below:     
``
git clone https://github.com/Binance-docs/Binance_Futures_python.git
``

### Java

To get the provided SDK for Binance Futures,    
please visit [https://github.com/Binance-docs/Binance_Futures_Java](https://github.com/Binance-docs/Binance_Futures_Java),   
or use the command below:     
``
git clone https://github.com/Binance-docs/Binance_Futures_Java.git
``


## General API Information

* Some endpoints will require an API Key. Please refer to [this page](https://www.binance.com/en/support/articles/360002502072)
* The base endpoint is: **https://fapi.binance.com**
* All endpoints return either a JSON object or array.
* Data is returned in **ascending** order. Oldest first, newest last.
* All time and timestamp related fields are in milliseconds.

### HTTP Return Codes

* HTTP `4XX` return codes are used for for malformed requests;
  the issue is on the sender's side.
* HTTP `403` return code is used when the WAF Limit (Web Application Firewall) has been violated.  
* HTTP `429` return code is used when breaking a request rate limit.
* HTTP `418` return code is used when an IP has been auto-banned for continuing to send requests after receiving `429` codes.
* HTTP `5XX` return codes are used for internal errors; the issue is on
  Binance's side.   
  HTTP `503` return code is used when the API successfully sent the message but not get a response within the timeout period.   
  It is important to **NOT** treat this as a failure operation; the execution status is
  **UNKNOWN** and could have been a success.

### Error Codes

* Any endpoint can return an ERROR

> ***The error payload is as follows:***
 
```javascript
{
  "code": -1121,
  "msg": "Invalid symbol."
}
```

* Specific error codes and messages defined in [Error Codes](#error-codes).

### General Information on Endpoints

* For `GET` endpoints, parameters must be sent as a `query string`.
* For `POST`, `PUT`, and `DELETE` endpoints, the parameters may be sent as a
  `query string` or in the `request body` with content type
  `application/x-www-form-urlencoded`. You may mix parameters between both the
  `query string` and `request body` if you wish to do so.
* Parameters may be sent in any order.
* If a parameter sent in both the `query string` and `request body`, the
  `query string` parameter will be used.

## LIMITS
* The `/fapi/v1/exchangeInfo` `rateLimits` array contains objects related to the exchange's `RAW_REQUEST`, `REQUEST_WEIGHT`, and `ORDER` rate limits. These are further defined in the `ENUM definitions` section under `Rate limiters (rateLimitType)`.
* A `429` will be returned when either rate limit is violated.

### IP Limits
* Every request will contain `X-MBX-USED-WEIGHT-(intervalNum)(intervalLetter)` in the response headers which has the current used weight for the IP for all request rate limiters defined.
* Each route has a `weight` which determines for the number of requests each endpoint counts for. Heavier endpoints and endpoints that do operations on multiple symbols will have a heavier `weight`.
* When a 429 is received, it's your obligation as an API to back off and not spam the API.
* **Repeatedly violating rate limits and/or failing to back off after receiving 429s will result in an automated IP ban (HTTP status 418).**
* IP bans are tracked and **scale in duration** for repeat offenders, **from 2 minutes to 3 days**.
* **The limits on the API are based on the IPs, not the API keys.**

<aside class="notice">
We recommend using the websocket for getting data as much as possible, as this will not count to the request rate limit.
</aside>

### Order Rate Limits
* Every order response will contain a `X-MBX-ORDER-COUNT-(intervalNum)(intervalLetter)` header which has the current order count for the account for all order rate limiters defined.
* Rejected/unsuccessful orders are not guaranteed to have `X-MBX-ORDER-COUNT-**` headers in the response.
* **The order rate limit is counted against each account**.

## Endpoint Security Type
* Each endpoint has a security type that determines the how you will
  interact with it.
* API-keys are passed into the Rest API via the `X-MBX-APIKEY`
  header.
* API-keys and secret-keys **are case sensitive**.
* API-keys can be configured to only access certain types of secure endpoints.
 For example, one API-key could be used for TRADE only, while another API-key
 can access everything except for TRADE routes.
* By default, API-keys can access all secure routes.

Security Type | Description
------------ | ------------
NONE | Endpoint can be accessed freely.
TRADE | Endpoint requires sending a valid API-Key and signature.
USER_DATA | Endpoint requires sending a valid API-Key and signature.
USER_STREAM | Endpoint requires sending a valid API-Key.
MARKET_DATA | Endpoint requires sending a valid API-Key.


* `TRADE` and `USER_DATA` endpoints are `SIGNED` endpoints.

## SIGNED (TRADE and USER_DATA) Endpoint Security
* `SIGNED` endpoints require an additional parameter, `signature`, to be
  sent in the  `query string` or `request body`.
* Endpoints use `HMAC SHA256` signatures. The `HMAC SHA256 signature` is a keyed `HMAC SHA256` operation.
  Use your `secretKey` as the key and `totalParams` as the value for the HMAC operation.
* The `signature` is **not case sensitive**.
* Please make sure the `signature` is the end part of your `query string` or `request body`.
* `totalParams` is defined as the `query string` concatenated with the
  `request body`.

### Timing Security
* A `SIGNED` endpoint also requires a parameter, `timestamp`, to be sent which
  should be the millisecond timestamp of when the request was created and sent.
* An additional parameter, `recvWindow`, may be sent to specify the number of
  milliseconds after `timestamp` the request is valid for. If `recvWindow`
  is not sent, **it defaults to 5000**.
  
> The logic is as follows:

```javascript
  if (timestamp < (serverTime + 1000) && (serverTime - timestamp) <= recvWindow）{
    // process request
  } 
  else {
    // reject request
  }
```

**Serious trading is about timing.** Networks can be unstable and unreliable,
which can lead to requests taking varying amounts of time to reach the
servers. With `recvWindow`, you can specify that the request must be
processed within a certain number of milliseconds or be rejected by the
server.

<aside class="notice">
It is recommended to use a small recvWindow of 5000 or less!
</aside>

### SIGNED Endpoint Examples for POST /fapi/v1/order
Here is a step-by-step example of how to send a vaild signed payload from the
Linux command line using `echo`, `openssl`, and `curl`.

Key | Value
------------ | ------------
apiKey | vmPUZE6mv9SD5VNHk4HlWFsOr6aKE2zvsw0MuIgwCIPy6utIco14y7Ju91duEh8A
secretKey | NhqPtmdSJYdKjVHjA7PZj4Mge3R5YNiP1e3UZjInClVN65XAbvqqM6A7H5fATj0j


Parameter | Value
------------ | ------------
symbol | BTCUSDT
side | BUY
type | LIMIT
timeInForce | GTC
quantity | 1
price | 0.1
recvWindow | 5000
timestamp | 1499827319559


#### Example 1: As a query string

> **Example 1**

>  **HMAC SHA256 signature:**

```shell
    $ echo -n "symbol=BTCUSDT&side=BUY&type=LIMIT&timeInForce=GTC&quantity=1&price=0.1&recvWindow=5000&timestamp=1499827319559" | openssl dgst -sha256 -hmac "NhqPtmdSJYdKjVHjA7PZj4Mge3R5YNiP1e3UZjInClVN65XAbvqqM6A7H5fATj0j"
    (stdin)= c8db56825ae71d6d79447849e617115f4a920fa2acdcab2b053c4b2838bd6b71
    
```
> **curl command:**

```shell
    (HMAC SHA256)
    $ curl -H "X-MBX-APIKEY: vmPUZE6mv9SD5VNHk4HlWFsOr6aKE2zvsw0MuIgwCIPy6utIco14y7Ju91duEh8A" -X POST 'https://fapi.binance.com/fapi/v1/order?symbol=BTCUSDT&side=BUY&type=LIMIT&timeInForce=GTC&quantity=1&price=0.1&recvWindow=5000&timestamp=1499827319559&signature=c8db56825ae71d6d79447849e617115f4a920fa2acdcab2b053c4b2838bd6b71'
    
```
* **queryString:** 

	symbol=BTCUSDT  
	&side=BUY   
	&type=LIMIT  
	&timeInForce=GTC   
	&quantity=1  
	&price=0.1   
	&recvWindow=5000   
	&timestamp=1499827319559




#### Example 2: As a request body

> **Example 2**

> **HMAC SHA256 signature:**

```shell
    $ echo -n "symbol=BTCUSDT&side=BUY&type=LIMIT&timeInForce=GTC&quantity=1&price=0.1&recvWindow=5000&timestamp=1499827319559" | openssl dgst -sha256 -hmac "NhqPtmdSJYdKjVHjA7PZj4Mge3R5YNiP1e3UZjInClVN65XAbvqqM6A7H5fATj0j"
    (stdin)= c8db56825ae71d6d79447849e617115f4a920fa2acdcab2b053c4b2838bd6b71
```


> **curl command:**

```shell
    (HMAC SHA256)
    $ curl -H "X-MBX-APIKEY: vmPUZE6mv9SD5VNHk4HlWFsOr6aKE2zvsw0MuIgwCIPy6utIco14y7Ju91duEh8A" -X POST 'https://fapi.binance.com/fapi/v1/order' -d 'symbol=BTCUSDT&side=BUY&type=LIMIT&timeInForce=GTC&quantity=1&price=0.1&recvWindow=5000&timestamp=1499827319559&signature=c8db56825ae71d6d79447849e617115f4a920fa2acdcab2b053c4b2838bd6b71'
```

* **requestBody:**

	symbol=BTCUSDT   
	&side=BUY   
	&type=LIMIT   
	&timeInForce=GTC   
	&quantity=1   
	&price=0.1   
	&recvWindow=5000   
	&timestamp=1499827319559



#### Example 3: Mixed query string and request body

> **Example 3**

> **HMAC SHA256 signature:**

```shell
    $ echo -n "symbol=BTCUSDT&side=BUY&type=LIMIT&timeInForce=GTCquantity=1&price=0.1&recvWindow=5000&timestamp=1499827319559" | openssl dgst -sha256 -hmac "NhqPtmdSJYdKjVHjA7PZj4Mge3R5YNiP1e3UZjInClVN65XAbvqqM6A7H5fATj0j"
    (stdin)= 0fd168b8ddb4876a0358a8d14d0c9f3da0e9b20c5d52b2a00fcf7d1c602f9a77
```

> **curl command:**

```shell
    (HMAC SHA256)
    $ curl -H "X-MBX-APIKEY: vmPUZE6mv9SD5VNHk4HlWFsOr6aKE2zvsw0MuIgwCIPy6utIco14y7Ju91duEh8A" -X POST 'https://fapi.binance.com/fapi/v1/order?symbol=BTCUSDT&side=BUY&type=LIMIT&timeInForce=GTC' -d 'quantity=1&price=0.1&recvWindow=5000&timestamp=1499827319559&signature=0fd168b8ddb4876a0358a8d14d0c9f3da0e9b20c5d52b2a00fcf7d1c602f9a77'
```

* **queryString:** symbol=BTCUSDT&side=BUY&type=LIMIT&timeInForce=GTC
* **requestBody:** quantity=1&price=0.1&recvWindow=5000&timestamp=1499827319559



Note that the signature is different in example 3.     
There is no & between "GTC" and "quantity=1".


## Public Endpoints Info
### Terminology
* `base asset` refers to the asset that is the `quantity` of a symbol.
* `quote asset` refers to the asset that is the `price` of a symbol.


### ENUM definitions

**Symbol type:**

* FUTURE

**Order status (status):**

* NEW
* PARTIALLY_FILLED
* FILLED
* CANCELED
* REJECTED
* EXPIRED

**Order types (orderTypes, type):**

* LIMIT 
* MARKET 
* STOP 
* STOP_MARKET 
* TAKE_PROFIT 
* TAKE_PROFIT_MARKET 
* TRAILING_STOP_MARKET

**Order side (side):**

* BUY
* SELL

**Position side (positionSide):**

* BOTH 
* LONG 
* SHORT 

**Time in force (timeInForce):**

* GTC - Good Till Cancel
* IOC - Immediate or Cancel
* FOK - Fill or Kill
* GTX - Good Till Crossing	(Post Only)	

**Working Type (workingType)**

* MARK_PRICE
* CONTRACT_PRICE 

**Response Type (newOrderRespType)**

* ACK
* RESULT


**Kline/Candlestick chart intervals:**

m -> minutes; h -> hours; d -> days; w -> weeks; M -> months

* 1m
* 3m
* 5m
* 15m
* 30m
* 1h
* 2h
* 4h
* 6h
* 8h
* 12h
* 1d
* 3d
* 1w
* 1M

**Rate limiters (rateLimitType)**

> REQUEST_WEIGHT

```javascript
  {
  	"rateLimitType": "REQUEST_WEIGHT",
  	"interval": "MINUTE",
  	"intervalNum": 1,
  	"limit": 2400
  }
```

> ORDERS

```javascript
  {
  	"rateLimitType": "ORDERS",
  	"interval": "MINUTE",
  	"intervalNum": 1,
  	"limit": 1200
   }
```


* REQUEST_WEIGHT

* ORDERS


**Rate limit intervals (interval)**

* MINUTE




## Filters
Filters define trading rules on a symbol or an exchange.

### Symbol filters
#### PRICE_FILTER

> **/exchangeInfo format:**

```javascript
  {
    "filterType": "PRICE_FILTER",
    "minPrice": "0.00000100",
    "maxPrice": "100000.00000000",
    "tickSize": "0.00000100"
  }
```

The `PRICE_FILTER` defines the `price` rules for a symbol. There are 3 parts:

* `minPrice` defines the minimum `price`/`stopPrice` allowed; disabled on `minPrice` == 0.
* `maxPrice` defines the maximum `price`/`stopPrice` allowed; disabled on `maxPrice` == 0.
* `tickSize` defines the intervals that a `price`/`stopPrice` can be increased/decreased by; disabled on `tickSize` == 0.

Any of the above variables can be set to 0, which disables that rule in the `price filter`. In order to pass the `price filter`, the following must be true for `price`/`stopPrice` of the enabled rules:

* `price` >= `minPrice` 
* `price` <= `maxPrice`
* (`price`-`minPrice`) % `tickSize` == 0


#### LOT_SIZE

> **/exchangeInfo format:**

```javascript
  {
    "filterType": "LOT_SIZE",
    "minQty": "0.00100000",
    "maxQty": "100000.00000000",
    "stepSize": "0.00100000"
  }
```

The `LOT_SIZE` filter defines the `quantity` (aka "lots" in auction terms) rules for a symbol. There are 3 parts:

* `minQty` defines the minimum `quantity` allowed.
* `maxQty` defines the maximum `quantity` allowed.
* `stepSize` defines the intervals that a `quantity` can be increased/decreased by.

In order to pass the `lot size`, the following must be true for `quantity`:

* `quantity` >= `minQty`
* `quantity` <= `maxQty`
* (`quantity`-`minQty`) % `stepSize` == 0



#### MARKET_LOT_SIZE


> **/exchangeInfo format:**

```javascript
  {
    "filterType": "MARKET_LOT_SIZE",
    "minQty": "0.00100000",
    "maxQty": "100000.00000000",
    "stepSize": "0.00100000"
  }
```

The `MARKET_LOT_SIZE` filter defines the `quantity` (aka "lots" in auction terms) rules for `MARKET` orders on a symbol. There are 3 parts:

* `minQty` defines the minimum `quantity` allowed.
* `maxQty` defines the maximum `quantity` allowed.
* `stepSize` defines the intervals that a `quantity` can be increased/decreased by.

In order to pass the `market lot size`, the following must be true for `quantity`:

* `quantity` >= `minQty`
* `quantity` <= `maxQty`
* (`quantity`-`minQty`) % `stepSize` == 0


#### MAX_NUM_ORDERS

> **/exchangeInfo format:**

```javascript
  {
    "filterType": "MAX_NUM_ORDERS",
    "limit": 25
  }
```

The `MAX_NUM_ORDERS` filter defines the maximum number of orders an account is allowed to have open on a symbol.
Note that both "algo" orders and normal orders are counted for this filter.



#### PERCENT_PRICE

> **ExchangeInfo format:**

```javascript
  {
    "filterType": "PERCENT_PRICE",
    "multiplierUp": "1.1500",
    "multiplierDown": "0.8500",
    "multiplierDecimal": 4
  }
```

The `PERCENT_PRICE` filter defines valid range for a price based on the mark price.

In order to pass the `percent price`, the following must be true for `price`:

* BUY: `price` <= `markPrice` * `multiplierUp`
* SELL: `price` >= `markPrice` * `multiplierDown`


---
## Postman Collections

There is now a Postman collection containing the API endpoints for quick and easy use. 

For more information please refer to this page: [Binance API Postman](https://github.com/binance-exchange/binance-api-postman)

# Market Data Endpoints

## Test Connectivity


> **Response:**

```javascript
{}
```


``
GET /fapi/v1/ping
``

Test connectivity to the Rest API.

**Weight:**
1

**Parameters:**
NONE



## Check Server Time

> **Response:**

```javascript
{
  "serverTime": 1499827319559
}
```

``
GET /fapi/v1/time
``

Test connectivity to the Rest API and get the current server time.

**Weight:**
1

**Parameters:**
NONE


## Exchange Information

> **Response:**

```javascript
{
	"exchangeFilters": [],
 	"rateLimits": [
 		{
 			"interval": "MINUTE",
   			"intervalNum": 1,
   			"limit": 2400,
   			"rateLimitType": "REQUEST_WEIGHT"
   		},
  		{
  			"interval": "MINUTE",
   			"intervalNum": 1,
   			"limit": 1200,
   			"rateLimitType": "ORDERS"
   		}
   	],
 	"serverTime": 1565613908500,
 	"symbols": [
 		{
 			"symbol": "BTCUSDT",
 			"status": "TRADING",
 			"maintMarginPercent": "2.5000",
 			"requiredMarginPercent": "5.0000",
 			"baseAsset": "BTC",
 			"quoteAsset": "USDT",
 			"pricePrecision": 2,
 			"quantityPrecision": 3,
 			"baseAssetPrecision": 8,
 			"quotePrecision": 8,
 			"filters": [
 				{
 					"minPrice": "0.01",
   					"maxPrice": "100000",
   					"filterType": "PRICE_FILTER",
   					"tickSize": "0.01"
   				},
  				{
  					"stepSize": "0.001",
   					"filterType": "LOT_SIZE",
   					"maxQty": "1000",
   					"minQty": "0.001"
   				},
  				{
  					"stepSize": "0.001",
   					"filterType": "MARKET_LOT_SIZE",
   					"maxQty": "1000",
   					"minQty": "0.001"
   				},
  				{
  					"limit": 0, 
  					"filterType": "MAX_NUM_ORDERS"
  				},
  				{
  					"multiplierDown": "0.7000",
   					"multiplierUp": "1.3000",
   					"multiplierDecimal": "4",
   					"filterType": "PERCENT_PRICE"
   				}
   			],
 			"orderTypes": [
 				"LIMIT",
  				"MARKET",
  				"STOP",
  				"STOP_MARKET",
  				"TAKE_PROFIT",
  				"TAKE_PROFIT_MARKET"
  			],
 			"timeInForce": [
 				"GTC", 
 				"IOC", 
 				"FOK",
 				"GTX"
 			]
 		}
   	],
	"timezone": "UTC"
}

```

``
GET /fapi/v1/exchangeInfo
``

Current exchange trading rules and symbol information

**Weight:**
1

**Parameters:**
NONE




## Order Book


> **Response:**

```javascript
{
  "lastUpdateId": 1027024,
  "E": 1589436922972,   // Message output time
  "T": 1589436922959,   // Transaction time
  "bids": [
    [
      "4.00000000",     // PRICE
      "431.00000000"    // QTY
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

``
GET /fapi/v1/depth
``

**Weight:**

Adjusted based on the limit:


Limit | Weight
------------ | ------------
5, 10, 20, 50 | 2
100 | 5
500 | 10
1000 | 20

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
limit | INT | NO | Default 100; max 1000. Valid limits:[5, 10, 20, 50, 100, 500, 1000]




## Recent Trades List

> **Response:**

```javascript
[
  {
    "id": 28457,
    "price": "4.00000100",
    "qty": "12.00000000",
    "quoteQty": "48.00",
    "time": 1499865549590,
    "isBuyerMaker": true,
  }
]
```

``
GET /fapi/v1/trades
``

Get recent trades (up to last 24h).

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
limit | INT | NO | Default 500; max 1000.



## Old Trades Lookup (MARKET_DATA)

> **Response:**

```javascript
[
  {
    "id": 28457,
    "price": "4.00000100",
    "qty": "12.00000000",
    "quoteQty": "8000.00",
    "time": 1499865549590,
    "isBuyerMaker": true,
  }
]
```

``
GET /fapi/v1/historicalTrades
``

Get older market historical trades.

**Weight:**
5

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
limit | INT | NO | Default 500; max 1000.
fromId | LONG | NO | TradeId to fetch from. Default gets most recent trades.




## Compressed/Aggregate Trades List

> **Response:**

```javascript
[
  {
    "a": 26129,         // Aggregate tradeId
    "p": "0.01633102",  // Price
    "q": "4.70443515",  // Quantity
    "f": 27781,         // First tradeId
    "l": 27781,         // Last tradeId
    "T": 1498793709153, // Timestamp
    "m": true,          // Was the buyer the maker?
  }
]
```

``
GET /fapi/v1/aggTrades
``

Get compressed, aggregate trades. Trades that fill at the time, from the same
order, with the same price will have the quantity aggregated.

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
fromId | LONG | NO | ID to get aggregate trades from INCLUSIVE.
startTime | LONG | NO | Timestamp in ms to get aggregate trades from INCLUSIVE.
endTime | LONG | NO | Timestamp in ms to get aggregate trades until INCLUSIVE.
limit | INT | NO | Default 500; max 1000.

* If both startTime and endTime are sent, time between startTime and endTime must be less than 1 hour.
* If fromId, startTime, and endTime are not sent, the most recent aggregate trades will be returned.



## Kline/Candlestick Data


> **Response:**

```javascript
[
  [
    1499040000000,      // Open time
    "0.01634790",       // Open
    "0.80000000",       // High
    "0.01575800",       // Low
    "0.01577100",       // Close
    "148976.11427815",  // Volume
    1499644799999,      // Close time
    "2434.19055334",    // Quote asset volume
    308,                // Number of trades
    "1756.87402397",    // Taker buy base asset volume
    "28.46694368",      // Taker buy quote asset volume
    "17928899.62484339" // Ignore.
  ]
]
```

``
GET /fapi/v1/klines
``

Kline/candlestick bars for a symbol.
Klines are uniquely identified by their open time.

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
interval | ENUM | YES |
startTime | LONG | NO |
endTime | LONG | NO |
limit | INT | NO | Default 500; max 1500.

* If startTime and endTime are not sent, the most recent klines are returned.




## Mark Price


> **Response:**

```javascript
{
    "symbol": "BTCUSDT",
    "markPrice": "11012.80409769",  
    "lastFundingRate": "-0.03750000",  // This is the lasted funding rate
    "nextFundingTime": 1562569200000,
    "time": 1562566020000
}
```

> **OR (when symbol not sent)**

```javascript
[
	{
	    "symbol": "BTCUSDT",
	    "markPrice": "11012.80409769",
	    "lastFundingRate": "-0.03750000",  // This is the lasted funding rate
	    "nextFundingTime": 1562569200000,
	    "time": 1562566020000
	}
]
```


``
GET /fapi/v1/premiumIndex
``

Mark Price and Funding Rate

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | NO |



## Get Funding Rate History (MARKET_DATA)

> **Response:**

```javascript
[
	{
    	"symbol": "BTCUSDT",
    	"fundingRate": "-0.03750000",
    	"fundingTime": 1570608000000,
    	"time": 1576566020000
	},
	{
   		"symbol": "BTCUSDT",
    	"fundingRate": "0.00010000",
    	"fundingTime": 1570636800000,
    	"time": 1576566020000
	}
]
```

``
GET /fapi/v1/fundingRate
``


**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
startTime | LONG | NO | Timestamp in ms to get funding rate from INCLUSIVE.
endTime | LONG | NO | Timestamp in ms to get funding rate  until INCLUSIVE.
limit | INT | NO | Default 100; max 1000


* If `startTime` and `endTime` are not sent, the most recent `limit` datas are returned.
* If the number of data between `startTime` and `endTime` is larger than `limit`, return as `startTime` + `limit`.
* In ascending order.





## 24hr Ticker Price Change Statistics

> **Response:**

```javascript
{
  "symbol": "BTCUSDT",
  "priceChange": "-94.99999800",
  "priceChangePercent": "-95.960",
  "weightedAvgPrice": "0.29628482",
  "prevClosePrice": "0.10002000",
  "lastPrice": "4.00000200",
  "lastQty": "200.00000000",
  "openPrice": "99.00000000",
  "highPrice": "100.00000000",
  "lowPrice": "0.10000000",
  "volume": "8913.30000000",
  "quoteVolume": "15.30000000",
  "openTime": 1499783499040,
  "closeTime": 1499869899040,
  "firstId": 28385,   // First tradeId
  "lastId": 28460,    // Last tradeId
  "count": 76         // Trade count
}
```

> OR

```javascript
[
	{
  		"symbol": "BTCUSDT",
  		"priceChange": "-94.99999800",
  		"priceChangePercent": "-95.960",
  		"weightedAvgPrice": "0.29628482",
  		"prevClosePrice": "0.10002000",
  		"lastPrice": "4.00000200",
  		"lastQty": "200.00000000",
  		"openPrice": "99.00000000",
  		"highPrice": "100.00000000",
  		"lowPrice": "0.10000000",
  		"volume": "8913.30000000",
  		"quoteVolume": "15.30000000",
  		"openTime": 1499783499040,
  		"closeTime": 1499869899040,
  		"firstId": 28385,   // First tradeId
  		"lastId": 28460,    // Last tradeId
  		"count": 76         // Trade count
	}
]
```

``
GET /fapi/v1/ticker/24hr
``

24 hour rolling window price change statistics.    
**Careful** when accessing this with no symbol.

**Weight:**   
1 for a single symbol;    
**40** when the symbol parameter is omitted

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | NO |

* If the symbol is not sent, tickers for all symbols will be returned in an array.



## Symbol Price Ticker

> **Response:**

```javascript
{
  "symbol": "BTCUSDT",
  "price": "6000.01",
  "time": 1589437530011   // Transaction time
}
```

> OR


```javascript
[
	{
  		"symbol": "BTCUSDT",
  		"price": "6000.01",
  		"time": 1589437530011
	}
]
```

``
GET /fapi/v1/ticker/price
``

Latest price for a symbol or symbols.

**Weight:**   
1 for a single symbol;    
**2** when the symbol parameter is omitted

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | NO |

* If the symbol is not sent, prices for all symbols will be returned in an array.



## Symbol Order Book Ticker


> **Response:**

```javascript
{
  "symbol": "BTCUSDT",
  "bidPrice": "4.00000000",
  "bidQty": "431.00000000",
  "askPrice": "4.00000200",
  "askQty": "9.00000000",
  "time": 1589437530011   // Transaction time
}
```

> OR


```javascript
[
	{
  		"symbol": "BTCUSDT",
  		"bidPrice": "4.00000000",
  		"bidQty": "431.00000000",
  		"askPrice": "4.00000200",
  		"askQty": "9.00000000",
  		"time": 1589437530011
	}
]
```

``
GET /fapi/v1/ticker/bookTicker
``

Best price/qty on the order book for a symbol or symbols.

**Weight:**   
1 for a single symbol;    
**2** when the symbol parameter is omitted

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | NO |

* If the symbol is not sent, bookTickers for all symbols will be returned in an array.



##Get all Liquidation Orders
> **Response:**

```javascript
[

    {
          "symbol": "BTCUSDT",                // SYMBOL
          "price": "7918.33",                 // ORDER_PRICE
          "origQty": "0.014",                 // ORDER_AMOUNT
          "executedQty": "0.014",             // FILLED_AMOUNT
          "avragePrice": "7918.33",           // AVG_PRICE
          "status": "FILLED",                 // STATUS
          "timeInForce": "IOC",               // TIME_IN_FORCE
          "type": "LIMIT",
          "side": "SELL",                     // DIRECTION
         "time": 1568014460893 
    },
]
```


``GET /fapi/v1/allForceOrders``

**Weight:** 5

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol	| STRING | NO	
startTime | LONG | NO	
endTime | LONG | NO	| Default precent timestamp
limit | LONG | NO | Max returned data number from endTime; Default:100 Max:1000

* If the symbol is not sent, liquidation orders for all symbols will be returned.




## Open Interest


> **Response:**

```javascript
{
	"openInterest": "10659.509", 
	"symbol": "BTCUSDT",
	"time": 1589437530011   // Transaction time
}

```

Get present open interest of a specific symbol.


``
GET /fapi/v1/openInterest
``


**Weight:** 1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol	| STRING | YES




## Open Interest Statistics (MARKET_DATA)

> **Response:**

```javascript
[
    { 
         "symbol":"BTCUSDT",
	      "sumOpenInterest":"20403.63700000",  // total open inerest 
	      "sumOpenInterestValue": "150570784.07809979",   // total open interet value
	      "timestamp":"1583127900000"
    
     },
     
     {
     
         "symbol":"BTCUSDT",
         "sumOpenInterest":"20401.36700000",
         "sumOpenInterestValue":"149940752.14464448",
         "timestamp":"1583128200000"
         
        },   
	    
]

```


``
GET /futures/data/openInterestHist
``


**Weight:** 1

**Parameters:**

 Name  |  Type  | Mandatory |  Description
------ | ------ | -------- | ------
symbol | STRING | YES      | 
period | NUM    | YES      | "5m","15m","30m","1h","2h","4h","6h","12h","1d"
limit  | LONG   | NO       | default 30, max 500
startTime | LONG | NO      |
endTime   | LONG | NO      |

* If there is no limit of startime and endtime, it will return the value brfore the current time by default.
* Only the data of the latest 30 days is available.


## Top Trader Long/Short Ratio (Accounts)  (MARKET_DATA)

> **Response:**

```javascript
[
    { 
         "symbol":"BTCUSDT",
	      "longShortRatio":"1.8105",  // long/short account num ratio of top traders
	      "longAccount": "0.6442",   // long account num ratio of top traders 
	      "shortAccount":"0.3558",   // long account num ratio of top traders 
	      "timestamp":"1583139600000"
    
     },
     
     {
         
         "symbol":"BTCUSDT",
	      "longShortRatio":"0.5576",
	      "longAccount": "0.3580", 
	      "shortAccount":"0.6420", 	                
	      "timestamp":"1583139900000"
	               
        },   
	    
]

```



``
GET /futures/data/topLongShortAccountRatio
``


**Weight:** 1

**Parameters:**

 Name  |  Type  | Mandatory |  Description
------ | ------ | -------- | ------
symbol | STRING | YES      | 
period | NUM    | YES      | "5m","15m","30m","1h","2h","4h","6h","12h","1d"
limit  | LONG   | NO       | default 30, max 500
startTime | LONG | NO      |
endTime   | LONG | NO      |

* If there is no limit of startime and endtime, it will return the value brfore the current time by default.
* Only the data of the latest 30 days is available.



## Top Trader Long/Short Ratio (Positions) (MARKET_DATA)

> **Response:**

```javascript
[
    { 
         "symbol":"BTCUSDT",
	      "longShortRatio":"1.4342",// long/short position ratio of top traders
	      "longAccount": "0.5891", // long positions ratio of top traders
	      "shortAccount":"0.4108", // short positions ratio of top traders
	      "timestamp":"1583139600000"
    
     },
     
     {
         
         "symbol":"BTCUSDT",
	      "longShortRatio":"1.4337",
	      "longAccount": "0.3583", 
	      "shortAccount":"0.6417", 	                
	      "timestamp":"1583139900000"
	               
        },   
	    
]

```



``
GET /futures/data/topLongShortPositionRatio
``


**Weight:** 1

**Parameters:**

 Name  |  Type  | Mandatory |  Description
------ | ------ | -------- | ------
symbol | STRING | YES      | 
period | NUM    | YES      | "5m","15m","30m","1h","2h","4h","6h","12h","1d"
limit  | LONG   | NO       | default 30, max 500
startTime | LONG | NO      |
endTime   | LONG | NO      |

* If there is no limit of startime and endtime, it will return the value brfore the current time by default.
* Only the data of the latest 30 days is available.



## Long/Short Ratio (MARKET_DATA)

> **Response:**


```javascript
[
    { 
         "symbol":"BTCUSDT",  // long/short account num ratio of all traders
	      "longShortRatio":"0.1960",  //long account num ratio of all traders
	      "longAccount": "0.6622",   // short account num ratio of all traders
	      "shortAccount":"0.3378", 
	      "timestamp":"1583139600000"
    
     },
     
     {
         
         "symbol":"BTCUSDT",
	      "longShortRatio":"1.9559",
	      "longAccount": "0.6617", 
	      "shortAccount":"0.3382", 	                
	      "timestamp":"1583139900000"
	               
        },   
	    
]

```



``
GET /futures/data/globalLongShortAccountRatio
``

**Weight:** 1

**Parameters:**

 Name  |  Type  | Mandatory |  Description
------ | ------ | -------- | ------
symbol | STRING | YES      | 
period | NUM    | YES      | "5m","15m","30m","1h","2h","4h","6h","12h","1d"
limit  | LONG   | NO       | default 30, max 500
startTime | LONG | NO      |
endTime   | LONG | NO      |

* If there is no limit of startime and endtime, it will return the value brfore the current time by default.
* Only the data of the latest 30 days is available.


## Taker Buy/Sell Volume (MARKET_DATA)

> **Response:**


```javascript
[
    { 
	      "buySellRatio":"1.5586",
	      "buyVol": "387.3300", 
	      "sellVol":"248.5030", 
	      "timestamp":"1585614900000"
    
     },
     
     {
     
	      "buySellRatio":"1.3104",
	      "buyVol": "343.9290", 
	      "sellVol":"248.5030", 	                
	      "timestamp":"1583139900000"
	               
        },   
	    
]

```



``
GET /futures/data/takerlongshortRatio
``

**Weight:** 1

**Parameters:**

 Name  |  Type  | Mandatory |  Description
------ | ------ | -------- | ------
symbol | STRING | YES      | 
period | NUM    | YES      | "5m","15m","30m","1h","2h","4h","6h","12h","1d"
limit  | LONG   | NO       | default 30, max 500
startTime | LONG | NO      |
endTime   | LONG | NO      |

* If there is no limit of startime and endtime, it will return the value brfore the current time by default.
* Only the data of the latest 30 days is available. 


# Websocket Market Streams

* The base endpoint is: **wss://fstream.binance.com**
* Streams can be access either in a single raw stream or a combined stream
* Raw streams are accessed at **/ws/\<streamName\>**
* Combined streams are accessed at **/stream?streams=\<streamName1\>/\<streamName2\>/\<streamName3\>**
* Combined stream events are wrapped as follows: **{"stream":"\<streamName\>","data":\<rawPayload\>}**
* All symbols for streams are **lowercase**
* A single connection to **fstream.binance.com** is only valid for 24 hours; expect to be disconnected at the 24 hour mark
* The websocket server will send a `ping frame` every 3 minutes. If the websocket server does not receive a `pong frame` back from the connection within a 10 minute period, the connection will be disconnected. Unsolicited `pong frames` are allowed.


## Live Subscribing/Unsubscribing to streams

* The following data can be sent through the websocket instance in order to subscribe/unsubscribe from streams. Examples can be seen below.
* The `id` used in the JSON payloads is an unsigned INT used as an identifier to uniquely identify the messages going back and forth.

### Subscribe to a stream

> **Response**

  ```javascript
  {
    "result": null,
    "id": 1
  }
  ```

* **Request**

  	{    
    	"method": "SUBSCRIBE",    
    	"params":     
    	[   
      	"btcusdt@aggTrade",    
      	"btcusdt@depth"     
    	],    
    	"id": 1   
  	}



### Unsubscribe to a stream

> **Response**
  
  ```javascript
  {
    "result": null,
    "id": 312
  }
  ```


* **Request**

  {   
    "method": "UNSUBSCRIBE",    
    "params":     
    [    
      "btcusdt@depth"   
    ],    
    "id": 312   
  }



### Listing Subscriptions

> **Response**
  
  ```javascript
  {
    "result": [
      "btcusdt@aggTrade"
    ],
    "id": 3
  }
  ```


* **Request**

  {   
    "method": "LIST_SUBSCRIPTIONS",    
    "id": 3   
  }     
 


### Setting Properties
Currently, the only property can be set is to set whether `combined` stream payloads are enabled are not.
The combined property is set to `false` when connecting using `/ws/` ("raw streams") and `true` when connecting using `/stream/`.

> **Response**
  
  ```javascript
  {
    "result": null,
    "id": 5
  }
  ```

* **Request**

  {    
    "method": "SET_PROPERTY",    
    "params":     
    [   
      "combined",    
      true   
    ],    
    "id": 5   
  }




### Retrieving Properties

> **Response**

  ```javascript
  {
    "result": true, // Indicates that combined is set to true.
    "id": 2
  }
  ```
  
* **Request**
  
  {   
    "method": "GET_PROPERTY",    
    "params":     
    [   
      "combined"   
    ],    
    "id": 2   
  }   
 



### Error Messages

Error Message | Description
---|---
{"code": 0, "msg": "Unknown property"} | Parameter used in the `SET_PROPERTY` or `GET_PROPERTY` was invalid
{"code": 1, "msg": "Invalid value type: expected Boolean"} | Value should only be `true` or `false`
{"code": 2, "msg": "Invalid request: property name must be a string"}| Property name provided was invalid
{"code": 2, "msg": "Invalid request: request ID must be an unsigned integer"}| Parameter `id` had to be provided or the value provided in the `id` parameter is an unsupported type
{"code": 2, "msg": "Invalid request: unknown variant %s, expected one of `SUBSCRIBE`, `UNSUBSCRIBE`, `LIST_SUBSCRIPTIONS`, `SET_PROPERTY`, `GET_PROPERTY` at line 1 column 28"} | Possible typo in the provided method or provided method was neither of the expected values
{"code": 2, "msg": "Invalid request: too many parameters"}| Unnecessary parameters provided in the data
{"code": 2, "msg": "Invalid request: property name must be a string"} | Property name was not provided
{"code": 2, "msg": "Invalid request: missing field `method` at line 1 column 73"} | `method` was not provided in the data
{"code":3,"msg":"Invalid JSON: expected value at line %s column %s"} | JSON data sent has incorrect syntax.






## Aggregate Trade Streams


> **Payload:**

```javascript
{
  "e": "aggTrade",  // Event type
  "E": 123456789,   // Event time
  "s": "BTCUSDT",    // Symbol
  "a": 5933014,		// Aggregate trade ID
  "p": "0.001",     // Price
  "q": "100",       // Quantity
  "f": 100,         // First trade ID
  "l": 105,         // Last trade ID
  "T": 123456785,   // Trade time
  "m": true,        // Is the buyer the market maker?
}
```

The Aggregate Trade Streams push trade information that is aggregated for a single taker order every 100 milliseconds.

**Stream Name:**     
``<symbol>@aggTrade``

**Update Speed:** 100ms


## Mark Price Stream

> **Payload:**

```javascript
  {
    "e": "markPriceUpdate",  // Event type
    "E": 1562305380000,      // Event time
    "s": "BTCUSDT",          // Symbol
    "p": "11185.87786614",   // Mark price
    "r": "0.00030000",       // Funding rate
    "T": 1562306400000       // Next funding time
  }
```

Mark price and funding rate for a single symbol pushed every 3 seconds or every second.

**Stream Name:**     
``<symbol>@markPrice`` or ``<symbol>@markPrice@1s``

**Update Speed:** 3000ms or 1000ms



## Mark Price Stream for All market

> **Payload:**

```javascript
[ 
  {
    "e": "markPriceUpdate",  // Event type
    "E": 1562305380000,      // Event time
    "s": "BTCUSDT",          // Symbol
    "p": "11185.87786614",   // Mark price
    "r": "0.00030000",       // Funding rate
    "T": 1562306400000       // Next funding time
  }
]
```

Mark price and funding rate for all symbols pushed every 3 seconds or every second.

**Stream Name:**     
``!markPrice@arr`` or ``!markPrice@arr@1s``

**Update Speed:** 3000ms or 1000ms



## Kline/Candlestick Streams


> **Payload:**

```javascript
{
  "e": "kline",     // Event type
  "E": 123456789,   // Event time
  "s": "BTCUSDT",    // Symbol
  "k": {
    "t": 123400000, // Kline start time
    "T": 123460000, // Kline close time
    "s": "BTCUSDT",  // Symbol
    "i": "1m",      // Interva\
    "f": 100,       // First trade ID
    "L": 200,       // Last trade ID
    "o": "0.0010",  // Open price
    "c": "0.0020",  // Close price
    "h": "0.0025",  // High price
    "l": "0.0015",  // Low price
    "v": "1000",    // Base asset volume
    "n": 100,       // Number of trades
    "x": false,     // Is this kline closed?
    "q": "1.0000",  // Quote asset volume
    "V": "500",     // Taker buy base asset volume
    "Q": "0.500",   // Taker buy quote asset volume
    "B": "123456"   // Ignore
  }
}
```

The Kline/Candlestick Stream push updates to the current klines/candlestick every 250 milliseconds (if existing).

**Kline/Candlestick chart intervals:**

m -> minutes; h -> hours; d -> days; w -> weeks; M -> months

* 1m
* 3m
* 5m
* 15m
* 30m
* 1h
* 2h
* 4h
* 6h
* 8h
* 12h
* 1d
* 3d
* 1w
* 1M

**Stream Name:**     
``<symbol>@kline_<interval>``

**Update Speed:** 250ms


## Individual Symbol Mini Ticker Stream


> **Payload:**

```javascript
  {
    "e": "24hrMiniTicker",  // Event type
    "E": 123456789,         // Event time
    "s": "BTCUSDT",         // Symbol
    "c": "0.0025",          // Close price
    "o": "0.0010",          // Open price
    "h": "0.0025",          // High price
    "l": "0.0010",          // Low price
    "v": "10000",           // Total traded base asset volume
    "q": "18"               // Total traded quote asset volume
  }
```

24hr rolling window mini-ticker statistics for a single symbol. These are NOT the statistics of the UTC day, but a 24hr rolling window from requestTime to 24hrs before.

**Stream Name:**     
``<symbol>@miniTicker``

**Update Speed:** 500ms




## All Market Mini Tickers Stream


> **Payload:**

```javascript
[  
  {
    "e": "24hrMiniTicker",  // Event type
    "E": 123456789,         // Event time
    "s": "BTCUSDT",         // Symbol
    "c": "0.0025",          // Close price
    "o": "0.0010",          // Open price
    "h": "0.0025",          // High price
    "l": "0.0010",          // Low price
    "v": "10000",           // Total traded base asset volume
    "q": "18"               // Total traded quote asset volume
  }
]
```

24hr rolling window mini-ticker statistics for all symbols. These are NOT the statistics of the UTC day, but a 24hr rolling window from requestTime to 24hrs before. Note that only tickers that have changed will be present in the array.

**Stream Name:**     
``!miniTicker@arr``

**Update Speed:** 1000ms



## Individual Symbol Ticker Streams


> **Payload:**

```javascript
{
  "e": "24hrTicker",  // Event type
  "E": 123456789,     // Event time
  "s": "BTCUSDT",     // Symbol
  "p": "0.0015",      // Price change
  "P": "250.00",      // Price change percent
  "w": "0.0018",      // Weighted average price
  "c": "0.0025",      // Last price
  "Q": "10",          // Last quantity
  "o": "0.0010",      // Open price
  "h": "0.0025",      // High price
  "l": "0.0010",      // Low price
  "v": "10000",       // Total traded base asset volume
  "q": "18",          // Total traded quote asset volume
  "O": 0,             // Statistics open time
  "C": 86400000,      // Statistics close time
  "F": 0,             // First trade ID
  "L": 18150,         // Last trade Id
  "n": 18151          // Total number of trades
}
```

24hr rollwing window ticker statistics for a single symbol. These are NOT the statistics of the UTC day, but a 24hr rolling window from requestTime to 24hrs before.

**Stream Name:**     
``<symbol>@ticker``

**Update Speed:** 500ms



## All Market Tickers Streams


> **Payload:**

```javascript
[
	{
	  "e": "24hrTicker",  // Event type
	  "E": 123456789,     // Event time
	  "s": "BTCUSDT",     // Symbol
	  "p": "0.0015",      // Price change
	  "P": "250.00",      // Price change percent
	  "w": "0.0018",      // Weighted average price
	  "c": "0.0025",      // Last price
	  "Q": "10",          // Last quantity
	  "o": "0.0010",      // Open price
	  "h": "0.0025",      // High price
	  "l": "0.0010",      // Low price
	  "v": "10000",       // Total traded base asset volume
	  "q": "18",          // Total traded quote asset volume
	  "O": 0,             // Statistics open time
	  "C": 86400000,      // Statistics close time
	  "F": 0,             // First trade ID
	  "L": 18150,         // Last trade Id
	  "n": 18151          // Total number of trades
	}
]
```

24hr rollwing window ticker statistics for all symbols. These are NOT the statistics of the UTC day, but a 24hr rolling window from requestTime to 24hrs before. Note that only tickers that have changed will be present in the array.

**Stream Name:**     
``!ticker@arr``

**Update Speed:** 1000ms





## Individual Symbol Book Ticker Streams

> **Payload:**

```javascript
{
  "u":400900217,     		// order book updateId
  "E": 1568014460893,  		// event time
  "T": 1568014460891,  		// transaction time
  "s":"BNBUSDT",     		// symbol
  "b":"25.35190000", 		// best bid price
  "B":"31.21000000", 		// best bid qty
  "a":"25.36520000", 		// best ask price
  "A":"40.66000000"  		// best ask qty
}
```


Pushes any update to the best bid or ask's price or quantity in real-time for a specified symbol.

**Stream Name:** `<symbol>@bookTicker`

**Update Speed:** Real-time





## All Book Tickers Stream

> **Payload:**

```javascript
{
  // Same as <symbol>@bookTicker payload
}
```

Pushes any update to the best bid or ask's price or quantity in real-time for all symbols.

**Stream Name:** `!bookTicker`

**Update Speed:** Real-time




##Liquidation Order Streams

> **Payload:**

```javascript
{

	"e":"forceOrder",                   // Event Type
	"E":1568014460893,                  // Event Time
	"o":{
	
		"s":"BTCUSDT",                   // Symbol
		"S":"SELL",                      // Side
		"o":"LIMIT",                     // Order Type
		"f":"IOC",                       // Time in Force
		"q":"0.014",                     // Original Quantity
		"p":"9910",                      // Price
		"ap":"9910",                     // Average Price
		"X":"FILLED",                    // Order Status
		"l":"0.014",                     // Order Last Filled Quantity
		"z":"0.014",                     // Order Filled Accumulated Quantity
		"T":1568014460893,          	 // Order Trade Time
	
	}

}
```

The Liquidation Order Streams push force liquidation order information for specific symbol

**Stream Name:**  ``<symbol>@forceOrder``

**Update Speed:** Real-time



##All Market Liquidation Order Streams

> **Payload:**

```javascript
{

	"e":"forceOrder",                   // Event Type
	"E":1568014460893,                  // Event Time
	"o":{
	
		"s":"BTCUSDT",                   // Symbol
		"S":"SELL",                      // Side
		"o":"LIMIT",                     // Order Type
		"f":"IOC",                       // Time in Force
		"q":"0.014",                     // Original Quantity
		"p":"9910",                      // Price
		"ap":"9910",                     // Average Price
		"X":"FILLED",                    // Order Status
		"l":"0.014",                     // Order Last Filled Quantity
		"z":"0.014",                     // Order Filled Accumulated Quantity
		"T":1568014460893,          	 // Order Trade Time
	
	}

}
```

The All Liquidation Order Streams push force liquidation order information for all symbols in the market.

**Stream Name:** ``!forceOrder@arr``

**Update Speed:** Real-time






## Partial Book Depth Streams

> **Payload:**

```javascript
{
  "e": "depthUpdate", // Event type
  "E": 1571889248277, // Event time
  "T": 1571889248276, // transaction time
  "s": "BTCUSDT",
  "U": 390497796,
  "u": 390497878,
  "pu": 390497794,
  "b": [          // Bids to be updated
    [
      "7403.89",  // Price Level to be
      "0.002"     // Quantity
    ],
    [
      "7403.90",
      "3.906"
    ],
    [
      "7404.00",
      "1.428"
    ],
    [
      "7404.85",
      "5.239"
    ],
    [
      "7405.43",
      "2.562"
    ]
  ],
  "a": [          // Asks to be updated
    [
      "7405.96",  // Price level to be
      "3.340"     // Quantity
    ],
    [
      "7406.63",
      "4.525"
    ],
    [
      "7407.08",
      "2.475"
    ],
    [
      "7407.15",
      "4.800"
    ],
    [
      "7407.20",
      "0.175"
    ]
  ]
}
```

Top **<levels\>** bids and asks, Valid **<levels\>** are 5, 10, or 20.

**Stream Names:** `<symbol>@depth<levels>` OR `<symbol>@depth<levels>@500ms` OR `<symbol>@depth<levels>@100ms`.  

**Update Speed:** 250ms, 500ms or 100ms






## Diff. Book Depth Streams


> **Payload:**

```javascript
{
  "e": "depthUpdate", // Event type
  "E": 123456789,     // Event time
  "T": 123456788,     // transaction time 
  "s": "BTCUSDT",      // Symbol
  "U": 157,           // first update Id from last stream
  "u": 160,           // last update Id from last stream
  "pu": 149,          // last update Id in last stream（ie ‘u’ in last stream）
  "b": [              // Bids to be updated
    [
      "0.0024",       // Price level to be updated
      "10"            // Quantity
    ]
  ],
  "a": [              // Asks to be updated
    [
      "0.0026",       // Price level to be updated
      "100"          // Quantity
    ]
  ]
}
```

Bids and asks, pushed every 250 milliseconds, 500 milliseconds, 100 milliseconds or in real time(if existing)

**Stream Name:**     
``<symbol>@depth`` OR ``<symbol>@depth@500ms``  OR ``<symbol>@depth@100ms`` OR ``<symbol>@depth@0ms``

**Update Speed:** 250ms, 500ms, 100ms or Real-Time



## How to manage a local order book correctly
1. Open a stream to **wss://fstream.binance.com/stream?streams=btcusdt@depth**.
2. Buffer the events you receive from the stream. For same price, latest received update covers the previous one. 
3. Get a depth snapshot from **https://fapi.binance.com/fapi/v1/depth?symbol=BTCUSDT&limit=1000** .
4. Drop any event where `u` is < `lastUpdateId` in the snapshot.
5. The first processed event should have `U` <= `lastUpdateId` **AND** `u` >= `lastUpdateId`
6. While listening to the stream, each new event's `pu` should be equal to the previous event's `u`, otherwise initialize the process from step 3.
7. The data in each event is the **absolute** quantity for a price level.
8. If the quantity is 0, **remove** the price level.
9. Receiving an event that removes a price level that is not in your local order book can happen and is normal.






# Account/Trades Endpoints

## New Future Account Transfer 


> **Response:**

```javascript
{
    "tranId": 100000001    //transaction id
}
```

``
POST https://api.binance.com/sapi/v1/futures/transfer  (HMAC SHA256)
``

* ***NOTICE: base url for fapi is not available here***

Execute transfer between spot account and futures account.

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
asset | STRING | YES | The asset being transferred, e.g., USDT
amount | DECIMAL | YES | The amount to be transferred
type | INT | YES | 1: transfer from spot main account to future account 2: transfer from future account to spot main account
recvWindow | LONG | NO  | 
timestamp | LONG | YES



## Get Future Account Transaction History List (USER_DATA)


> **Response:**

```javascript
{
  "rows": [
    {
      "asset": "USDT",
      "tranId": 100000001
      "amount": "40.84624400",
      "type": "1",
      "timestamp": 1555056425000,
      "status": "CONFIRMED"          //one of PENDING (pending to execution), CONFIRMED (successfully transfered), FAILED (execution failed, nothing happened to your account);
    }
  ],
  "total": 1
}
```


``
GET https://api.binance.com/sapi/v1/futures/transfer (HMAC SHA256)
``

* ***NOTICE: base url for fapi is not avalible here***

**Weight:**
5

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
asset |	STRING | YES	
startTime |	LONG |	YES	
endTime | LONG | NO	
current | LONG | NO | Currently querying page. Start from 1. Default:1
size |	LONG | NO |	Default:10 Max:100
recvWindow | LONG | NO | 
timestamp | LONG | YES


## Change Position Mode（TRADE）

> **Response:**

```javascript
{
	"code": 200,
	"msg": "success"
}
```

``
POST /fapi/v1/positionSide/dual (HMAC SHA256)
``

Change user's position mode (Hedge Mode or One-way Mode ) on ***EVERY symbol***    

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
dualSidePosition | STRING   | YES      | "true": Hedge Mode mode; "false": One-way Mode 
recvWindow | LONG   | NO       |
timestamp  | LONG   | YES      |


## Get Current Position Mode（USER_DATA）

> **Response:**

```javascript
{
	"dualSidePosition": true // "true": Hedge Mode mode; "false": One-way Mode
}
```

``
GET /fapi/v1/positionSide/dual (HMAC SHA256)
``

Get user's position mode (Hedge Mode or One-way Mode ) on ***EVERY symbol***    

**Weight:**
30

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
recvWindow | LONG   | NO       |
timestamp  | LONG   | YES      |




## New Order  (TRADE)


> **Response:**

```javascript
{
 	"clientOrderId": "testOrder",
 	"cumQuote": "0",
 	"executedQty": "0",
 	"orderId": 22542179,
 	"avgPrice": "0.00000",
 	"origQty": "10",
 	"price": "0",
  	"reduceOnly": false,
  	"side": "BUY",
  	"positionSide": "SHORT",
  	"status": "NEW",
  	"stopPrice": "9300",		// please ignore when order type is TRAILING_STOP_MARKET
  	"closePosition": false,   // if Close-All
  	"symbol": "BTCUSDT",
  	"timeInForce": "GTC",
  	"type": "TRAILING_STOP_MARKET",
  	"activatePrice": "9020",	// activation price, only return with TRAILING_STOP_MARKET order
  	"priceRate": "0.3",			// callback rate, only return with TRAILING_STOP_MARKET order
 	"updateTime": 1566818724722,
 	"workingType": "CONTRACT_PRICE"
}
```


``
POST /fapi/v1/order  (HMAC SHA256)
``

Send in a new order.

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
side | ENUM | YES |
positionSide | ENUM	| NO | Default `BOTH` for One-way Mode ; `LONG` or `SHORT` for Hedge Mode. It must be sent in Hedge Mode.
type | ENUM | YES |
timeInForce | ENUM | NO |
quantity | DECIMAL | NO | Cannot be sent with `closePosition`=`true`(Close-All)
reduceOnly | STRING | NO | "true" or "false". Defalt "false". Cannot be sent in Hedge Mode; cannot be sent with `closePosition`=`true`
price | DECIMAL | NO |
newClientOrderId | STRING | NO | A unique id among open orders. Automatically generated if not sent.
stopPrice | DECIMAL | NO | Used with `STOP/STOP_MARKET` or `TAKE_PROFIT/TAKE_PROFIT_MARKET` orders.
closePosition    | STRING  | NO       | `true`, `false`；Close-All，used with `STOP_MARKET` or `TAKE_PROFIT_MARKET`.
activationPrice  | DECIMAL | NO       | Used with `TRAILING_STOP_MARKET` orders, default as the latest price（supporting different `workingType`)
callbackRate     | DECIMAL | NO       | Used with `TRAILING_STOP_MARKET` orders, min 0.1, max 5 where 1 for 1%
workingType | ENUM | NO | stopPrice triggered by: "MARK_PRICE", "CONTRACT_PRICE". Default "CONTRACT_PRICE"
newOrderRespType | ENUM    | NO       | "ACK", "RESULT", default "ACK"
recvWindow | LONG | NO |
timestamp | LONG | YES |

Additional mandatory parameters based on `type`:

Type | Additional mandatory parameters
------------ | ------------
`LIMIT` | `timeInForce`, `quantity`, `price`
`MARKET` | `quantity`
`STOP/TAKE_PROFIT` | `quantity`,  `price`, `stopPrice`
`STOP_MARKET/TAKE_PROFIT_MARKET` | `stopPrice`
`TRAILING_STOP_MARKET` | `callbackRate`

* Order with type `STOP`,  parameter `timeInForce` can be sent ( default `GTC`).
* Order with type `TAKE_PROFIT`,  parameter `timeInForce` can be sent ( default `GTC`).
* Condition orders will be triggered when:
	* `STOP`, `STOP_MARKET`:
		* BUY: latest price ("MARK_PRICE" or "CONTRACT_PRICE") >= `stopPrice`
		* SELL: latest price ("MARK_PRICE" or "CONTRACT_PRICE") <= `stopPrice`
	* `TAKE_PROFIT`, `TAKE_PROFIT_MARKET`:
		* BUY: latest price ("MARK_PRICE" or "CONTRACT_PRICE") <= `stopPrice`
		* SELL: latest price ("MARK_PRICE" or "CONTRACT_PRICE") >= `stopPrice`
	* `TRAILING_STOP_MARKET`:
		* BUY: the lowest price after order placed <= `activationPrice`, and the latest price >= the lowest price * (1 + `callbackRate`)
		* SELL: the highest price after order placed >= `activationPrice`, and the latest price <= the highest price * (1 - `callbackRate`)

* For `TRAILING_STOP_MARKET`, if you got such error code.  
  ``{"code": -2021, "msg": "Order would immediately trigger."}``    
  means that the parameters you send do not meet the following requirements:
	* BUY: `activationPrice` should be smaller than latest price.
	* SELL: `activationPrice` should be larger than latest price.

* If `newOrderRespType ` is sent as `RESULT` :
	* `MARKET` order: the final FILLED result of the order will be return directly.
	* `LIMIT` order with sepcial `timeInForce`: the final status result of the order(FILLED or EXPIRED) will be returned directly.

* `STOP_MARKET`, `TAKE_PROFIT_MARKET` with `closePosition`=`true`:
	* Follow the same rules for condition orders.
	* If triggered，**close all** current long position( if `SELL`) or current short position( if `BUY`).
	* Cannot be used with `quantity` paremeter
	* Cannot be used with `reduceOnly` parameter
	* In Hedge Mode,cannot be used with `BUY` orders in `LONG` position side. and cannot be used with `SELL` orders in `SHORT` position side


## Place Multiple Orders  (TRADE)


> **Response:**

```javascript
[
	{
	 	"clientOrderId": "testOrder",
	 	"cumQuote": "0",
	 	"executedQty": "0",
	 	"orderId": 22542179,
	 	"avgPrice": "0.00000",
	 	"origQty": "10",
	 	"price": "0",
	  	"reduceOnly": false,
	  	"side": "BUY",
	  	"positionSide": "SHORT",
	  	"status": "NEW",
	  	"stopPrice": "9300",		// please ignore when order type is TRAILING_STOP_MARKET
	  	"symbol": "BTCUSDT",
	  	"timeInForce": "GTC",
	  	"type": "TRAILING_STOP_MARKET",
	  	"activatePrice": "9020",	// activation price, only return with TRAILING_STOP_MARKET order
	  	"priceRate": "0.3",			// callback rate, only return with TRAILING_STOP_MARKET order
	 	"updateTime": 1566818724722,
	 	"workingType": "CONTRACT_PRICE"
	},
	{
		"code": -2022, 
		"msg": "ReduceOnly Order is rejected."
	}
]
```

``
POST /fapi/v1/batchOrders  (HMAC SHA256)
``

**Weight:**
5

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
batchOrders |	LIST<JSON> | 	YES |	order list. Max 5 orders
recvWindow |	LONG |	NO	
timestamp	| LONG | YES	

**Where ``batchOrders`` is the list of order parameters in JSON**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
side | ENUM | YES |
positionSide | ENUM	| NO | Default `BOTH` for One-way Mode ; `LONG` or `SHORT` for Hedge Mode. It must be sent with Hedge Mode.
type | ENUM | YES |
timeInForce | ENUM | NO |
quantity | DECIMAL | YES |
reduceOnly | STRING | NO | "true" or "false". Defalt "false".
price | DECIMAL | NO |
newClientOrderId | STRING | NO | A unique id among open orders. Automatically generated if not sent.
stopPrice | DECIMAL | NO | Used with `STOP/STOP_MARKET` or `TAKE_PROFIT/TAKE_PROFIT_MARKET` orders.
activationPrice  | DECIMAL | NO       | Used with `TRAILING_STOP_MARKET` orders, default as the latest price（supporting different `workingType`)
callbackRate     | DECIMAL | NO       | Used with `TRAILING_STOP_MARKET` orders, min 0.1, max 4 where 1 for 1%
workingType | ENUM | NO | stopPrice triggered by: "MARK_PRICE", "CONTRACT_PRICE". Default "CONTRACT_PRICE"
newOrderRespType | ENUM    | NO       | "ACK", "RESULT", default "ACK"


* Paremeter rules are same with `New Order`
* Batch orders are processed concurrently, and the order of matching is not guaranteed.
* The order of returned contents for batch orders is the same as the order of the order list.





## Query Order (USER_DATA)


> **Response:**

```javascript
{
  	"avgPrice": "0.00000",
  	"clientOrderId": "abc",
  	"cumQuote": "0",
  	"executedQty": "0",
  	"orderId": 1917641,
  	"origQty": "0.40",
  	"origType": "TRAILING_STOP_MARKET",
  	"price": "0",
  	"reduceOnly": false,
  	"side": "BUY",
  	"positionSide": "SHORT",
  	"status": "NEW",
  	"stopPrice": "9300",				// please ignore when order type is TRAILING_STOP_MARKET
  	"closePosition": false,   // if Close-All
  	"symbol": "BTCUSDT",
  	"time": 1579276756075,				// order time
  	"timeInForce": "GTC",
  	"type": "TRAILING_STOP_MARKET",
  	"activatePrice": "9020",			// activation price, only return with TRAILING_STOP_MARKET order
  	"priceRate": "0.3",					// callback rate, only return with TRAILING_STOP_MARKET order
  	"updateTime": 1579276756075,		// update time
  	"workingType": "CONTRACT_PRICE"
}
```

``
GET /fapi/v1/order (HMAC SHA256)
``

Check an order's status.

**Weight:**
1

* These orders will not be found:
	* order status is `CANCELED` or `EXPIRED`, **AND** 
	* order has NO filled trade, **AND**
	* created time + 30 days < current time

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
orderId | LONG | NO |
origClientOrderId | STRING | NO |
recvWindow | LONG | NO |
timestamp | LONG | YES |

Notes:

* Either `orderId` or `origClientOrderId` must be sent.



## Cancel Order (TRADE)

> **Response:**

```javascript
{
 	"clientOrderId": "myOrder1",
 	"cumQty": "0",
 	"cumQuote": "0",
 	"executedQty": "0",
 	"orderId": 283194212,
 	"origQty": "11",
 	"origType": "TRAILING_STOP_MARKET",
  	"price": "0",
  	"reduceOnly": false,
  	"side": "BUY",
  	"positionSide": "SHORT",
  	"status": "CANCELED",
  	"stopPrice": "9300",				// please ignore when order type is TRAILING_STOP_MARKET
  	"closePosition": false,   // if Close-All
  	"symbol": "BTCUSDT",
  	"timeInForce": "GTC",
  	"type": "TRAILING_STOP_MARKET",
  	"activatePrice": "9020",			// activation price, only return with TRAILING_STOP_MARKET order
  	"priceRate": "0.3",					// callback rate, only return with TRAILING_STOP_MARKET order
 	"updateTime": 1571110484038,
 	"workingType": "CONTRACT_PRICE"
}
```

``
DELETE /fapi/v1/order  (HMAC SHA256)
``

Cancel an active order.

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
orderId | LONG | NO |
origClientOrderId | STRING | NO | 
recvWindow | LONG | NO |
timestamp | LONG | YES |

Either `orderId` or `origClientOrderId` must be sent.


## Cancel All Open Orders (TRADE)

> **Response:**

```javascript
{
	"code": "200", 
	"msg": "The operation of cancel all open order is done."
}
```

``
DELETE /fapi/v1/allOpenOrders  (HMAC SHA256)
``

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
recvWindow | LONG | NO |
timestamp | LONG | YES |


## Cancel Multiple Orders (TRADE)

> **Response:**

```javascript
[
	{
	 	"clientOrderId": "myOrder1",
	 	"cumQty": "0",
	 	"cumQuote": "0",
	 	"executedQty": "0",
	 	"orderId": 283194212,
	 	"origQty": "11",
	 	"origType": "TRAILING_STOP_MARKET",
  		"price": "0",
  		"reduceOnly": false,
  		"side": "BUY",
  		"positionSide": "SHORT",
  		"status": "CANCELED",
  		"stopPrice": "9300",				// please ignore when order type is TRAILING_STOP_MARKET
  		"closePosition": false,   // if Close-All
  		"symbol": "BTCUSDT",
  		"timeInForce": "GTC",
  		"type": "TRAILING_STOP_MARKET",
  		"activatePrice": "9020",			// activation price, only return with TRAILING_STOP_MARKET order
  		"priceRate": "0.3",					// callback rate, only return with TRAILING_STOP_MARKET order
	 	"updateTime": 1571110484038
	},
	{
		"code": -2011,
		"msg": "Unknown order sent."
	}
]
```

``
DELETE /fapi/v1/batchOrders  (HMAC SHA256)
``

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
orderIdList | LIST\<LONG\> | NO | max length 10 <br /> e.g. [1234567,2345678]
origClientOrderIdList | LIST\<STRING\> | NO | max length 10<br /> e.g. ["my_id_1","my_id_2"], encode the double quotes. No space after comma.
recvWindow | LONG | NO |
timestamp | LONG | YES |

Either `orderIdList` or `origClientOrderIdList ` must be sent.




## Auto-Cancel All Open Orders (TRADE)

> **Response:**

```javascript
{
	"symbol": "BTCUSDT", 
	"countdownTime": "100000"
}
```


Cancel all open orders of the specified symbol at the end of the specified countdown.

``
POST /fapi/v1/countdownCancelAll  (HMAC SHA256)
``

**Weight:**
10

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
countdownTime | LONG | YES | countdown time, 1000 for 1 second. 0 to cancel the timer
recvWindow | LONG | NO |
timestamp | LONG | YES |

* This rest endpoint means to ensure your open orders are canceled in case of an outage. The endpoint should be called repeatedly as heartbeats so that the existing countdown time can be canceled and repalced by a new one.

* Example usage:    
	Call this endpoint at 30s intervals with an countdownTime of 120000 (120s).   
	If this endpoint is not called within 120 seconds, all your orders of the sepcified symbol will be automatically canceled.   
	If this endpoint is called with an countdownTime of 0, the countdown timer will be stopped.
	
* The system will check all countdowns **approximately every 10 milliseconds**, so please note that sufficient redundancy should be considered when using this function. We do not recommend setting the countdown time to be too precise or too small.





## Query Current Open Order (USER_DATA)

> **Response:**

```javascript

{
  	"avgPrice": "0.00000",				
  	"clientOrderId": "abc",				
  	"cumQuote": "0",						
  	"executedQty": "0",					
  	"orderId": 1917641,					
  	"origQty": "0.40",						
  	"origType": "TRAILING_STOP_MARKET",
  	"price": "0",
  	"reduceOnly": false,
  	"side": "BUY",
  	"positionSide": "SHORT",
  	"status": "NEW",
  	"stopPrice": "9300",				// please ignore when order type is TRAILING_STOP_MARKET
  	"closePosition": false,   			// if Close-All
  	"symbol": "BTCUSDT",
  	"time": 1579276756075,				// order time
  	"timeInForce": "GTC",
  	"type": "TRAILING_STOP_MARKET",
  	"activatePrice": "9020",			// activation price, only return with TRAILING_STOP_MARKET order
  	"priceRate": "0.3",					// callback rate, only return with TRAILING_STOP_MARKET order						
  	"updateTime": 1579276756075,		
  	"workingType": "CONTRACT_PRICE"		
}
```

``
GET /fapi/v1/openOrder  (HMAC SHA256)
``


**Weight:** 1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
orderId | LONG | NO | 
origClientOrderId | STRING | NO | 
recvWindow | LONG   | NO       |
timestamp  | LONG   | YES      |

* Either`orderId` or `origClientOrderId` must be sent
* If the queried order has been filled or cancelled, the error message "Order does not exist" will be returned.




## Current All Open Orders (USER_DATA)

> **Response:**

```javascript
[
  {
  	"avgPrice": "0.00000",
  	"clientOrderId": "abc",
  	"cumQuote": "0",
  	"executedQty": "0",
  	"orderId": 1917641,
  	"origQty": "0.40",
  	"origType": "TRAILING_STOP_MARKET",
  	"price": "0",
  	"reduceOnly": false,
  	"side": "BUY",
  	"positionSide": "SHORT",
  	"status": "NEW",
  	"stopPrice": "9300",				// please ignore when order type is TRAILING_STOP_MARKET
  	"closePosition": false,   // if Close-All
  	"symbol": "BTCUSDT",
  	"time": 1579276756075,				// order time
  	"timeInForce": "GTC",
  	"type": "TRAILING_STOP_MARKET",
  	"activatePrice": "9020",			// activation price, only return with TRAILING_STOP_MARKET order
  	"priceRate": "0.3",					// callback rate, only return with TRAILING_STOP_MARKET order
  	"updateTime": 1579276756075,		// update time
  	"workingType": "CONTRACT_PRICE"
  }
]
```

``
GET /fapi/v1/openOrders  (HMAC SHA256)
``

Get all open orders on a symbol. **Careful** when accessing this with no symbol.

**Weight:**
1 for a single symbol; **40** when the symbol parameter is omitted


**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | NO |
recvWindow | LONG | NO |
timestamp | LONG | YES |


* If the symbol is not sent, orders for all symbols will be returned in an array.

## All Orders (USER_DATA)


> **Response:**

```javascript
[
  {
   	"avgPrice": "0.00000",
  	"clientOrderId": "abc",
  	"cumQuote": "0",
  	"executedQty": "0",
  	"orderId": 1917641,
  	"origQty": "0.40",
  	"origType": "TRAILING_STOP_MARKET",
  	"price": "0",
  	"reduceOnly": false,
  	"side": "BUY",
  	"positionSide": "SHORT",
  	"status": "NEW",
  	"stopPrice": "9300",				// please ignore when order type is TRAILING_STOP_MARKET
  	"closePosition": false,   // if Close-All
  	"symbol": "BTCUSDT",
  	"time": 1579276756075,				// order time
  	"timeInForce": "GTC",
  	"type": "TRAILING_STOP_MARKET",
  	"activatePrice": "9020",			// activation price, only return with TRAILING_STOP_MARKET order
  	"priceRate": "0.3",					// callback rate, only return with TRAILING_STOP_MARKET order
  	"updateTime": 1579276756075,		// update time
  	"workingType": "CONTRACT_PRICE"
  }
]
```

``
GET /fapi/v1/allOrders (HMAC SHA256)
``

Get all account orders; active, canceled, or filled.

* These orders will not be found:
	* order status is `CANCELED` or `EXPIRED`, **AND** 
	* order has NO filled trade, **AND**
	* created time + 30 days < current time

**Weight:**
5 with symbol

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
orderId | LONG | NO |
startTime | LONG | NO |
endTime | LONG | NO |
limit | INT | NO | Default 500; max 1000.
recvWindow | LONG | NO |
timestamp | LONG | YES |

**Notes:**

* If `orderId` is set, it will get orders >= that `orderId`. Otherwise most recent orders are returned.



## Future Account Balance (USER_DATA)

> **Response:**

```javascript
[
 	{
 		"accountAlias": "SgsR",    // unique account code
 		"asset": "USDT",
 		"balance": "122607.35137903",
 		"withdrawAvailable": "102333.54137903"
	}
]
```

``
GET /fapi/v1/balance (HMAC SHA256)
``

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
recvWindow | LONG | NO |
timestamp | LONG | YES




## Account Information (USER_DATA)

> **Response:**


> **Response:**

```javascript

{
	"assets": [
		{
			"asset": "USDT",
		   	"initialMargin": "0.33683000",
		   	"maintMargin": "0.02695000",
		   	"marginBalance": "8.74947592",
		   	"maxWithdrawAmount": "8.41264592",
		   	"openOrderInitialMargin": "0.00000000",
		   	"positionInitialMargin": "0.33683000",
		   	"unrealizedProfit": "-0.44537584",
		   	"walletBalance": "9.19485176"
		}
	 ],
	 "canDeposit": true,
	 "canTrade": true,
	 "canWithdraw": true,
	 "feeTier": 2,
	 "maxWithdrawAmount": "8.41264592",
	 "positions": [
		 {
		 	"isolated": false, 
		 	"leverage": "20", 
		 	"initialMargin": "0.33683", 
		  	"maintMargin": "0.02695", 
		   	"openOrderInitialMargin": "0.00000", 
		   	"positionInitialMargin": "0.33683",
		   	"symbol": "BTCUSDT",
		   	"unrealizedProfit": "-0.44537584", 
		   	"positionSide": "BOTH", // BOTH means that it is the position of One-way Mode  
		 },
		 {
		 	"isolated": false, 
		 	"leverage": "20", 
		 	"initialMargin": "0.00000", 
		  	"maintMargin": "0.00000", 
		   	"openOrderInitialMargin": "0.00000", 
		   	"positionInitialMargin": "0.00000", 
		   	"symbol": "BTCUSDT", 
		   	"unrealizedProfit": "0.00000000", 
		   	"positionSide": "LONG", // LONG or SHORT means that it is the position of Hedge Mode 
		 },
		 {
		 	"isolated": false,
		 	"leverage": "20", 
		 	"initialMargin": "0.00000", 
		  	"maintMargin": "0.00000", 
		   	"openOrderInitialMargin": "0.00000", 
		   	"positionInitialMargin": "0.00000", 
		   	"symbol": "BTCUSDT",
		   	"unrealizedProfit": "0.00000000", 
		   	"positionSide": "SHORT", // LONG or SHORT means that it is the position of One-way Mode  
		 }
	 ],
	 "totalInitialMargin": "0.33683000",
	 "totalMaintMargin": "0.02695000",
	 "totalMarginBalance": "8.74947592",
	 "totalOpenOrderInitialMargin": "0.00000000",
	 "totalPositionInitialMargin": "0.33683000",
	 "totalUnrealizedProfit": "-0.44537584",
	 "totalWalletBalance": "9.19485176",
	 "updateTime": 0
 }

```

``
GET /fapi/v1/account (HMAC SHA256)
``

Get current account information.

**Weight:**
5

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
recvWindow | LONG | NO |
timestamp | LONG | YES |


## Change Initial Leverage (TRADE)

> **Response:**

```javascript
{
 	"leverage": 21,
 	"maxNotionalValue": "1000000",
 	"symbol": "BTCUSDT"
}
```

``
POST /fapi/v1/leverage (HMAC SHA256)
``

Change user's initial leverage of specific symbol market.

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | YES | 
leverage | INT | YES | target initial leverage: int from 1 to 125
recvWindow | LONG | NO |
timestamp | LONG | YES |


## Change Margin Type (TRADE)

> **Response:**

```javascript
{
	"code": 200,
	"msg": "success"
}
```

``
POST /fapi/v1/marginType (HMAC SHA256)
``


**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol	 | STRING | YES	
marginType | ENUM | YES | ISOLATED, CROSSED
recvWindow | LONG | NO	
timestamp | LONG | YES


## Modify Isolated Position Margin (TRADE)

> **Response:**

```javascript
{
	"amount": 100.0,
  	"code": 200,
  	"msg": "Successfully modify position margin.",
  	"type": 1
}
```

``
POST /fapi/v1/positionMargin (HMAC SHA256)
``

**Weight:**
1


**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | YES	
positionSide | ENUM	| NO | Default `BOTH` for One-way Mode ; `LONG` or `SHORT` for Hedge Mode. It must be sent with Hedge Mode.
amount | DECIMAL | YES	
type | INT | YES | 1: Add position margin，2: Reduce position margin
recvWindow | LONG | NO	
timestamp | LONG | YES

* Only for isolated symbol


## Get Position Margin Change History (TRADE)

> **Response:**

```javascript
[
	{
		"amount": "23.36332311",
	  	"asset": "USDT",
	  	"symbol": "BTCUSDT",
	  	"time": 1578047897183,
	  	"type": 1,
	  	"positionSide": "BOTH"
	},
	{
		"amount": "100",
	  	"asset": "USDT",
	  	"symbol": "BTCUSDT",
	  	"time": 1578047900425,
	  	"type": 1,
	  	"positionSide": "LONG"
	}
]
```

``
GET /fapi/v1/positionMargin/history (HMAC SHA256)
``

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | YES	
type | INT	 | NO | 1: Add position margin，2: Reduce position margin
startTime | LONG | NO	
endTime | LONG | NO	
limit | INT | NO | 默认值: 500
recvWindow | LONG | NO	
timestamp | LONG | YES	




## Position Information (USER_DATA)

> **Response:**

> **Response:**

```javascript
[
  	{
  		"entryPrice": "0.00000",
  		"marginType": "isolated", 
  		"isAutoAddMargin": "false",
  		"isolatedMargin": "0.00000000",	
  		"leverage": "10", 
  		"liquidationPrice": "0", 
  		"markPrice": "6679.50671178",	
  		"maxNotionalValue": "20000000", 
  		"positionAmt": "0.000", 
  		"symbol": "BTCUSDT", 
  		"unRealizedProfit": "0.00000000", 
  		"positionSide": "BOTH",
  	},
  	{
  		"entryPrice": "6563.66500", 
  		"marginType": "isolated", 
  		"isAutoAddMargin": "false",
  		"isolatedMargin": "15517.54150468",
  		"leverage": "10",
  		"liquidationPrice": "5930.78",
  		"markPrice": "6679.50671178",	
  		"maxNotionalValue": "20000000", 
  		"positionAmt": "20.000", 
  		"symbol": "BTCUSDT", 
  		"unRealizedProfit": "2316.83423560"
  		"positionSide": "LONG", 
  	},
  	{
  		"entryPrice": "0.00000",
  		"marginType": "isolated", 
  		"isAutoAddMargin": "false",
  		"isolatedMargin": "5413.95799991", 
  		"leverage": "10", 
  		"liquidationPrice": "7189.95", 
  		"markPrice": "6679.50671178",	
  		"maxNotionalValue": "20000000", 
  		"positionAmt": "-10.000", 
  		"symbol": "BTCUSDT",
  		"unRealizedProfit": "-1156.46711780" 
  		"positionSide": "SHORT",
  	}
]
```

``
GET /fapi/v1/positionRisk (HMAC SHA256)
``
Get current account information.

**Weight:**
5

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
recvWindow | LONG | NO |
timestamp | LONG | YES |

**Note**    
Please use with user data stream `ACCOUNT_UPDATE` to meet your timeliness and accuracy needs.


## Account Trade List (USER_DATA)


> **Response:**

```javascript
[
  {
  	"buyer": false,
  	"commission": "-0.07819010",
  	"commissionAsset": "USDT",
  	"id": 698759,
  	"maker": false,
  	"orderId": 25851813,
  	"price": "7819.01",
  	"qty": "0.002",
  	"quoteQty": "15.63802",
  	"realizedPnl": "-0.91539999",
  	"side": "SELL",
  	"positionSide": "SHORT",
  	"symbol": "BTCUSDT",
  	"time": 1569514978020
  }
]
```

``
GET /fapi/v1/userTrades  (HMAC SHA256)
``

Get trades for a specific account and symbol.

**Weight:**
5 

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
startTime | LONG | NO |
endTime | LONG | NO |
fromId | LONG | NO | Trade id to fetch from. Default gets most recent trades.
limit | INT | NO | Default 500; max 1000.
recvWindow | LONG | NO |
timestamp | LONG | YES |




## Get Income History(USER_DATA)


> **Response:**

```javascript
[
	{
    	"symbol": "",
    	"incomeType": "TRANSFER",
    	"income": "-0.37500000",
    	"asset": "USDT",
    	"info":"",	
    	"time": 1570608000000,
	},
	{
   		"symbol": "BTCUSDT",
    	"incomeType": "COMMISSION", 
    	"income": "-0.01000000",
    	"asset": "USDT",
    	"info":"",	
    	"time": 1570636800000,
	}
]
```

``
GET /fapi/v1/income (HMAC SHA256)
``

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | NO|
incomeType | STRING | NO | "TRANSFER"，"WELCOME_BONUS", "REALIZED_PNL"，"FUNDING_FEE", "COMMISSION", and "INSURANCE_CLEAR" 
startTime | LONG | NO | Timestamp in ms to get funding from INCLUSIVE.
endTime | LONG | NO | Timestamp in ms to get funding until INCLUSIVE.
limit | INT | NO | Default 100; max 1000
recvWindow|LONG|NO| 
timestamp|LONG|YES|


* If `incomeType ` is not sent, all kinds of flow will be returned



## Notional and Leverage Brackets (USER_DATA)


> **Response:**

```javascript
[
    {
        "symbol": "ETHUSDT",
        "brackets": [
            {
                "bracket": 1,   // Notianl bracket
                "initialLeverage": 75,  // Max initial leverge for this bracket
                "notionalCap": 10000,  // Cap notional of this bracket
                "notionalFloor": 0,  // Notionl threshold of this bracket 
                "maintMarginRatio": 0.0065 // Maintenance ratio for this bracket
            },
        ]
    }
]
```

> **OR** (if symbol sent)

```javascript

{
    "symbol": "ETHUSDT",
    "brackets": [
        {
            "bracket": 1,
            "initialLeverage": 75,
            "notionalCap": 10000,
            "notionalFloor": 0,
            "maintMarginRatio": 0.0065
        },
    ]
}
```


``
GET /fapi/v1/leverageBracket
``


**Weight:** 1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol	| STRING | NO
recvWindow|LONG|NO| 
timestamp|LONG|YES|




# User Data Streams

* The base API endpoint is: **https://fapi.binance.com**
* A User Data Stream `listenKey` is valid for 60 minutes after creation.
* Doing a `PUT` on a `listenKey` will extend its validity for 60 minutes.
* Doing a `DELETE` on a `listenKey` will close the stream and invalidate the `listenKey`.
* Doing a `POST` on an account with an active `listenKey` will return the currently active `listenKey` and extend its validity for 60 minutes.
* The base websocket endpoint is: **wss://fstream.binance.com**
* User Data Streams are accessed at **/ws/\<listenKey\>**
* User data stream payloads are **not guaranteed** to be in order during heavy periods; **make sure to order your updates using E**
* A single connection to **fstream.binance.com** is only valid for 24 hours; expect to be disconnected at the 24 hour mark


## Start User Data Stream (USER_STREAM)


> **Response:**

```javascript
{
  "listenKey": "pqia91ma19a5s61cv6a81va65sdf19v8a65a1a5s61cv6a81va65sdf19v8a65a1"
}
```

``
POST /fapi/v1/listenKey (HMAC SHA256)
``

Start a new user data stream. The stream will close after 60 minutes unless a keepalive is sent. If the account has an active `listenKey`, that `listenKey` will be returned and its validity will be extended for 60 minutes.

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
recvWindow | LONG | NO |
timestamp | LONG | YES |



## Keepalive User Data Stream (USER_STREAM)

> **Response:**

```javascript
{}
```

``
PUT /fapi/v1/listenKey (HMAC SHA256)
``

Keepalive a user data stream to prevent a time out. User data streams will close after 60 minutes. It's recommended to send a ping about every 60 minutes.

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
recvWindow | LONG | NO |
timestamp | LONG | YES |



## Close User Data Stream (USER_STREAM)


> **Response:**

```javascript
{}
```

``
DELETE /fapi/v1/listenKey (HMAC SHA256)
``

Close out a user data stream.

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
recvWindow | LONG | NO |
timestamp | LONG | YES |



## Event: User Data Stream Expired

> **Payload:**

```javascript
{
	'e': 'listenKeyExpired',      // event type
	'E': 1576653824250				// event time
}
```

When the `listenKey` used for the user data stream turns expired, this event will be pushed.

**Notice:**

* This event is not related to the websocket disconnection.
* This event will be received only when a valid `listenKey` in connection got expired.
* No more user data event will be updated after this event received until a new valid `listenKey` used.





##Event: Margin Call

> **Payload:**

```javascript
{
    "e":"MARGIN_CALL",    	// Event Type
    "E":1587727187525,		// Event Time
    "cw":"3.16812045",		// Cross Wallet Balance. Only pushed with crossed position margin call
    "p":[					// Position(s) of Margin Call
      {
        "s":"ETHUSDT",		// Symbol
        "ps":"LONG",		// Position Side
        "pa":"1.327",		// Position Amount
        "mt":"CROSSED",		// Margin Type
        "iw":"0",			// Isolated Wallet (if isolated position)
        "mp":"187.17127",	// Mark Price
        "up":"-1.166074",	// Unrealized PnL
        "mm":"1.614445"		// Maintenance Margin Required
      }
    ]
}  
 
```


* When the user's position risk ratio is too high, this stream will be pushed.
* This message is only used as risk guidance information and is not recommended for investment strategies.
* In the case of a highly volatile market, there may be the possibility that the user's position has been liquidated at the same time when this stream is pushed out.





##Event: Balance and Position Update


> **Payload:**

```javascript
{
  "e": "ACCOUNT_UPDATE",				// Event Type
  "E": 1564745798939,            		// Event Time
  "T": 1564745798938 ,           		// Transaction
  "a":                          		// Update Data
    {
      "B":[                     		// Balances
        {
          "a":"USDT",           		// Asset
          "wb":"122624.12345678",    	// Wallet Balance
          "cw":"100.12345678"			// Cross Wallet Balance
        },
        {
          "a":"BNB",           
          "wb":"1.00000000",
          "cw":"0.00000000"         
        }
      ],
      "P":[
        {
          "s":"BTCUSDT",          	// Symbol
          "pa":"0",               	// Position Amount
          "ep":"0.00000",            // Entry Price
          "cr":"200",             	// (Pre-fee) Accumulated Realized
          "up":"0",						// Unrealized PnL
          "mt":"isolated",				// Margin Type
          "iw":"0.00000000",			// Isolated Wallet (if isolated position)
          "ps":"BOTH"					// Position Side
        }，
        {
        	"s":"BTCUSDT",
        	"pa":"20",
        	"ep":"6563.66500",
        	"cr":"0",
        	"up":"2850.21200",
        	"mt":"isolated",
        	"iw":"13200.70726908",
        	"ps":"LONG"
      	 },
        {
        	"s":"BTCUSDT",
        	"pa":"-10",
        	"ep":"6563.86000",
        	"cr":"-45.04000000",
        	"up":"-1423.15600",
        	"mt":"isolated",
        	"iw":"6570.42511771",
        	"ps":"SHORT"
        }
      ]
    }
}
```

Event type is `ACCOUNT_UPDATE`.

* When balance or position get updated, this event will be pushed.
	* `ACCOUNT_UPDATE` will be pushed only when update happens on user's account, including changes on balances, positions, or margin type.
	* Unfilled orders or cancelled orders will not make the event `ACCOUNT_UPDATE` pushed, since there's no change on positions.
	* Only positions of symbols with non-zero isolatd wallet or non-zero position amount will be pushed in the "position" part of the event `ACCOUNT_UPDATE` when any position changes.

* When "FUNDING FEE" changes to the user's balance, the event will be pushed with the brief message:
	* When "FUNDING FEE" occurs in a **crossed position**, `ACCOUNT_UPDATE` will be pushed with only the balance `B`(including the "FUNDING FEE" asset only), without any position `P` message. 
	* When "FUNDING FEE" occurs in an **isolated position**, `ACCOUNT_UPDATE` will be pushed with only the balance `B`(including the "FUNDING FEE" asset only) and the relative position message `P`( including the isolated position on which the "FUNDING FEE" occurs only, without any other position message).  


##Event: Order Update


> **Payload:**

```javascript
{
  
  "e":"ORDER_TRADE_UPDATE",		// Event Type
  "E":1568879465651,			// Event Time
  "T": 1568879465650,			//	Transaction Time
  "o":{								
    "s":"BTCUSDT",			    // Symbol
    "c":"TEST",				    // Client Order Id
      // special client order id:
      // starts with "autoclose-": liquidation order
      // "adl_autoclose": ADL auto close order
    "S":"SELL",				    // Side
    "o":"LIMIT",			    // Order Type
    "f":"GTC",				    // Time in Force
    "q":"0.001",			    // Original Quantity
    "p":"9910",				    // Original Price
    "ap":"0",				    // Average Price
    "sp":"0",				    // Stop Price
    "x":"NEW",				    // Execution Type
    "X":"NEW",				    // Order Status
    "i":8886774,			    // Order Id
    "l":"0",				    // Order Last Filled Quantity
    "z":"0",				    // Order Filled Accumulated Quantity
    "L":"0",				    // Last Filled Price
    "N": "USDT",                // Commission Asset, will not push if no commission
    "n": "0",                   // Commission, will not push if no commission
    "T":1568879465651,		    // Order Trade Time
    "t":0,			            // Trade Id
    "b":"0",			        // Bids Notional
    "a":"9.91",				    // Ask Notional
    "m": false,				    // Is this trade the maker side?
    "R":false,					// Is this reduce only
    "wt": "CONTRACT_PRICE",      // stop price working type
    "cp":false,						// If Close-All, pushed with conditional order
    "AP":"7476.89",				// Activation Price, only puhed with TRAILING_STOP_MARKET order
    "cr":"5.0"					// Callback Rate, only puhed with TRAILING_STOP_MARKET order
  }
  
}
```

When new order created, order status changed will push such event.
event type is `ORDER_TRADE_UPDATE`.




**Side**

* BUY 
* SELL 

**Order Type**

* MARKET 
* LIMIT
* STOP
* TAKE_PROFIT
* LIQUIDATION

**Execution Type**

* NEW
* PARTIAL_FILL
* FILL
* CANCELED
* REJECTED
* CALCULATED		 - Liquidation Execution
* EXPIRED
* TRADE
* RESTATED 

**Order Status**

* NEW
* PARTIALLY_FILLED
* FILLED
* CANCELED
* REPLACED
* STOPPED
* REJECTED
* EXPIRED
* NEW_INSURANCE     - Liquidation with Insurance Fund
* NEW_ADL				- Counterparty Liquidation`

**Time in force**

* GTC
* IOC
* FOK
* GTX

**Working Type**

* MARK_PRICE
* CONTRACT_PRICE 

#Error Codes

> Here is the error JSON payload:
 
```javascript
{
  "code":-1121,
  "msg":"Invalid symbol."
}
```

Errors consist of two parts: an error code and a message.    
Codes are universal,but messages can vary. 



## 10xx - General Server or Network issues
### -1000 UNKNOWN
 * An unknown error occured while processing the request.

### -1001 DISCONNECTED
 * Internal error; unable to process your request. Please try again.

### -1002 UNAUTHORIZED
 * You are not authorized to execute this request.

### -1003 TOO_MANY_REQUESTS
 * Too many requests queued.
 * Too many requests; please use the websocket for live updates.
 * Too many requests; current limit is %s requests per minute. Please use the websocket for live updates to avoid polling the API.
 * Way too many requests; IP banned until %s. Please use the websocket for live updates to avoid bans.
 
### -1004 DUPLICATE_IP
 * This IP is already on the white list

### -1005 NO_SUCH_IP
 * No such IP has been white listed
 
### -1006 UNEXPECTED_RESP
 * An unexpected response was received from the message bus. Execution status unknown.

### -1007 TIMEOUT
 * Timeout waiting for response from backend server. Send status unknown; execution status unknown.

### -1010 ERROR_MSG_RECEIVED
 * ERROR_MSG_RECEIVED.  
 
### -1011 NON_WHITE_LIST
 * This IP cannot access this route. 
 
### -1013 INVALID_MESSAGE
* INVALID_MESSAGE.

### -1014 UNKNOWN_ORDER_COMPOSITION
 * Unsupported order combination.

### -1015 TOO_MANY_ORDERS
 * Too many new orders.
 * Too many new orders; current limit is %s orders per %s.

### -1016 SERVICE_SHUTTING_DOWN
 * This service is no longer available.

### -1020 UNSUPPORTED_OPERATION
 * This operation is not supported.

### -1021 INVALID_TIMESTAMP
 * Timestamp for this request is outside of the recvWindow.
 * Timestamp for this request was 1000ms ahead of the server's time.

### -1022 INVALID_SIGNATURE
 * Signature for this request is not valid.

### -1023 START_TIME_GREATER_THAN_END_TIME
 * Start time is greater than end time.


## 11xx - Request issues
### -1100 ILLEGAL_CHARS
 * Illegal characters found in a parameter.
 * Illegal characters found in parameter '%s'; legal range is '%s'.

### -1101 TOO_MANY_PARAMETERS
 * Too many parameters sent for this endpoint.
 * Too many parameters; expected '%s' and received '%s'.
 * Duplicate values for a parameter detected.

### -1102 MANDATORY_PARAM_EMPTY_OR_MALFORMED
 * A mandatory parameter was not sent, was empty/null, or malformed.
 * Mandatory parameter '%s' was not sent, was empty/null, or malformed.
 * Param '%s' or '%s' must be sent, but both were empty/null!

### -1103 UNKNOWN_PARAM
 * An unknown parameter was sent.

### -1104 UNREAD_PARAMETERS
 * Not all sent parameters were read.
 * Not all sent parameters were read; read '%s' parameter(s) but was sent '%s'.

### -1105 PARAM_EMPTY
 * A parameter was empty.
 * Parameter '%s' was empty.

### -1106 PARAM_NOT_REQUIRED
 * A parameter was sent when not required.
 * Parameter '%s' sent when not required.

### -1108 BAD_ASSET
 * Invalid asset.

### -1109 BAD_ACCOUNT
 * Invalid account.

### -1110 BAD_INSTRUMENT_TYPE
 * Invalid symbolType.
 
### -1111 BAD_PRECISION
 * Precision is over the maximum defined for this asset.

### -1112 NO_DEPTH
 * No orders on book for symbol.
 
### -1113 WITHDRAW_NOT_NEGATIVE
 * Withdrawal amount must be negative.
 
### -1114 TIF_NOT_REQUIRED
 * TimeInForce parameter sent when not required.

### -1115 INVALID_TIF
 * Invalid timeInForce.

### -1116 INVALID_ORDER_TYPE
 * Invalid orderType.

### -1117 INVALID_SIDE
 * Invalid side.

### -1118 EMPTY_NEW_CL_ORD_ID
 * New client order ID was empty.

### -1119 EMPTY_ORG_CL_ORD_ID
 * Original client order ID was empty.

### -1120 BAD_INTERVAL
 * Invalid interval.

### -1121 BAD_SYMBOL
 * Invalid symbol.

### -1125 INVALID_LISTEN_KEY
 * This listenKey does not exist.

### -1127 MORE_THAN_XX_HOURS
 * Lookup interval is too big.
 * More than %s hours between startTime and endTime.

### -1128 OPTIONAL_PARAMS_BAD_COMBO
 * Combination of optional parameters invalid.

### -1130 INVALID_PARAMETER
 * Invalid data sent for a parameter.
 * Data sent for paramter '%s' is not valid.

## 20xx - Processing Issues
### -2008 BAD_API_ID
 * Invalid Api-Key ID
 
### -2010 NEW_ORDER_REJECTED
 * NEW_ORDER_REJECTED

### -2011 CANCEL_REJECTED
 * CANCEL_REJECTED

### -2013 NO_SUCH_ORDER
 * Order does not exist.

### -2014 BAD_API_KEY_FMT
 * API-key format invalid.

### -2015 REJECTED_MBX_KEY
 * Invalid API-key, IP, or permissions for action.

### -2016 NO_TRADING_WINDOW
 * No trading window could be found for the symbol. Try ticker/24hrs instead.

### -2018 BALANCE_NOT_SUFFICIENT
 * Balance is insufficient.

### -2019 MARGIN_NOT_SUFFICIEN
 * Margin is insufficient.

### -2020 UNABLE_TO_FILL
 * Unable to fill.

### -2021 ORDER_WOULD_IMMEDIATELY_TRIGGER
 * Order would immediately trigger.

### -2022 REDUCE_ONLY_REJECT
 * ReduceOnly Order is rejected.

### -2023 USER_IN_LIQUIDATION
 * User in liquidation mode now.

### -2024 POSITION_NOT_SUFFICIENT
 * Position is not sufficient.

### -2025 MAX_OPEN_ORDER_EXCEEDED
 * Reach max open order limit.

### -2026 REDUCE_ONLY_ORDER_TYPE_NOT_SUPPORTED
 * This OrderType is not supported when reduceOnly.
 

## 40xx - Filters and other Issues
### -4000 INVALID_ORDER_STATUS
 * Invalid order status.

### -4001 PRICE_LESS_THAN_ZERO
 * Price less than 0.

### -4002 PRICE_GREATER_THAN_MAX_PRICE
 * Price greater than max price.
 
### -4003 QTY_LESS_THAN_ZERO
 * Quantity less than zero.

### -4004 QTY_LESS_THAN_MIN_QTY
 * Quantity less than min quantity.
 
### -4005 QTY_GREATER_THAN_MAX_QTY
 * Quantity greater than max quantity. 

### -4006 STOP_PRICE_LESS_THAN_ZERO
 * Stop price less than zero. 
 
### -4007 STOP_PRICE_GREATER_THAN_MAX_PRICE
 * Stop price greater than max price. 

### -4008 TICK_SIZE_LESS_THAN_ZERO
 * Tick size less than zero.

### -4009 MAX_PRICE_LESS_THAN_MIN_PRICE
 * Max price less than min price.

### -4010 MAX_QTY_LESS_THAN_MIN_QTY
 * Max qty less than min qty.

### -4011 STEP_SIZE_LESS_THAN_ZERO
 * Step size less than zero.

### -4012 MAX_NUM_ORDERS_LESS_THAN_ZERO
 * Max mum orders less than zero.

### -4013 PRICE_LESS_THAN_MIN_PRICE
 * Price less than min price.

### -4014 PRICE_NOT_INCREASED_BY_TICK_SIZE
 * Price not increased by tick size.
 
### -4015 INVALID_CL_ORD_ID_LEN
 * Client order id is not valid.
 * Client order id length should be less than 32 chars

### -4016 PRICE_HIGHTER_THAN_MULTIPLIER_UP
 * Price is higher than mark price multiplier cap.

### -4017 MULTIPLIER_UP_LESS_THAN_ZERO
 * Multiplier up less than zero.

### -4018 MULTIPLIER_DOWN_LESS_THAN_ZERO
 * Multiplier down less than zero.

### -4019 COMPOSITE_SCALE_OVERFLOW
 * Composite scale too large.

### -4020 TARGET_STRATEGY_INVALID
 * Target strategy invalid for orderType '%s',reduceOnly '%b'.

### -4021 INVALID_DEPTH_LIMIT
 * Invalid depth limit.
 * '%s' is not valid depth limit.

### -4022 WRONG_MARKET_STATUS
 * market status sent is not valid.
 
### -4023 QTY_NOT_INCREASED_BY_STEP_SIZE
 * Qty not increased by step size.

### -4024 PRICE_LOWER_THAN_MULTIPLIER_DOWN
 * Price is lower than mark price multiplier floor.

### -4025 MULTIPLIER_DECIMAL_LESS_THAN_ZERO
 * Multiplier decimal less than zero.

### -4026 COMMISSION_INVALID
 * Commission invalid.
 * `%s` less than zero.
 * `%s` absolute value greater than `%s`

### -4027 INVALID_ACCOUNT_TYPE
 * Invalid account type.

### -4028 INVALID_LEVERAGE
 * Invalid leverage
 * Leverage `%s` is not valid
 * Leverage `%s` already exist with `%s`

### -4029 INVALID_TICK_SIZE_PRECISION
 * Tick size precision is invalid.

### -4030 INVALID_STEP_SIZE_PRECISION
 * Step size precision is invalid.

### -4031 INVALID_WORKING_TYPE
 * Invalid parameter working type
 * Invalid parameter working type: `%s`

### -4032 EXCEED_MAX_CANCEL_ORDER_SIZE
 * Exceed maximum cancel order size.
 * Invalid parameter working type: `%s`

### -4033 INSURANCE_ACCOUNT_NOT_FOUND
 * Insurance account not found.

### -4044 INVALID_BALANCE_TYPE
 * Balance Type is invalid.

### -4045 MAX_STOP_ORDER_EXCEEDED
 * Reach max stop order limit.

### -4046 NO_NEED_TO_CHANGE_MARGIN_TYPE
 * No need to change margin type.

### -4047 THERE_EXISTS_OPEN_ORDERS
 * Margin type cannot be changed if there exists open orders.

### -4048 THERE_EXISTS_QUANTITY
 * Margin type cannot be changed if there exists position.

### -4049 ADD_ISOLATED_MARGIN_REJECT
 * Add margin only support for isolated position.

### -4050 CROSS_BALANCE_INSUFFICIENT
 * Cross balance insufficient.

### -4051 ISOLATED_BALANCE_INSUFFICIENT
 * Isolated balance insufficient.

### -4052 NO_NEED_TO_CHANGE_AUTO_ADD_MARGIN
 * No need to change auto add margin.

### -4053 AUTO_ADD_CROSSED_MARGIN_REJECT
 * Auto add margin only support for isolated position.

### -4054 ADD_ISOLATED_MARGIN_NO_POSITION_REJECT
 * Cannot add position margin: position is 0.

### -4055 AMOUNT_MUST_BE_POSITIVE
 * Amount must be positive.

 
## Messages for -1010 ERROR_MSG_RECEIVED, -2010 NEW_ORDER_REJECTED, and -2011 CANCEL_REJECTED
This code is sent when an error has been returned by the matching engine.
The following messages which will indicate the specific error:


Error message | Description
------------ | ------------
"Unknown order sent." | The order (by either `orderId`, `clientOrderId`, `origClientOrderId`) could not be found
"Duplicate order sent." | The `clientOrderId` is already in use
"Market is closed." | The symbol is not trading
"Account has insufficient balance for requested action." | Not enough funds to complete the action
"Market orders are not supported for this symbol." | `MARKET` is not enabled on the symbol
"Stop loss orders are not supported for this symbol." | `STOP_LOSS` is not enabled on the symbol
"Stop loss limit orders are not supported for this symbol." | `STOP_LOSS_LIMIT` is not enabled on the symbol
"Take profit orders are not supported for this symbol." | `TAKE_PROFIT` is not enabled on the symbol
"Take profit limit orders are not supported for this symbol." | `TAKE_PROFIT_LIMIT` is not enabled on the symbol
"Price * QTY is zero or less." | `price` * `quantity` is too low
"This action disabled is on this account." | Contact customer support; some actions have been disabled on the account.
"Unsupported order combination" | The `orderType`, `timeInForce`, `stopPrice` combination isn't allowed.
"Order would trigger immediately." | The order's stop price is not valid when compared to the last traded price.
"Cancel order is invalid. Check origClientOrderId and orderId." | No `origClientOrderId` or `orderId` was sent in.
"Order would immediately match and take." | `GTX` order would immediately match and trade, and not be a pure maker order.

## -9xxx Filter failures
Error message | Description
------------ | ------------
"Filter failure: PRICE_FILTER" | `price` is too high, too low, and/or not following the tick size rule for the symbol.
"Filter failure: LOT_SIZE" | `quantity` is too high, too low, and/or not following the step size rule for the symbol.
"Filter failure: MARKET_LOT_SIZE" | `MARKET` order's `quantity` is too high, too low, and/or not following the step size rule for the symbol.
"Filter failure: MAX_NUM_ORDERS" | Account has too many open orders on the symbol.
"Filter failure: PERCENT_PRICE" | `price` is X% too high or X% too low from the average weighted price over the last Y minutes.
