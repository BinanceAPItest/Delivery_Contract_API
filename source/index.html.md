---
title: Binance API Documentation
language_tabs: # must be one of https://git.io/vQNgJ
  #- shell
  #- javascript
  #- jason

toc_footers:
  - <a href='https://www.binance.com/'>Binance Exchange</a>

includes:

search: true

---


# Change Log


<font size=4>**2020-05-06**</font>

* New endpoints for Mining:
	* `GET /sapi/v1/mining/pub/algoList`
	* `GET /sapi/v1/mining/pub/coinList`
	* `GET /sapi/v1/mining/worker/detail`
	* `GET /sapi/v1/mining/worker/list`
	* `GET /sapi/v1/mining/payment/list`
	* `GET /sapi/v1/mining/statistics/user/status`
	* `GET /sapi/v1/mining/statistics/user/list` 

---



<font size=4>**2020-05-03**</font>

* New request limit for some margin endpoints
  * Changes on these endpoints:
    * `POST /sapi/v1/margin/transfer`
    * `POST /sapi/v1/margin/loan`
    * `POST /sapi/v1/margin/repay`
  * Limit to 1 every 2 seconds。
  * Request beyond the limit will receive HTTP status code `429`。

---

<font size=4>**2020-05-01**</font>

* From 2020-05-01 UTC 00:00, all symbols will have a limit of 200 open orders using the [MAX_NUM_ORDERS](https://binance-docs.github.io/apidocs/spot/en/#filters) filter.
    * No existing orders will be removed or canceled.
    * Accounts that have 200 or more open orders on a symbol will not be able to place new orders on that symbol until the open order count is below 200.
    * OCO orders count as 2 open orders before the `LIMIT` order is touched or the `STOP_LOSS` (or `STOP_LOSS_LIMIT`) order is triggered; once this happens the other order is canceled and will no longer count as an open order.

---

<font size=4>**2020-04-25**</font>

<font size=4>SPOT API</font>

* New field `permissions`
    * Defines the trading permissions that are allowed on accounts and symbols.
    * `permissions` is an enum array; values:
        * `SPOT` 
        * `MARGIN`
    * `permissions` will replace `isSpotTradingAllowed` and `isMarginTradingAllowed` on `GET api/v3/exchangeInfo` in future API versions (v4+).
    * For an account to trade on a symbol, the account and symbol must share at least 1 permission in common.
* Updates to GET `api/v3/exchangeInfo`
    *  New field `permissions` added.
    *  New field `quoteAssetPrecision` added; a duplicate of the `quotePrecision` field. `quotePrecision` will be removed in future API versions (v4+).
* Updates to GET `api/v3/account`
    * New field `permissions` added.
* New endpoint DELETE `api/v3/openOrders`
    * This will allow a user to cancel all open orders on a single symbol.
    * This endpoint will cancel all open orders including OCO orders.
* Orders can be canceled via the API on symbols in the `BREAK` or `HALT` status.

<font size=4> USER DATA STREAM </font>

* `OutboundAccountInfo` has new field `P` which shows the trading permissions of the account.

---

<font size=4>**2020-04-23**</font>

WEB SOCKET STREAM

* WebSocket connections have a limit of 5 incoming messages per second. A message is considered:
    * A PING frame
    * A PONG frame
    * A JSON control message (e.g. subscribe, unsubscribe)
* A connection that goes beyond the limit will be disconnected; IPs that are repeatedly disconnected may be banned.
* A single connection can listen to a maximum of 1024 streams.


<font size=4>**2020-04-16**</font>

* New fields in response to endpoint``GET /sapi/v1/lending/daily/token/position``：
	* `todayPurchasedAmount` for user's purchased amount today

* New lending endpoints for customized fixed projects:
	* ``GET /sapi/v1/lending/project/list``
	* ``POST /sapi/v1/lending/customizedFixed/purchase``
	* ``GET /sapi/v1/lending/project/position/list`` 


---




<font size=4>**2020-04-02**</font>

* New fields in response to endpoint``GET /sapi/v1/capital/config/getall``：
	* `minConfirm` for min number for balance confirmation
	* `unLockConfirm` for confirmation number for balance unlcok

---



<font size=4>**2020-03-24**</font>

* `MAX_POSITION` filter added.
    * This filter defines the allowed maximum position an account can have on the base asset of a symbol. An account's position defined as the sum of the account's:
        * free balance of the base asset
        * locked balance of the base asset
        * sum of the qty of all open BUY orders

    * `BUY` orders will be rejected if the account's position is greater than the maximum position allowed.

---

<font size=4>**2020-03-13**</font>

* New parameter `transactionFeeFlag` is available in endpoint:
	* ``POST /sapi/v1/capital/withdraw/apply`` and
	* ``POST /wapi/v3/withdraw.html``

---

<font size=4>**2020-02-05**</font>

* New sub account endpoints:
	* ``POST /sapi/v1/sub-account/futures/transfer`` to transfer between futures and spot accout of sub-account.
	* ``POST /sapi/v1/sub-account/margin/transfer`` to transfer between margin and spot accout of sub-account.
	* ``POST /sapi/v1/sub-account/transfer/subToSub`` to transfer to master account by sub-account.
	* ``POST /sapi/v1/sub-account/transfer/subToMaster`` to transfer to another sub-account of same master by sub-account.
	* ``GET /sapi/v1/sub-account/transfer/subUserHistory`` to get transfer history of sub-account.
 

---

<font size=4>**2020-01-15**</font>

* New parameter `withdrawOrderId` for client customized withdraw id for endpoint ``POST /wapi/v3/withdraw.html``.

* New field `withdrawOrderId` in response to ``GET /wapi/v3/withdrawHistory.html``


---

<font size=4>**2019-12-25**</font>

* New endpoints for Binance Savings:
	* ``GET /sapi/v1/lending/daily/product/list``
	* ``GET /sapi/v1/lending/daily/userLeftQuota``
	* ``POST /sapi/v1/lending/daily/purchase ``
	* ``GET /sapi/v1/lending/daily/userRedemptionQuota``
	* ``POST /sapi/v1/lending/daily/redeem``
	* ``GET /sapi/v1/lending/daily/token/position``
	* ``GET /sapi/v1/lending/union/account``
	* ``GET /sapi/v1/lending/union/purchaseRecord``
	* ``GET /sapi/v1/lending/union/redemptionRecord``
	* ``GET /sapi/v1/lending/union/interestHistory``

* Added time interval limit in    
``GET  /sapi/v1/capital/withdraw/history``,    
``GET /wapi/v3/withdrawHistory.html``,    
``GET /sapi/v1/capital/deposit/hisrec`` and    
``GET /wapi/v3/depositHistory.html``:
	* The default `startTime` is 90 days from current time, and the default `endTime` is current time. 
	* Please notice the default `startTime` and `endTime` to make sure that time interval is within 0-90 days.
	* If both ``startTime`` and ``endTime`` are sent, time between ``startTime`` and ``endTime`` must be less than 90 days.


---
<font size=4>**2019-12-18**</font>

* New endpoint to get daily snapshot of account:   
	``GET /sapi/v1/accountSnapshot``

---
<font size=4>**2019-11-30**</font>

* Added parameter `sideEffectType` in `POST  /sapi/v1/margin/order (HMAC SHA256)` with enums:
	* `NO_SIDE_EFFECT` for normal trade order;
	* `MARGIN_BUY` for margin trade order;
	* `AUTO_REPAY` for making auto repayment after order filled.

* New field `marginBuyBorrowAmount` and `marginBuyBorrowAsset` in `FULL` response to `POST  /sapi/v1/margin/order (HMAC SHA256)`

---
<font size=4>**2019-11-28**</font>

* New SAPI endpont to disable fast withdraw switch:    
``
POST /sapi/v1/account/disableFastWithdrawSwitch (HMAC SHA256)
``
* New SAPI endpont to enable fast withdraw switch:    
``
POST /sapi/v1/account/enableFastWithdrawSwitch (HMAC SHA256)
``

---


<font size=4>**2019-11-22**</font>

* Quote Order Qty Market orders have been enabled on all symbols.
    * Quote Order Qty `MARKET` orders allow a user to specify the total `quoteOrderQty` spent or received in the `MARKET` order.
    * Quote Order Qty `MARKET` orders will not break `LOT_SIZE` filter rules; the order will execute a quantity that will have the notional value as close as possible to `quoteOrderQty`.
    * Using `BNBBTC` as an example:
        * On the `BUY` side, the order will buy as many BNB as `quoteOrderQty` BTC can.
        * On the `SELL` side, the order will sell as much BNB as needed to receive `quoteOrderQty` BTC.

---
<font size=4>**2019-11-19**</font>

* `GET /sapi/v1/sub-account/margin/account` has new field:
 	`marginTradeCoeffVo` which contains
	* `forceLiquidationBar` for liquidation margin ratio
	* `marginCallBar` for margin call margin ratio
	* `normalBar` for initial margin ratio



---
<font size=4>**2019-11-13**</font>

<font size=4>Rest API </font>

* api/v3/exchangeInfo has new fields:
    * `quoteOrderQtyMarketAllowed`
    * `baseCommissionDecimalPlaces`
    * `quoteCommissionDecimalPlaces`
* `MARKET` orders have a new optional field: `quoteOrderQty` used to specify the quote quantity to BUY or SELL. This cannot be used in combination with `quantity`.
    * The exact timing that `quoteOrderQty` MARKET orders will be enabled is TBD. There will be a separate announcement and further details at that time.
* All order query endpoints will return a new field `origQuoteOrderQty` in the JSON payload. (e.g. GET api/v3/allOrders)

```json
	{
      "code": -1128,
      "msg": "Combination of optional parameters invalid. Recommendation: 'stopLimitTimeInForce' should also be sent."
	}
```

* Updated error messages for  -1128
	* Sending an `OCO` with a `stopLimitPrice` but without a `stopLimitTimeInForce` will return the error:

* Updated error messages for -1003 to specify the limit is referring to the request weight, not to the number of requests.


    
    
**Deprecation of v1 endpoints**:

By end of Q1 2020, the following endpoints will be removed from the API. The documentation has been updated to use the v3 versions of these endpoints.

* GET api/v1/depth
* GET api/v1/historicalTrades
* GET api/v1/aggTrades
* GET api/v1/klines
* GET api/v1/ticker/24hr
* GET api/v1/ticker/price
* GET api/v1/exchangeInfo
* POST api/v1/userDataStream
* PUT api/v1/userDataStream
* GET api/v1/ping
* GET api/v1/time
* GET api/v1/ticker/bookTicker

**These endpoints however, will NOT be migrated to v3. Please use the following endpoints instead moving forward.**

<table>
<tr>
<th>Old V1 Endpoints</th>
<th>New V3 Endpoints</th>
</tr>
<tr>
<td>GET api/v1/ticker/allPrices</td>
<td>GET api/v3/ticker/price</td>
</tr>
<tr>
<td>GET api/v1/ticker/allBookTickers</td>
<td>GET api/v3/ticker/bookTicker</td>
</tr>
</table>

<font size=4>USER DATA STREAM </font>

* Changes to`executionReport` event
    * If the C field is empty, it will now properly return `null`, instead of `"null"`.
    * New field Q which represents the `quoteOrderQty`.

* `balanceUpdate` event type added
    * This event occurs when funds are deposited or withdrawn from your account.

<font size=4> WEB SOCKET STREAM</font>

* WSS now supports live subscribing/unsubscribing to streams.

---

<font size=4>**2019-11-08**</font>

* New sapi for subaccount management on margin and futures:
	* ``GET /sapi/v1/sub-account/status (HMAC SHA256)``
	* ``POST /sapi/v1/sub-account/margin/enable (HMAC SHA256)``
	* ``GET /sapi/v1/sub-account/margin/account (HMAC SHA256)``
	* ``GET /sapi/v1/sub-account/margin/accountSummary (HMAC SHA256)``
	* ``POST /sapi/v1/sub-account/futures/enable (HMAC SHA256)``
	* ``GET /sapi/v1/sub-account/futures/account (HMAC SHA256)`` 
	* ``GET /sapi/v1/sub-account/futures/accountSummary (HMAC SHA256)`` 
	* ``GET /sapi/v1/sub-account/futures/positionRisk (HMAC SHA256)``   


---
<font size=4>**2019-11-04**</font>

* New sapi endpoints for subaccount wallet.
  * `GET /sapi/v1/capital/deposit/subAddress (HMAC SHA256))`: fetch subaccount deposit address.
  * `GET /sapi/v1/capital/deposit/subHisrec (HMAC SHA256))`: fetch subaccount deposit history.

---
<font size=4>**2019-10-29**</font>

* New sapi endpoints for wallet.
  * `POST /sapi/v1/capital/withdraw/apply (HMAC SHA256)`: withdraw.
  * `Get /sapi/v1/capital/withdraw/history (HMAC SHA256)`: fetch withdraw history with network.
 
---


<font size=4>**2019-10-14**</font>

* New sapi endpoints for wallet.
  * `GET /sapi/v1/capital/config/getall (HMAC SHA256)`: get all coins' information for user.
  * `GET /sapi/v1/capital/deposit/hisrec (HMAC SHA256)`: fetch deposit history with network.
  * `GET /sapi/v1/capital/deposit/address (HMAC SHA256)`: fetch deposit address with network.

---
<font size=4>**2019-10-11**</font>

* Added parameter `network` in `POST /wapi/v3/withdraw.html` so that asset can be withdrawed with specific network.

---
<font size=4>**2019-09-09**</font>

* New WebSocket streams for bookTickers added: `<symbol>@bookTicker` and `!bookTicker`. 

---
<font size=4>**2019-09-03**</font>

* Faster order book data with 100ms updates: `<symbol>@depth@100ms` and `<symbol>@depth#@100ms`
* Added "Update Speed:" to `Websocket Market Streams`
* Removed deprecated v1 endpoints as per previous announcement:
    * GET api/v1/order
    * GET api/v1/openOrders
    * POST api/v1/order
    * DELETE api/v1/order
    * GET api/v1/allOrders
    * GET api/v1/account
    * GET api/v1/myTrades


---
<font size=4>**2019-08-16**</font>

* GET api/v1/depth `limit` of 10000 has been temporarily removed

* In Q4 2017, the following endpoints were deprecated and removed from the API documentation. They have been permanently removed from the API as of this version. We apologize for the omission from the original changelog:
    * GET api/v1/order
    * GET api/v1/openOrders
    * POST api/v1/order
    * DELETE api/v1/order
    * GET api/v1/allOrders
    * GET api/v1/account
    * GET api/v1/myTrades

* Streams, endpoints, parameters, payloads, etc. described in the documents in this repository are **considered official** and **supported**. The use of any other streams, endpoints, parameters, or payloads, etc. is **not supported; use them at your own risk and with no guarantees.**

---
<font size=4>**2019-09-15**</font>

<font size=4>Rest API</font>

* New order type: OCO ("One Cancels the Other")
    * An OCO has 2 orders: (also known as legs in financial terms)
        * ```STOP_LOSS``` or ```STOP_LOSS_LIMIT``` leg
        * ```LIMIT_MAKER``` leg

    * Price Restrictions:
        * ```SELL Orders``` : Limit Price > Last Price > Stop Price
        * ```BUY Orders``` : Limit Price < Last Price < Stop Price
        * As stated, the prices must "straddle" the last traded price on the symbol. EX: If the last price is 10:
            * A SELL OCO must have the limit price greater than 10, and the stop price less than 10.
            * A BUY OCO must have a limit price less than 10, and the stop price greater than 10.

    * Quantity Restrictions:
        * Both legs must have the **same quantity**.
        * ```ICEBERG``` quantities however, do not have to be the same.

    * Execution Order:
        * If the ```LIMIT_MAKER``` is touched, the limit maker leg will be executed first BEFORE canceling the Stop Loss Leg.
        * if the Market Price moves such that the ```STOP_LOSS``` or ```STOP_LOSS_LIMIT``` will trigger, the Limit Maker leg will be cancelled BEFORE executing the ```STOP_LOSS``` Leg.

    * Cancelling an OCO
        * Cancelling either order leg will cancel the entire OCO.
        * The entire OCO can be canceled via the ```orderListId``` or the ```listClientOrderId```.

    * New Enums for OCO:
        1. ```ListStatusType```
            * ```RESPONSE``` - used when ListStatus is responding to a failed action. (either order list placement or cancellation)
            * ```EXEC_STARTED``` - used when an order list has been placed or there is an update to a list's status.
            * ```ALL_DONE``` - used when an order list has finished executing and is no longer active.
        1. ```ListOrderStatus```
            * ```EXECUTING``` - used when an order list has been placed or there is an update to a list's status.
            * ```ALL_DONE``` - used when an order list has finished executing and is no longer active.
            * ```REJECT``` - used when ListStatus is responding to a failed action. (either order list placement or cancellation)
        1. ```ContingencyType```
            * ```OCO``` - specifies the type of order list.

    * New Endpoints:
        * POST api/v3/order/oco
        * DELETE api/v3/orderList
        * GET api/v3/orderList

* ```recvWindow``` cannot exceed 60000.
* New `intervalLetter` values for headers:
    * SECOND => S
    * MINUTE => M
    * HOUR => H
    * DAY => D
* New Headers `X-MBX-USED-WEIGHT-(intervalNum)(intervalLetter)` will give your current used request weight for the (intervalNum)(intervalLetter) rate limiter. For example, if there is a one minute request rate weight limiter set, you will get a `X-MBX-USED-WEIGHT-1M` header in the response. The legacy header `X-MBX-USED-WEIGHT` will still be returned and will represent the current used weight for the one minute request rate weight limit.
* New Header `X-MBX-ORDER-COUNT-(intervalNum)(intervalLetter)`that is updated on any valid order placement and tracks your current order count for the interval; rejected/unsuccessful orders are not guaranteed to have `X-MBX-ORDER-COUNT-**` headers in the response.
    * Eg. `X-MBX-ORDER-COUNT-1S` for "orders per 1 second" and `X-MBX-ORDER-COUNT-1D` for orders per "one day"
* GET api/v1/depth now supports `limit` 5000 and 10000; weights are 50 and 100 respectively.
* GET api/v1/exchangeInfo has a new parameter `ocoAllowed`.


<font size=4>USER DATA STREAM</font>

* ```executionReport``` event now contains "g" which has the ```orderListId```; it will be set to -1 for non-OCO orders.
* New Event Type ```listStatus```; ```listStatus``` is sent on an update to any OCO order.
* New Event Type ```outboundAccountPosition```; ```outboundAccountPosition``` is sent any time an account's balance changes and contains the assets that could have changed by the event that generated the balance change (a deposit, withdrawal, trade, order placement, or cancelation).


<font size=4>NEW ERRORS</font>

* **-1131 BAD_RECV_WINDOW**
    * ```recvWindow``` must be less than 60000
* **-1099 Not found, authenticated, or authorized**
    * This replaces error code -1999


<font size=4>NEW -2011 ERRORS</font>

* **OCO_BAD_ORDER_PARAMS**
    * A parameter for one of the orders is incorrect.
* **OCO_BAD_PRICES**
    * The relationship of the prices for the orders is not correct.
* **UNSUPPORTED_ORD_OCO**
    * OCO orders are not supported for this symbol.

---
<font size=4>**2019-03-12**</font>

<font size=4>Rest API</font>

* X-MBX-USED-WEIGHT header added to Rest API responses.
* Retry-After header added to Rest API 418 and 429 responses.
* When canceling the Rest API can now return `errorCode` -1013 OR -2011 if the symbol's `status` isn't `TRADING`.
* `api/v1/depth` no longer has the ignored and empty `[]`.
* `api/v3/myTrades` now returns `quoteQty`; the price * qty of for the trade.
  

<font size=4>Websocket streams</font>

* `<symbol>@depth` and `<symbol>@depthX` streams no longer have the ignored and empty `[]`.
  

<font size=4>System improvements</font>

* Matching Engine stability/reliability improvements.
* Rest API performance improvements.

---
<font size=4>**2018-11-13**</font>

<font size=4>Rest API</font>

* Can now cancel orders through the Rest API during a trading ban.
* New filters: `PERCENT_PRICE`, `MARKET_LOT_SIZE`, `MAX_NUM_ICEBERG_ORDERS`.
* Added `RAW_REQUST` rate limit. Limits based on the number of requests over X minutes regardless of weight.
* /api/v3/ticker/price increased to weight of 2 for a no symbol query.
* /api/v3/ticker/bookTicker increased weight of 2 for a no symbol query.
* DELETE /api/v3/order will now return an execution report of the final state of the order.
* `MIN_NOTIONAL` filter has two new parameters: `applyToMarket` (whether or not the filter is applied to MARKET orders) and `avgPriceMins` (the number of minutes over which the price averaged for the notional estimation).
* `intervalNum` added to /api/v1/exchangeInfo limits. `intervalNum` describes the amount of the interval. For example: `intervalNum` 5, with `interval` minute, means "every 5 minutes".
  

<font size=4>Explanation for the average price calculation:</font>

1. (qty * price) of all trades / numTrades of the trades over previous 5 minutes.

2. If there is no trade in the last 5 minutes, it takes the first trade that happened outside of the 5min window.
   For example if the last trade was 20 minutes ago, that trade's price is the 5 min average.

3. If there is no trade on the symbol, there is no average price and market orders cannot be placed.
   On a new symbol with `applyToMarket` enabled on the `MIN_NOTIONAL` filter, market orders cannot be placed until there is at least 1 trade.

4. The current average price can be checked here: `https://api.binance.com/api/v3/avgPrice?symbol=<symbol>`
   For example:
   https://api.binance.com/api/v3/avgPrice?symbol=BNBUSDT


<font size=4>User data stream</font>

* `Last quote asset transacted quantity` (as variable `Y`) added to execution reports. Represents the `lastPrice` * `lastQty` (`L` * `l`).

---
<font size=4>**2018-07-18**</font>

<font size=4>Rest API</font>

*  New filter: `ICEBERG_PARTS`
*  `POST api/v3/order` new defaults for `newOrderRespType`. `ACK`, `RESULT`, or `FULL`; `MARKET` and `LIMIT` order types default to `FULL`, all other orders default to `ACK`.
*  POST api/v3/order `RESULT` and `FULL` responses now have `cummulativeQuoteQty`
*  GET api/v3/openOrders with no symbol weight reduced to 40.
*  GET api/v3/ticker/24hr with no symbol weight reduced to 40.
*  Max amount of trades from GET /api/v1/trades increased to 1000.
*  Max amount of trades from GET /api/v1/historicalTrades increased to 1000.
*  Max amount of aggregate trades from GET /api/v1/aggTrades increased to 1000.
*  Max amount of aggregate trades from GET /api/v1/klines increased to 1000.
*  Rest API Order lookups now return `updateTime` which represents the last time the order was updated; `time` is the order creation time.
*  Order lookup endpoints will now return `cummulativeQuoteQty`. If `cummulativeQuoteQty` is < 0, it means the data isn't available for this order at this time.
*  `REQUESTS` rate limit type changed to `REQUEST_WEIGHT`. This limit was always logically request weight and the previous name for it caused confusion.


<font size=4>User data stream</font>

*  `cummulativeQuoteQty` field added to order responses and execution reports (as variable `Z`). Represents the cummulative amount of the `quote` that has been spent (with a `BUY` order) or received (with a `SELL` order). Historical orders will have a value < 0 in this field indicating the data is not available at this time. `cummulativeQuoteQty` divided by `cummulativeQty` will give the average price for an order.
*  `O` (order creation time) added to execution reports

---
<font size=4>**2018-01-23**</font>

* GET /api/v1/historicalTrades weight decreased to 5
* GET /api/v1/aggTrades weight decreased to 1
* GET /api/v1/klines weight decreased to 1
* GET /api/v1/ticker/24hr all symbols weight decreased to number of trading symbols / 2
* GET /api/v3/allOrders weight decreased to 5
* GET /api/v3/myTrades weight decreased to 5
* GET /api/v3/account weight decreased to 5
* GET /api/v1/depth limit=500 weight decreased to 5
* GET /api/v1/depth limit=1000 weight decreased to 10
* -1003 error message updated to direct users to websockets

---
<font size=4>**2018-01-20**</font>

* GET /api/v1/ticker/24hr single symbol weight decreased to 1
* GET /api/v3/openOrders all symbols weight decreased to number of trading symbols / 2
* GET /api/v3/allOrders weight decreased to 15
* GET /api/v3/myTrades weight decreased to 15
* GET /api/v3/order weight decreased to 1
* myTrades will now return both sides of a self-trade/wash-trade

---
<font size=4>**2018-01-14**</font>

* GET /api/v1/aggTrades weight changed to 2
* GET /api/v1/klines weight changed to 2
* GET /api/v3/order weight changed to 2
* GET /api/v3/allOrders weight changed to 20
* GET /api/v3/account weight changed to 20
* GET /api/v3/myTrades weight changed to 20
* GET /api/v3/historicalTrades weight changed to 20



# Introduction

## API Key Setup

* Some endpoints will require an API Key. Please refer to [this page](https://binance.zendesk.com/hc/en-us/articles/360002502072-How-to-create-API) regarding API key creation.
* Once API key is created, it is recommended to set IP restrictions on the key for security reasons.
* **Never share your API key/secret key to ANYONE.**

<aside class="warning">
If the API keys were accidentally shared, please delete them immediately and create a new key.
</aside>

## API Key Restrictions

* After creating the API key, the default restrictions set will be to enable trade, allowing to make orders on the API.
* To **enable withdrawals via the API**, the API key restriction needs to be modified through the Binance UI.

## Enabling Accounts

### Spot Account

A `SPOT` account is provided by default upon creation of a Binance Account.

### Margin Account

To enable a `MARGIN` account for Margin Trading, please refer to the [Margin Trading Guide](https://www.binance.vision/tutorials/binance-margin-trading-guide)

## SPOT Testnet

Users can use the SPOT Testnet to practice `SPOT` trading.

Currently, this is only available via the API.

Please refer to the [SPOT Testnet page](https://testnet.binance.vision/) for more information and how to set up the Testnet API key.

## Postman Collections

There is now a Postman collection containing the API endpoints for quick and easy use.

This is recommended for new users who want to get a quick-start into using the API.

For more information please refer to this page: [Binance API Postman](https://github.com/binance-exchange/binance-api-postman)

## Contact Us

* [Binance API Telegram Group](https://t.me/binance_api_english)
    * For any questions in sudden drop in performance with the API and/or Websockets.
    * For any general questions about the API not covered in the documentation.
* [Binance Developers](https://dev.binance.vision/)
    * For any questions on your code implementation with the API and/or Websockets.
* [Binance Customer Support](https://www.binance.com/en/support-center)
    * For cases such as missing funds, help with 2FA, etc.

---

# General Info
## General API Information

* The base endpoint is: **https://api.binance.com**
* All endpoints return either a JSON object or array.
* Data is returned in **ascending** order. Oldest first, newest last.
* All time and timestamp related fields are in **milliseconds**.

### HTTP Return Codes
* HTTP `4XX` return codes are used for malformed requests;
  the issue is on the sender's side.
* HTTP `403` return code is used when the WAF Limit (Web Application Firewall) has been violated.
* HTTP `429` return code is used when breaking a request rate limit.
* HTTP `418` return code is used when an IP has been auto-banned for continuing to send requests after receiving `429` codes.
* HTTP `5XX` return codes are used for internal errors; the issue is on
  Binance's side.
* With using `/wapi/v3` , HTTP `504` return code is used when the API successfully sent the message but not get a response within the timeout period.
  It is important to **NOT** treat this as a failure operation; the execution status is
  **UNKNOWN** and could have been a success.

### Error Codes

* If there is an error, the API will return an error with a message of the reason.

> The error payload on API and SAPI is as follows:
 
```javascript
{
  "code": -1121,
  "msg": "Invalid symbol."
}
```

* When using `/wapi/v3`, any endpoint can return an ERROR; 

> The error payload on WAPI is as follows:
 
```javascript
{
  "success": false,
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

---
## LIMITS

### General Info on Limits
* The following `intervalLetter` values for headers:
    * SECOND => S
    * MINUTE => M
    * HOUR => H
    * DAY => D
* `intervalNum` describes the amount of the interval. For example, `intervalNum` 5 with `intervalLetter` M means "Every 5 minutes".
* The `/api/v3/exchangeInfo` `rateLimits` array contains objects related to the exchange's `RAW_REQUEST`, `REQUEST_WEIGHT`, and `ORDER` rate limits. These are further defined in the `ENUM definitions` section under `Rate limiters (rateLimitType)`.
* A 429 will be returned when either rate limit is violated.

### IP Limits
* Every request will contain `X-MBX-USED-WEIGHT-(intervalNum)(intervalLetter)` in the response headers which has the current used weight for the IP for all request rate limiters defined.
* Each route has a `weight` which determines for the number of requests each endpoint counts for. Heavier endpoints and endpoints that do operations on multiple symbols will have a heavier `weight`.
* When a 429 is received, it's your obligation as an API to back off and not spam the API.
* **Repeatedly violating rate limits and/or failing to back off after receiving 429s will result in an automated IP ban (HTTP status 418).**
* IP bans are tracked and **scale in duration** for repeat offenders, **from 2 minutes to 3 days**.
* A `Retry-After` header is sent with a 418 or 429 responses and will give the **number of seconds** required to wait, in the case of a 429, to prevent a ban, or, in the case of a 418, until the ban is over.
* **The limits on the API are based on the IPs, not the API keys.**

<aside class="notice">
We recommend using the websocket for getting data as much as possible, as this will not count to the request rate limit.
</aside>

### Order Rate Limits
* Every successful order response will contain a `X-MBX-ORDER-COUNT-(intervalNum)(intervalLetter)` header which has the current order count for the account for all order rate limiters defined.
* Rejected/unsuccessful orders are not guaranteed to have `X-MBX-ORDER-COUNT-**` headers in the response.
* **The order rate limit is counted against each account**.

## Endpoint security type
* Each endpoint has a security type that determines the how you will
  interact with it. This is stated next to the NAME of the endpoint.
    * If no security type is stated, assume the security type is NONE.
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


* `TRADE`, `MARGIN` and `USER_DATA` endpoints are `SIGNED` endpoints.


---
## SIGNED (TRADE, USER_DATA, AND MARGIN) Endpoint security
* `SIGNED` endpoints require an additional parameter, `signature`, to be
  sent in the  `query string` or `request body`.
* Endpoints use `HMAC SHA256` signatures. The `HMAC SHA256 signature` is a keyed `HMAC SHA256` operation.
  Use your `secretKey` as the key and `totalParams` as the value for the HMAC operation.
* The `signature` is **not case sensitive**.
* `totalParams` is defined as the `query string` concatenated with the
  `request body`.

### Timing security
* A `SIGNED` endpoint also requires a parameter, `timestamp`, to be sent which
  should be the millisecond timestamp of when the request was created and sent.
* An additional parameter, `recvWindow`, may be sent to specify the number of
  milliseconds after `timestamp` the request is valid for. If `recvWindow`
  is not sent, **it defaults to 5000**.

> The logic is as follows:

```javascript
  if (timestamp < (serverTime + 1000) && (serverTime - timestamp) <= recvWindow)
  {
    // process request
  } 
  else 
  {
    // reject request
  }
```

**Serious trading is about timing.** Networks can be unstable and unreliable,
which can lead to requests taking varying amounts of time to reach the
servers. With `recvWindow`, you can specify that the request must be
processed within a certain number of milliseconds or be rejected by the
server.

<aside class="notice">
It is recommended to use a small recvWindow of 5000 or less! The max cannot go beyond 60,000!
</aside>

### SIGNED Endpoint Examples for POST /api/v3/order
Here is a step-by-step example of how to send a vaild signed payload from the
Linux command line using `echo`, `openssl`, and `curl`.

Key | Value
------------ | ------------
apiKey | vmPUZE6mv9SD5VNHk4HlWFsOr6aKE2zvsw0MuIgwCIPy6utIco14y7Ju91duEh8A
secretKey | NhqPtmdSJYdKjVHjA7PZj4Mge3R5YNiP1e3UZjInClVN65XAbvqqM6A7H5fATj0j


Parameter | Value
------------ | ------------
symbol | LTCBTC
side | BUY
type | LIMIT
timeInForce | GTC
quantity | 1
price | 0.1
recvWindow | 5000
timestamp | 1499827319559


#### Example 1: As a request body


> **Example 1**

> **HMAC SHA256 signature:**

```shell
    $ echo -n "symbol=LTCBTC&side=BUY&type=LIMIT&timeInForce=GTC&quantity=1&price=0.1&recvWindow=5000&timestamp=1499827319559" | openssl dgst -sha256 -hmac "NhqPtmdSJYdKjVHjA7PZj4Mge3R5YNiP1e3UZjInClVN65XAbvqqM6A7H5fATj0j"
    (stdin)= c8db56825ae71d6d79447849e617115f4a920fa2acdcab2b053c4b2838bd6b71
```


> **curl command:**

```shell
    (HMAC SHA256)
    $ curl -H "X-MBX-APIKEY: vmPUZE6mv9SD5VNHk4HlWFsOr6aKE2zvsw0MuIgwCIPy6utIco14y7Ju91duEh8A" -X POST 'https://api.binance.com/api/v3/order' -d 'symbol=LTCBTC&side=BUY&type=LIMIT&timeInForce=GTC&quantity=1&price=0.1&recvWindow=5000&timestamp=1499827319559&signature=c8db56825ae71d6d79447849e617115f4a920fa2acdcab2b053c4b2838bd6b71'
    
```

* **requestBody:** 

symbol=LTCBTC   
&side=BUY   
&type=LIMIT   
&timeInForce=GTC   
&quantity=1   
&price=0.1   
&recvWindow=5000   
&timestamp=1499827319559


#### Example 2: As a query string

> **Example 2**

> **HMAC SHA256 signature:**

```shell
    $ echo -n "symbol=LTCBTC&side=BUY&type=LIMIT&timeInForce=GTC&quantity=1&price=0.1&recvWindow=5000&timestamp=1499827319559" | openssl dgst -sha256 -hmac "NhqPtmdSJYdKjVHjA7PZj4Mge3R5YNiP1e3UZjInClVN65XAbvqqM6A7H5fATj0j"
    (stdin)= c8db56825ae71d6d79447849e617115f4a920fa2acdcab2b053c4b2838bd6b71
    
```

> **curl command:**

```shell
    (HMAC SHA256)
   $ curl -H "X-MBX-APIKEY: vmPUZE6mv9SD5VNHk4HlWFsOr6aKE2zvsw0MuIgwCIPy6utIco14y7Ju91duEh8A" -X POST 'https://api.binance.com/api/v3/order?symbol=LTCBTC&side=BUY&type=LIMIT&timeInForce=GTC&quantity=1&price=0.1&recvWindow=5000&timestamp=1499827319559&signature=c8db56825ae71d6d79447849e617115f4a920fa2acdcab2b053c4b2838bd6b71'
    
```

*  **queryString:** 

symbol=LTCBTC   
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
   $ echo -n "symbol=LTCBTC&side=BUY&type=LIMIT&timeInForce=GTCquantity=1&price=0.1&recvWindow=5000&timestamp=1499827319559" | openssl dgst -sha256 -hmac "NhqPtmdSJYdKjVHjA7PZj4Mge3R5YNiP1e3UZjInClVN65XAbvqqM6A7H5fATj0j"
    (stdin)= 0fd168b8ddb4876a0358a8d14d0c9f3da0e9b20c5d52b2a00fcf7d1c602f9a77
    
```

> **curl command:**

```shell
    (HMAC SHA256)
    $ curl -H "X-MBX-APIKEY: vmPUZE6mv9SD5VNHk4HlWFsOr6aKE2zvsw0MuIgwCIPy6utIco14y7Ju91duEh8A" -X POST 'https://api.binance.com/api/v3/order?symbol=LTCBTC&side=BUY&type=LIMIT&timeInForce=GTC' -d 'quantity=1&price=0.1&recvWindow=5000&timestamp=1499827319559&signature=0fd168b8ddb4876a0358a8d14d0c9f3da0e9b20c5d52b2a00fcf7d1c602f9a77'
```

* **queryString:** 

symbol=LTCBTC&side=BUY&type=LIMIT&timeInForce=GTC

* **requestBody:** 

quantity=1&price=0.1&recvWindow=5000&timestamp=1499827319559


Note that the signature is different in example 3.
There is no & between "GTC" and "quantity=1".


### SIGNED Endpoint Examples for POST /wapi/v3/withdraw.html
Here is a step-by-step example of how to send a vaild signed payload from the
Linux command line using `echo`, `openssl`, and `curl`.

Key | Value
------------ | ------------
apiKey | vmPUZE6mv9SD5VNHk4HlWFsOr6aKE2zvsw0MuIgwCIPy6utIco14y7Ju91duEh8A
secretKey | NhqPtmdSJYdKjVHjA7PZj4Mge3R5YNiP1e3UZjInClVN65XAbvqqM6A7H5fATj0j


Parameter | Value
------------ | ------------
asset | ETH
address  |0x6915f16f8791d0a1cc2bf47c13a6b2a92000504b
addressTag | 1 (Secondary address identifier for coins like XRP,XMR etc.)
amount | 1
recvWindow | 5000
name | addressName (Description of the address)
timestamp | 1508396497000
signature  | 157fb937ec848b5f802daa4d9f62bea08becbf4f311203bda2bd34cd9853e320


#### Example 1: As a query string

> **HMAC SHA256 signature:**

```shell
    $ echo -n "asset=ETH&address=0x6915f16f8791d0a1cc2bf47c13a6b2a92000504b&amount=1&recvWindow=5000&timestamp=1510903211000" | openssl dgst -sha256 -hmac "NhqPtmdSJYdKjVHjA7PZj4Mge3R5YNiP1e3UZjInClVN65XAbvqqM6A7H5fATj0j"
    (stdin)= 157fb937ec848b5f802daa4d9f62bea08becbf4f311203bda2bd34cd9853e320
    
```


> **curl command:**

```shell
    (HMAC SHA256)
    $ curl -H "X-MBX-APIKEY: vmPUZE6mv9SD5VNHk4HlWFsOr6aKE2zvsw0MuIgwCIPy6utIco14y7Ju91duEh8A" -X POST 'https://www.binance.com/wapi/v3/withdraw.html?asset=ETH&address=0x6915f16f8791d0a1cc2bf47c13a6b2a92000504b&amount=1&recvWindow=5000&name=addressName&timestamp=1510903211000&signature=157fb937ec848b5f802daa4d9f62bea08becbf4f311203bda2bd34cd9853e320'
    
```

* **queryString:** 

asset=ETH   
&address=0x6915f16f8791d0a1cc2bf47c13a6b2a92000504b
&amount=1&recvWindow=5000&name=test&timestamp=1510903211000

Note that for `wapi`, parameters must be sent in query strings.


---

## Public API Definitions
### Terminology
* `base asset` refers to the asset that is the `quantity` of a symbol.
* `quote asset` refers to the asset that is the `price` of a symbol.

### ENUM definitions
**Symbol status (status):**

* PRE_TRADING
* TRADING
* POST_TRADING
* END_OF_DAY
* HALT
* AUCTION_MATCH
* BREAK

**Symbol type:**

* SPOT

**Order status (status):**

* NEW - The order has been accepted by the engine.
* PARTIALLY_FILLED - A part of the order has been filled.
* FILLED - The order has been completely filled.
* CANCELED - The order has been canceled by the user.
* PENDING_CANCEL (currently unused)
* REJECTED - The order was not accepted by the engine and not processed.
* EXPIRED - The order was canceled according to the order type's rules (e.g. LIMIT FOK orders with no fill, LIMIT IOC or MARKET orders that partially fill) or by the exchange, (e.g. orders canceled during liquidation, orders canceled during maintenance)

**OCO Status (listStatusType):**

* RESPONSE
* EXEC_STARTED
* ALL_DONE

**OCO Order Status (listOrderStatus):**

* EXECUTING
* ALL_DONE
* REJECT

**ContingencyType**

* OCO


**Order types (orderTypes, type):**

* LIMIT
* MARKET
* STOP_LOSS
* STOP_LOSS_LIMIT
* TAKE_PROFIT
* TAKE_PROFIT_LIMIT
* LIMIT_MAKER

**Order side (side):**

* BUY
* SELL

**Time in force (timeInForce):**

* GTC
* IOC
* FOK

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

```json
    {
      "rateLimitType": "REQUEST_WEIGHT",
      "interval": "MINUTE",
      "intervalNum": 1,
      "limit": 1200
    }
```

> ORDERS

```json
    {
      "rateLimitType": "ORDERS",
      "interval": "SECOND",
      "intervalNum": 10,
      "limit": 100
    },
    {
      "rateLimitType": "ORDERS",
      "interval": "DAY",
      "intervalNum": 1,
      "limit": 200000
    }
```

> RAW_REQUESTS

```json
    {
      "rateLimitType": "RAW_REQUESTS",
      "interval": "MINUTE",
      "intervalNum": 5,
      "limit": 5000
    }
```

* REQUEST_WEIGHT

* ORDERS

* RAW_REQUESTS

**Rate limit intervals (interval)**

* SECOND
* MINUTE
* DAY

---
## Filters
Filters define trading rules on a symbol or an exchange.
Filters come in two forms: `symbol filters` and `exchange filters`.

### Symbol Filters
#### PRICE_FILTER

> **ExchangeInfo format:**

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


#### PERCENT_PRICE

> **ExchangeInfo format:**

```javascript
  {
    "filterType": "PERCENT_PRICE",
    "multiplierUp": "1.3000",
    "multiplierDown": "0.7000",
    "avgPriceMins": 5
  }
```

The `PERCENT_PRICE` filter defines valid range for a price based on the average of the previous trades.
`avgPriceMins` is the number of minutes the average price is calculated over. 0 means the last price is used.

In order to pass the `percent price`, the following must be true for `price`:

* `price` <= `weightedAveragePrice` * `multiplierUp`
* `price` >= `weightedAveragePrice` * `multiplierDown`



#### LOT_SIZE


> **ExchangeInfo format:**

```javascript
  {
    "filterType": "LOT_SIZE",
    "minQty": "0.00100000",
    "maxQty": "100000.00000000",
    "stepSize": "0.00100000"
  }
```

The `LOT_SIZE` filter defines the `quantity` (aka "lots" in auction terms) rules for a symbol. There are 3 parts:

* `minQty` defines the minimum `quantity`/`icebergQty` allowed.
* `maxQty` defines the maximum `quantity`/`icebergQty` allowed.
* `stepSize` defines the intervals that a `quantity`/`icebergQty` can be increased/decreased by.

In order to pass the `lot size`, the following must be true for `quantity`/`icebergQty`:

* `quantity` >= `minQty`
* `quantity` <= `maxQty`
* (`quantity`-`minQty`) % `stepSize` == 0


#### MIN_NOTIONAL

> **ExchangeInfo format:**

```javascript
  {
    "filterType": "MIN_NOTIONAL",
    "minNotional": "0.00100000",
    "applyToMarket": true,
    "avgPriceMins": 5
  }
```

The `MIN_NOTIONAL` filter defines the minimum notional value allowed for an order on a symbol.
An order's notional value is the `price` * `quantity`.
`applyToMarket` determines whether or not the `MIN_NOTIONAL` filter will also be applied to `MARKET` orders.
Since `MARKET` orders have no price, the average price is used over the last `avgPriceMins` minutes.
`avgPriceMins` is the number of minutes the average price is calculated over. 0 means the last price is used.




#### ICEBERG_PARTS

> **ExchangeInfo format:**

```javascript
  {
    "filterType": "ICEBERG_PARTS",
    "limit": 10
  }
```

The `ICEBERG_PARTS` filter defines the maximum parts an iceberg order can have. The number of `ICEBERG_PARTS` is defined as `CEIL(qty / icebergQty)`.


#### MARKET_LOT_SIZE

> **ExchangeInfo format:**

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

> **ExchangeInfo format:**

```javascript
  {
    "filterType": "MAX_NUM_ORDERS",
    "limit": 25
  }
```

The `MAX_NUM_ORDERS` filter defines the maximum number of orders an account is allowed to have open on a symbol.
Note that both "algo" orders and normal orders are counted for this filter.



#### MAX_NUM_ALGO_ORDERS

> **ExchangeInfo format:**

```javascript
  {
    "filterType": "MAX_NUM_ALGO_ORDERS",
    "maxNumAlgoOrders": 5
  }
```

The `MAX_NUM_ALGO_ORDERS` filter defines the maximum number of "algo" orders an account is allowed to have open on a symbol.
"Algo" orders are `STOP_LOSS`, `STOP_LOSS_LIMIT`, `TAKE_PROFIT`, and `TAKE_PROFIT_LIMIT` orders.



#### MAX_NUM_ICEBERG_ORDERS
The `MAX_NUM_ICEBERG_ORDERS` filter defines the maximum number of `ICEBERG` orders an account is allowed to have open on a symbol.
An `ICEBERG` order is any order where the `icebergQty` is > 0.

> **ExchangeInfo format:**

```javascript
  {
    "filterType": "MAX_NUM_ICEBERG_ORDERS",
    "maxNumIcebergOrders": 5
  }
```

#### MAX_POSITION FILTER

The `MAX_POSITION` filter defines the allowed maximum position an account can have on the base asset of a symbol. 
An account's position defined as the sum of the account's:

1. free balance of the base asset
1. locked balance of the base asset
1. sum of the qty of all open BUY orders

`BUY` orders will be rejected if the account's position is greater than the maximum position allowed.

> **ExchangeInfo format:**

```javascript
{
  "filterType":"MAX_POSITION",
  "maxPosition":"10.00000000"
}
```


### Exchange Filters
#### EXCHANGE_MAX_NUM_ORDERS

> **ExchangeInfo format:**

```javascript
  {
    "filterType": "EXCHANGE_MAX_NUM_ORDERS",
    "maxNumOrders": 1000
  }
```

The `MAX_NUM_ORDERS` filter defines the maximum number of orders an account is allowed to have open on the exchange.
Note that both "algo" orders and normal orders are counted for this filter.



#### EXCHANGE_MAX_NUM_ALGO_ORDERS

> **ExchangeInfo format:**

```javascript
  {
    "filterType": "EXCHANGE_MAX_ALGO_ORDERS",
    "maxNumAlgoOrders": 200
  }
```

The `MAX_ALGO_ORDERS` filter defines the maximum number of "algo" orders an account is allowed to have open on the exchange.
"Algo" orders are `STOP_LOSS`, `STOP_LOSS_LIMIT`, `TAKE_PROFIT`, and `TAKE_PROFIT_LIMIT` orders.

---

# Wallet Endpoints

## System Status (System)


> **Response:**

```javascript
{ 
    "status": 0,              // 0: normal，1：system maintenance
    "msg": "normal"           // normal or system maintenance
}
```

``
GET /wapi/v3/systemStatus.html
``

Fetch system status.



## All Coins' Information (USER_DATA)

Get information of coins (available for deposit and withdraw) for user.

> **Response:**

```javascript
[
	{
		"coin": "BTC",
 		"depositAllEnable": true,
 		"free": "0.08074558",
 		"freeze": "0.00000000",
 		"ipoable": "0.00000000",
 		"ipoing": "0.00000000",
 		"isLegalMoney": false,
 		"locked": "0.00000000",
 		"name": "Bitcoin",
 		"networkList": [
	 		{
	 			"addressRegex": "^(bnb1)[0-9a-z]{38}$",
	   			"coin": "BTC",
	   			"depositDesc": "Wallet Maintenance, Deposit Suspended", // shown only when "depositEnable" is false.
	   			"depositEnable": false,
	   			"isDefault": false,        
	   			"memoRegex": "^[0-9A-Za-z\\-_]{1,120}$",
	   			"minConfirm": 1,  // min number for balance confirmation
	   			"name": "BEP2",
	   			"network": "BNB",            
	   			"resetAddressStatus": false,
	   			"specialTips": "Both a MEMO and an Address are required to successfully deposit your BEP2-BTCB tokens to Binance.",
	   			"unLockConfirm": 0,  // confirmation number for balance unlcok 
	   			"withdrawDesc": "Wallet Maintenance, Withdrawal Suspended", // shown only when "withdrawEnable" is false.
	   			"withdrawEnable": false,
	   			"withdrawFee": "0.00000220",
	   			"withdrawMin": "0.00000440"
	   		},
	  		{
	  			"addressRegex": "^[13][a-km-zA-HJ-NP-Z1-9]{25,34}$|^(bc1)[0-9A-Za-z]{39,59}$",
	   			"coin": "BTC",
	   			"depositEnable": ture,
	   			"insertTime": 1563532929000,
	   			"isDefault": true,
	   			"memoRegex": "",
	   			"minConfirm": 1, 
	   			"name": "BTC",
	   			"network": "BTC",
	   			"resetAddressStatus": false,
	   			"specialTips": "",
	   			"unLockConfirm": 2,
	   			"updateTime": 1571014804000, 
	   			"withdrawEnable": true,
	   			"withdrawFee": "0.00050000",
	   			"withdrawIntegerMultiple": "0.00000001",
	   			"withdrawMin": "0.00100000"
	   		}
   		],
 		"storage": "0.00000000",
 		"trading": true,
 		"withdrawAllEnable": true,
 		"withdrawing": "0.00000000"
 	}
]
```

``
GET /sapi/v1/capital/config/getall (HMAC SHA256)
``



**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
recvWindow | LONG | NO	
timestamp | LONG | YES	



## Daily Account Snapshot (USER_DATA)

> **Response:**

```javascript
{
   "code":200, // 200 for success; others are error codes
   "msg":"", // error message
   "snapshotVos":[
      {
         "data":{
            "balances":[
               {
                  "asset":"BTC",
                  "free":"0.09905021",
                  "locked":"0.00000000"
               },
               {
                  "asset":"USDT",
                  "free":"1.89109409",
                  "locked":"0.00000000"
               }
            ],
            "totalAssetOfBtc":"0.09942700"
         },
         "type":"spot",
         "updateTime":1576281599000
      }
   ]
}

```

> OR

```javascript
{
   "code":200, // 200 for success; others are error codes
   "msg":"", // error message
   "snapshotVos":[
      {
         "data":{
            "marginLevel":"2748.02909813",
            "totalAssetOfBtc":"0.00274803",
            "totalLiabilityOfBtc":"0.00000100",
            "totalNetAssetOfBtc":"0.00274750",
            "userAssets":[
               {
                  "asset":"XRP",
                  "borrowed":"0.00000000",
                  "free":"1.00000000",
                  "interest":"0.00000000",
                  "locked":"0.00000000",
                  "netAsset":"1.00000000"
               }
            ]
         },
         "type":"margin",
         "updateTime":1576281599000
      }
   ]
}
```
> OR

```javascript
{
   "code":200, // 200 for success; others are error codes
   "msg":"", // error message
   "snapshotVos":[
      {
         "data":{
            "assets":[
               {
                  "asset":"USDT",
                  "marginBalance":"118.99782335",
                  "walletBalance":"120.23811389"
               }
            ],
            "position":[
               {
                  "entryPrice":"7130.41000000",
                  "markPrice":"7257.66239673",
                  "positionAmt":"0.01000000",
                  "symbol":"BTCUSDT",
                  "unRealizedProfit":"1.24029054"
               }
            ]
         },
         "type":"futures",
         "updateTime":1576281599000
      }
   ]
}
```

``
GET /sapi/v1/accountSnapshot (HMAC SHA256)
``

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
type | STRING | YES | "SPOT", "MARGIN", "FUTURES"
startTime	| LONG | NO	
endTime | LONG | NO	
limit | INT | NO | min 5, max 30, default 5
recvWindow | LONG | NO 
timestamp | LONG | YES 







## Disable Fast Withdraw Switch (USER_DATA)

> **Response:**

```
{}
```

``
POST /sapi/v1/account/disableFastWithdrawSwitch (HMAC SHA256)
``

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
recvWindow | LONG | NO 
timestamp | LONG | YES 

* **Caution:**

	This request will disable fastwithdraw switch under your account. <br>
	You need to enable "trade" option for the api key which requests this 	endpoint.
	





## Enable Fast Withdraw Switch (USER_DATA)

> **Response:**

```
{}
```

``
POST /sapi/v1/account/enableFastWithdrawSwitch (HMAC SHA256)
``

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
recvWindow | LONG | NO 
timestamp | LONG | YES 
 
* This request will enable fastwithdraw switch under your  account. <br>
	You need to enable "trade" option for the api key which requests this endpoint.
* When Fast Withdraw Switch is on, transferring funds to a Binance account will be done instantly. There is no on-chain transaction, no transaction ID and no withdrawal fee.



## Withdraw [SAPI]

> **Response:**

```javascript
{
    "id":"7213fea8e94b4a5593d507237e5a555b"
}
```

``
POST /sapi/v1/capital/withdraw/apply (HMAC SHA256)
``

Submit a withdraw request.

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
coin	|  STRING |	YES	
withdrawOrderId | STRING | NO |  client id for withdraw
network | STRING | NO
address	 | STRING | YES	
addressTag | STRING | NO | Secondary address identifier for coins like XRP,XMR etc.
amount | DECIMAL | YES	
transactionFeeFlag | BOOLEAN | NO | When making internal transfer, `true` for returning the fee to the destination account; `false` for returning the fee back to the departure account. Default `false`.
name | STRING | NO | Description of the address
recvWindow | LONG | NO	
timestamp | LONG | YES	

* If `network` not send, return with default network of the coin.
* You can get `network` and `isDefault` in `networkList` of a coin in the response of `Get /sapi/v1/capital/config/getall (HMAC SHA256)`.


## Withdraw

> **Response:**

```javascript
{
    "msg": "success",
    "success": true,
    "id":"7213fea8e94b4a5593d507237e5a555b"
}
```

``
POST /wapi/v3/withdraw.html (HMAC SHA256)
``

Submit a withdraw request.

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
asset	|  STRING |	YES	 |
withdrawOrderId | STRING | NO | client customize id for withdraw order
network | STRING | NO | 
address	 | STRING | YES	
addressTag | STRING | NO | Secondary address identifier for coins like XRP,XMR etc.
amount | DECIMAL | YES	
transactionFeeFlag | BOOLEAN | NO | When making internal transfer, `true` for returning the fee to the destination account; `false` for returning the fee back to the departure account. Default `false`.
name | STRING | NO | Description of the address
recvWindow | LONG | NO	
timestamp | LONG | YES	

* If `network` not send, return with default network of the coin.
* You can get `network` and `isDefault` in `networkList` of a coin in the response of `Get /sapi/v1/capital/config/getall (HMAC SHA256)`. 



## Deposit History（supporting network） (USER_DATA)

> **Response:**

```javascript
[
	{
		"address": "0xddc66e4313fd6c737b6cae67cad90bb4e0ac7092",
  		"addressTag": "",
  		"amount": "139.04370000",
  		"coin": "USDT",
  		"insertTime": 1566791463000,
  		"network": "ETH",
  		"status": 1,
  		"txId": "0x5759dfe9983a4c7619bce9bc736bb6c26f804091753bf66fa91e7cd5cfeebafd"
  	}
]
```


``
GET /sapi/v1/capital/deposit/hisrec (HMAC SHA256)
``

Fetch deposit history.

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
coin | STRING | NO	
status | INT | NO | 0(0:pending,6: credited but cannot withdraw, 1:success)
startTime | LONG | NO | Default: 90 days from current timestamp 
endTime | LONG | NO	|  Default: present timestamp
offest	| INT | NO | default:0
limit | INT | NO | 
recvWindow | LONG | NO	
timestamp | LONG | YES	

* Please notice the default `startTime` and `endTime` to make sure that time interval is within 0-90 days.
* If both ``startTime`` and ``endTime`` are sent, time between ``startTime`` and ``endTime`` must be less than 90 days.






## Deposit History (USER_DATA)

> **Response:**

```javascript
{
    "depositList": [
        {
            "insertTime": 1508198532000,
            "amount": 0.04670582,
            "asset": "ETH",
            "address": "0x6915f16f8791d0a1cc2bf47c13a6b2a92000504b",
            "txId": "0xdf33b22bdb2b28b1f75ccd201a4a4m6e7g83jy5fc5d5a9d1340961598cfcb0a1",
            "status": 1
        },
        {
            "insertTime": 1508298532000,
            "amount": 1000,
            "asset": "XMR",
            "address": "463tWEBn5XZJSxLU34r6g7h8jtxuNcDbjLSjkn3XAXHCbLrTTErJrBWYgHJQyrCwkNgYvyV3z8zctJLPCZy24jvb3NiTcTJ",
            "addressTag": "342341222",
            "txId": "b3c6219639c8ae3f9cf010cdc24fw7f7yt8j1e063f9b4bd1a05cb44c4b6e2509",
            "status": 1
        }
    ],
    "success": true
}
```

``
GET /wapi/v3/depositHistory.html (HMAC SHA256)
``

Fetch deposit history.

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
asset | STRING | NO	
status | INT | NO | 0(0:pending,6: credited but cannot withdraw, 1:success)
startTime | LONG | NO | Default: 90 days from current timestamp 
endTime | LONG | NO |  Default: present timestamp
recvWindow | LONG | NO	
timestamp | LONG | YES	

* Please notice the default `startTime` and `endTime` to make sure that time interval is within 0-90 days.
* If both ``startTime`` and ``endTime`` are sent, time between ``startTime`` and ``endTime`` must be less than 90 days.




## Withdraw History (supporting network) (USER_DATA)


> **Response:**

```javascript
[
	{
		"address": "0x94df8b352de7f46f64b01d3666bf6e936e44ce60",
  		"amount": "8.91000000",
  		"applyTime": "2019-10-12 11:12:02",
  		"coin": "USDT",
  		"id": "b6ae22b3aa844210a7041aee7589627c",
  		"withdrawOrderId": "WITHDRAWtest123", // will not be returned if there's no withdrawOrderId for this withdraw.
  		"network": "ETH", 
  		"transferType": 0,   // 1 for internal transfer, 0 for external transfer   
  		"status": 6,
  		"txId": "0xb5ef8c13b968a406cc62a93a8bd80f9e9a906ef1b3fcf20a2e48573c17659268"
  	},
 	{
 		"address": "1FZdVHtiBqMrWdjPyRPULCUceZPJ2WLCsB",
  		"amount": "0.00150000",
  		"applyTime": "2019-09-24 12:43:45",
  		"coin": "BTC",
  		"id": "156ec387f49b41df8724fa744fa82719",
  		"network": "BTC",
  		"status": 6,
  		"txId": "60fd9007ebfddc753455f95fafa808c4302c836e4d1eebc5a132c36c1d8ac354"
  	}
]
```

``
GET /sapi/v1/capital/withdraw/history (HMAC SHA256)
``

Fetch withdraw history.

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
coin | STRING | NO	
status | INT | NO | 0(0:Email Sent,1:Cancelled 2:Awaiting Approval 3:Rejected 4:Processing 5:Failure 6:Completed)
offset | INT | NO
limit | INT | NO
startTime | LONG | NO | Default: 90 days from current timestamp 
endTime | LONG | NO	| Default: present timestamp
recvWindow | LONG | NO	
timestamp | LONG | YES	

* ``network`` may not be in the response for old withdraw.
* Please notice the default `startTime` and `endTime` to make sure that time interval is within 0-90 days.
* If both ``startTime`` and ``endTime`` are sent, time between ``startTime`` and ``endTime`` must be less than 90 days.




## Withdraw History (USER_DATA)


> **Response:**

```javascript
{
    "withdrawList": [
        {
            "id":"7213fea8e94b4a5593d507237e5a555b",
            "withdrawOrderId": None,    
            "amount": 0.99,
            "transactionFee": 0.01,
            "address": "0x6915f16f8791d0a1cc2bf47c13a6b2a92000504b",
            "asset": "ETH",
            "txId": "0xdf33b22bdb2b28b1f75ccd201a4a4m6e7g83jy5fc5d5a9d1340961598cfcb0a1",
            "applyTime": 1508198532000,
            "status": 4
        },
        {
            "id":"7213fea8e94b4a5534ggsd237e5a555b",
            "withdrawOrderId": "withdrawtest", 
            "amount": 999.9999,
            "transactionFee": 0.0001,
            "address": "463tWEBn5XZJSxLU34r6g7h8jtxuNcDbjLSjkn3XAXHCbLrTTErJrBWYgHJQyrCwkNgYvyV3z8zctJLPCZy24jvb3NiTcTJ",
            "addressTag": "342341222",
            "txId": "b3c6219639c8ae3f9cf010cdc24fw7f7yt8j1e063f9b4bd1a05cb44c4b6e2509",
            "asset": "XMR",
            "applyTime": 1508198532000,
            "status": 4
        }
    ],
    "success": true
}
```

``
GET /wapi/v3/withdrawHistory.html (HMAC SHA256)
``

Fetch withdraw history.

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
asset | STRING | NO	
status | INT | NO | 0(0:Email Sent,1:Cancelled 2:Awaiting Approval 3:Rejected 4:Processing 5:Failure 6:Completed)
startTime | LONG | NO | Default: 90 days from current timestamp
endTime | LONG | NO | Default: current timestamp
recvWindow | LONG | NO	
timestamp | LONG | YES	

* Please notice the default `startTime` and `endTime` to make sure that time interval is within 0-90 days.
* If both ``startTime`` and ``endTime`` are sent, time between ``startTime`` and ``endTime`` must be less than 90 days.




## Deposit Address (supporting network) (USER_DATA)

> **Response:**

```javascript
{
	"address": "1HPn8Rx2y6nNSfagQBKy27GB99Vbzg89wv",
 	"coin": "BTC",
 	"tag": "",
 	"url": "https://btc.com/1HPn8Rx2y6nNSfagQBKy27GB99Vbzg89wv"
}
```

``
GET /sapi/v1/capital/deposit/address (HMAC SHA256)
``

Fetch deposit address with network.

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
coin | STRING | YES	
network| STRING |NO | 
recvWindow | LONG | NO	
timestamp | LONG | YES	

* If `network` is not send, return with default network of the coin.
* You can get `network` and `isDefault` in `networkList` in the response of `Get /sapi/v1/capital/config/getall (HMAC SHA256)`. 





## Deposit Address (USER_DATA)


> **Response:**

```javascript
{
    "address": "0x6915f16f8791d0a1cc2bf47c13a6b2a92000504b",
    "success": true,
    "addressTag": "1231212",
    "asset": "BNB"
}

```

``
GET  /wapi/v3/depositAddress.html (HMAC SHA256)
``

Fetch deposit address.

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
asset | STRING | YES	
status | Boolean |NO
recvWindow | LONG | NO	
timestamp | LONG | YES	



## Account Status (USER_DATA)


> **Response:**

```javascript
{
    "msg": "Order failed:Low Order fill rate! Will be reactivated after 5 minutes.",
    "success": true,
    "objs": [
        "5"
    ]
}
```

``
GET /wapi/v3/accountStatus.html
``

Fetch account status detail.

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
recvWindow | LONG | NO	
timestamp | LONG | YES	




## Account API Trading Status (USER_DATA)

> **Response:**

```javascript
{
    "success": true,     // Query result
    "status": {          // API trading status detail
        "isLocked": false,   // API trading function is locked or not
        "plannedRecoverTime": 0,  // If API trading function is locked, this is the planned recover time
        "triggerCondition": { 
            "gcr": 150,  // Number of GTC orders
            "ifer": 150, // Number of FOK/IOC orders
            "ufr": 300   // Number of orders
        },
        "indicators": {  // The indicators updated every 30 seconds
           "BTCUSDT": [  // The symbol
            {
            "i": "UFR",  // Unfilled Ratio (UFR)
            "c": 20,     // Count of all orders
            "v": 0.05,   // Current UFR value
            "t": 0.995   // Trigger UFR value
            },
            {
            "i": "IFER", // IOC/FOK Expiration Ratio (IFER)
            "c": 20,     // Count of FOK/IOC orders
            "v": 0.99,   // Current IFER value
            "t": 0.99    // Trigger IFER value
            },
            {
            "i": "GCR",  // GTC Cancellation Ratio (GCR)
            "c": 20,     // Count of GTC orders
            "v": 0.99,   // Current GCR value
            "t": 0.99    // Trigger GCR value
            }
            ],
            "ETHUSDT": [ 
            {
            "i": "UFR",
            "c": 20,
            "v": 0.05,
            "t": 0.995
            },
            {
            "i": "IFER",
            "c": 20,
            "v": 0.99,
            "t": 0.99
            },
            {
            "i": "GCR",
            "c": 20,
            "v": 0.99,
            "t": 0.99
            }
            ]
        },
        "updateTime": 1547630471725   // The query result return time
    }
}
```

``
GET /wapi/v3/apiTradingStatus.html
``


Fetch account api trading status detail.



**Weight:**
1


**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
recvWindow | LONG | NO  
timestamp | LONG | YES  


## DustLog (USER_DATA)


> **Response:**

```javascript
{
    "success": true, 
    "results": {
        "total": 2,   //Total counts of exchange
        "rows": [
            {
                "transfered_total": "0.00132256",//Total transfered BNB amount for this exchange.
                "service_charge_total": "0.00002699",   //Total service charge amount for this exchange.
                "tran_id": 4359321,
                "logs": [           //Details of  this exchange.
                    {
                        "tranId": 4359321,
                        "serviceChargeAmount": "0.000009",
                        "uid": "10000015",
                        "amount": "0.0009",
                        "operateTime": "2018-05-03 17:07:04",
                        "transferedAmount": "0.000441",
                        "fromAsset": "USDT"
                    },
                    {
                        "tranId": 4359321,
                        "serviceChargeAmount": "0.00001799",
                        "uid": "10000015",
                        "amount": "0.0009",
                        "operateTime": "2018-05-03 17:07:04",
                        "transferedAmount": "0.00088156",
                        "fromAsset": "ETH"
                    }
                ],
                "operate_time": "2018-05-03 17:07:04" //The time of this exchange.
            },
            {
                "transfered_total": "0.00058795",
                "service_charge_total": "0.000012",
                "tran_id": 4357015,
                "logs": [       // Details of  this exchange.
                    {
                        "tranId": 4357015,
                        "serviceChargeAmount": "0.00001",
                        "uid": "10000015",
                        "amount": "0.001",
                        "operateTime": "2018-05-02 13:52:24",
                        "transferedAmount": "0.00049",
                        "fromAsset": "USDT"
                    },
                    {
                        "tranId": 4357015,
                        "serviceChargeAmount": "0.000002",
                        "uid": "10000015",
                        "amount": "0.0001",
                        "operateTime": "2018-05-02 13:51:11",
                        "transferedAmount": "0.00009795",
                        "fromAsset": "ETH"
                    }
                ],
                "operate_time": "2018-05-02 13:51:11"
            }
        ]
    }
}
```

``
GET /wapi/v3/userAssetDribbletLog.html   (HMAC SHA256)
``

Fetch small amounts of assets exchanged BNB records.


**Weight:**
1


**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
recvWindow | LONG | NO  
timestamp | LONG | YES  




## Dust Transfer (USER_DATA)


> **Response:**

```javascript
{
    "totalServiceCharge":"0.02102542",
    "totalTransfered":"1.05127099",
    "transferResult":[
        {
            "amount":"0.03000000",
            "fromAsset":"ETH",
            "operateTime":1563368549307,
            "serviceChargeAmount":"0.00500000",
            "tranId":2970932918,
            "transferedAmount":"0.25000000"
        },
        {
            "amount":"0.09000000",
            "fromAsset":"LTC",
            "operateTime":1563368549404,
            "serviceChargeAmount":"0.01548000",
            "tranId":2970932918,
            "transferedAmount":"0.77400000"
        },
        {
            "amount":"248.61878453",
            "fromAsset":"TRX",
            "operateTime":1563368549489,
            "serviceChargeAmount":"0.00054542",
            "tranId":2970932918,
            "transferedAmount":"0.02727099"
        }
    ]
}
```

``
Post /sapi/v1/asset/dust (HMAC SHA256)
``

Convert dust assets to BNB.

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
asset | ARRAY | YES | The asset being converted. For example: asset=BTC&asset=USDT
recvWindow | LONG | NO |
timestamp | LONG | YES |




## Asset Dividend Record (USER_DATA)


> **Response:**

```javascript
{
    "rows":[
        {
            "amount":"10.00000000",
            "asset":"BHFT",
            "divTime":1563189166000,
            "enInfo":"BHFT distribution",
            "tranId":2968885920
        },
        {
            "amount":"10.00000000",
            "asset":"BHFT",
            "divTime":1563189165000,
            "enInfo":"BHFT distribution",
            "tranId":2968885920
        }
    ],
    "total":2
}
```

``
Get /sapi/v1/asset/assetDividend (HMAC SHA256)
``

Query asset dividend record.

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
asset | STRING | NO |
startTime | LONG | NO |
endTime | LONG | NO |
limit | INT | NO | Default 20, max 500
recvWindow | LONG | NO |
timestamp | LONG | YES |



## Asset Detail (USER_DATA)

Fetch details of assets supported on Binance.



> **Response:**

```javascript
{
    "success": true,
    "assetDetail": {
        "CTR": {
            "minWithdrawAmount": "70.00000000", //min withdraw amount
            "depositStatus": false,//deposit status (false if ALL of networks' are false)
            "withdrawFee": 35, // withdraw fee
            "withdrawStatus": true, //withdraw status (false if ALL of networks' are false)
            "depositTip": "Delisted, Deposit Suspended" //reason
        },
        "SKY": {
            "minWithdrawAmount": "0.02000000",
            "depositStatus": true,
            "withdrawFee": 0.01,
            "withdrawStatus": true
        }	
    }
}
```

``
GET  /wapi/v3/assetDetail.html (HMAC SHA256)
``




**Weight:**
1


**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
recvWindow | LONG | NO  
timestamp | LONG | YES  

* Please get network and other deposit or withdraw details from ``GET /sapi/v1/capital/config/getall``.





## Trade Fee (USER_DATA)


> **Response:**

```javascript
{
	"tradeFee": [
    {
      "symbol": "ADABNB",
      "maker": 0.9000,
      "taker": 1.0000
    },
    {
      "symbol": "BNBBTC",
      "maker": 0.3000,
      "taker": 0.3000
    }
  ],
	"success": true
}
```

``
GET  /wapi/v3/tradeFee.html (HMAC SHA256)
``

Fetch trade fee, values in percentage.


**Weight:**
1


**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
recvWindow | LONG | NO  
timestamp | LONG | YES  
symbol | STRING | NO





# Sub-Account Endpoints

* The endpoints documented in this section are for [Corporate Accounts](https://www.binance.com/en/support/articles/360020371872).
* To become a corporate account, please refer to this document: [Corporate Account Application](https://www.binance.com/en/support/articles/360015552032)


## Query Sub-account List(For Master Account)


> **Response:**

```javascript
{
    "success":true,
    "subAccounts":[
        {
            "email":"123@test.com",
            "status":"enabled",
            "activated":true,
            "mobile":"91605290",
            "gAuth":true,
            "createTime":1544433328000
        },
        {
            "email":"321@test.com",
            "status":"disabled",
            "activated":true,
            "mobile":"22501238",
            "gAuth":true,
            "createTime":1544433328000
        }
    ]
}
```

``
GET   /wapi/v3/sub-account/list.html (HMAC SHA256)
``

Fetch sub account list.


**Weight:**
1


**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
email | STRING | NO | [Sub-account email](#email-address)
status | STRING | NO | Sub-account status: enabled or disabled
page | INT | NO | Default value: 1
limit | INT | NO | Default value: 500
recvWindow | LONG | NO  
timestamp | LONG | YES  



## Query Sub-account Transfer History(For Master Account)


> **Response:**

```javascript
{
    "success":true,
    "transfers":[
        {
            "from":"aaa@test.com",
            "to":"bbb@test.com",
            "asset":"BTC",
            "qty":"1",
            "time":1544433328000
        },
        {
            "from":"bbb@test.com",
            "to":"ccc@test.com",
            "asset":"ETH",
            "qty":"2",
            "time":1544433328000
        }
    ]
}
```

``
GET   /wapi/v3/sub-account/transfer/history.html (HMAC SHA256)
``

Fetch transfer history list


**Weight:**
1


**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
email | STRING | YES | [Sub-account email](#email-address)
startTime | LONG | NO | Default return the history with in 100 days
endTime | LONG | NO | Default return the history with in 100 days
page | INT | NO | Default value: 1
limit | INT | NO | Default value: 500
recvWindow | LONG | NO  
timestamp | LONG | YES  



## Sub-account Transfer(For Master Account)


> **Response:**

```javascript
{
    "success":true,
    "txnId":"2966662589"
}

```

``
POST   /wapi/v3/sub-account/transfer.html (HMAC SHA256)
``

Execute sub-account transfer


**Weight:**
1


**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
fromEmail | STRING | YES | Sender email
toEmail | STRING | YES | Recipient email
asset | STRING | YES
amount | DECIMAL | YES
recvWindow | LONG | NO  
timestamp | LONG | YES  




## Query Sub-account Assets(For Master Account)


> **Response:**

```javascript
{

    "success":true,
    "balances":[
        {
            "asset":"ADA",
            "free":10000,
            "locked":0
        },
        {
            "asset":"BNB",
            "free":10003,
            "locked":0
        },
        {
            "asset":"BTC",
            "free":11467.6399,
            "locked":0
        },
        {
            "asset":"ETH",
            "free":10004.995,
            "locked":0
        },
        {
            "asset":"USDT",
            "free":11652.14213,
            "locked":0
        }
    ]
}
```

``
GET   /wapi/v3/sub-account/assets.html (HMAC SHA256)
``

Fetch sub-account assets

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
email | STRING | YES  | Sub account email
recvWindow | LONG | NO  
timestamp | LONG | YES  






## Get Sub-account Deposit Address (For Master Account)


> **Response:**

```javascript
{
	"address":"TDunhSa7jkTNuKrusUTU1MUHtqXoBPKETV",
	"coin":"USDT",
	"tag":"",
	"url":"https://tronscan.org/#/address/TDunhSa7jkTNuKrusUTU1MUHtqXoBPKETV"
}
```

``
GET /sapi/v1/capital/deposit/subAddress (HMAC SHA256)
``

Fetch sub-account deposit address

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
email | STRING | YES  | Sub account email
coin | STRING | YES|
network | STRING | NO
recvWindow | LONG | NO  
timestamp | LONG | YES  



## Get Sub-account Deposit History (For Master Account)


> **Response:**

```javascript
[
	{
		"address":"0x1cc9380c0027b3a1dc603ee369f3fa23464e2d0c",
		"addressTag":"",
		"amount":"3.13000000",
		"coin":"POA",
		"insertTime":1555320696000,
		"status":1,
		"txId":"0x0dd033de5b1531ea463cbd9aae492bafca4daad497a733a48f8ea75cad15b93b"
	},
	{
		"address":"QSNnvmH8GfLt9eCeVqgQdjmDYp3qGA5o6j",
		"addressTag":"",
		"amount":"0.99000000",
		"coin":"QTUM",
		"insertTime":1555299491000,
		"status":1,
		"txId":"fd9bc013d37c82ebcd5fb0bba6727c12e8649c541e57d85a4a142a0b7fd19183"
	},
	{
		"address":"hx9995931e4ac4cafe0da5a0e0fe22ff2397ae8604",
		"addressTag":"",
		"amount":"1.98000000",
		"coin":"ICX",
		"insertTime":1555078384000,
		"status":1,
		"txId":"0xbe6de043d8f26b1f21ce9345fc7e8346013d22001095dedc7939633990783ffa"
	}
]
```

``
GET /sapi/v1/capital/deposit/subHisrec (HMAC SHA256)
``

Fetch sub-account deposit history

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
email | STRING | YES  | Sub account email
coin | STRING | NO	
status | INT | NO | 0(0:pending,6: credited but cannot withdraw, 1:success)
startTime | LONG | NO	
endTime | LONG | NO	
limit | INT | NO
offset	| INT | NO | default:0
recvWindow | LONG | NO	
timestamp | LONG | YES





## Get Sub-account's Status on Margin/Futures(For Master Account)

> **Response**

```javascript
[
    {
        "email":"123@test.com",			// user email
        "isSubUserEnabled": true,  	// true or false
        "isUserActive": true, 			// true or false
        "insertTime": 1570791523523 	// sub account create time
        "isMarginEnabled": true,      //true or false for margin
        "isFutureEnabled": true       //true or false for futures.
        "mobile": 1570791523523   		// user mobile number
    }
]
```

`GET /sapi/v1/sub-account/status (HMAC SHA256)`

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
email | STRING |	NO | [Sub-account email](#email-address)
recvWindow | LONG |	NO	
timestamp | LONG |	YES	

* If no email sent, all sub-accounts' information will be returned. 



## Enable Margin for Sub-account  (For Master Account)

> **Response**

```javascript
{

    "email":"123@test.com",

    "isMarginEnabled": true

}
```

``POST /sapi/v1/sub-account/margin/enable (HMAC SHA256)``


**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
email | STRING | YES |	[Sub-account email](#email-address)
recvWindow | LONG |	NO	
timestamp | LONG | YES	



## Get Detail on Sub-account's Margin Account (For Master Account)

> **Response**

```javascript
{
      "email":"123@test.com",
      "marginLevel": "11.64405625",
      "totalAssetOfBtc": "6.82728457",
      "totalLiabilityOfBtc": "0.58633215",
      "totalNetAssetOfBtc": "6.24095242",
      "marginTradeCoeffVo": {
			"forceLiquidationBar": "1.10000000",  // Liquidation margin ratio
			"marginCallBar": "1.50000000",        // Margin call margin ratio
			"normalBar": "2.00000000"		      // Initial margin ratio
		},
      "marginUserAssetVoList": [
          {
              "asset": "BTC",
              "borrowed": "0.00000000",
              "free": "0.00499500",
              "interest": "0.00000000",
              "locked": "0.00000000",
              "netAsset": "0.00499500"
          },
          {
              "asset": "BNB",
              "borrowed": "201.66666672",
              "free": "2346.50000000",
              "interest": "0.00000000",
              "locked": "0.00000000",
              "netAsset": "2144.83333328"
          },
          {
              "asset": "ETH",
              "borrowed": "0.00000000",
              "free": "0.00000000",
              "interest": "0.00000000",
              "locked": "0.00000000",
              "netAsset": "0.00000000"
          },
          {
              "asset": "USDT",
              "borrowed": "0.00000000",
              "free": "0.00000000",
              "interest": "0.00000000",
              "locked": "0.00000000",
              "netAsset": "0.00000000"
          }
      ]
}
```


``GET /sapi/v1/sub-account/margin/account (HMAC SHA256)``

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
email | STRING | YES |	[Sub-account email](#email-address)
recvWindow |LONG | NO	
timestamp	LONG	YES	


## Get Summary of Sub-account's Margin Account (For Master Account)


> **Response**

```javascript
{
    "totalAssetOfBtc": "4.33333333", 
    "totalLiabilityOfBtc": "2.11111112", 
    "totalNetAssetOfBtc": "2.22222221",
    "subAccountList":[
        {
            "email":"123@test.com",
            "totalAssetOfBtc": "2.11111111",
            "totalLiabilityOfBtc": "1.11111111",
            "totalNetAssetOfBtc": "1.00000000"
        },
        { 
            "email":"345@test.com",
            "totalAssetOfBtc": "2.22222222", 
            "totalLiabilityOfBtc": "1.00000001", 
            "totalNetAssetOfBtc": "1.22222221"
        }
    ]
}
```

``GET /sapi/v1/sub-account/margin/accountSummary (HMAC SHA256)``


**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
recvWindow | LONG |	NO	
timestamp | LONG | YES	



## Enable Futures for Sub-account (For Master Account)

> **Response**

```javascript
{

    "email":"123@test.com",

    "isFuturesEnabled": true  // true or false

}
```

``POST /sapi/v1/sub-account/futures/enable (HMAC SHA256)``


**Response:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
email | STRING | YES | 	[Sub-account email](#email-address)
recvWindow | LONG | NO	
timestamp | LONG | YES	



## Get Detail on Sub-account's Futures Account (For Master Account)

> **Response**

```javascript

{
	"email": "abc@test.com",
	"asset": "USDT",
	"assets":[
		{
		  	"asset": "USDT",
		   	"initialMargin": "0.00000000",
		   	"maintenanceMargin": "0.00000000",
		   	"marginBalance": "0.88308000",
		   	"maxWithdrawAmount": "0.88308000",
		   	"openOrderInitialMargin": "0.00000000",
		   	"positionInitialMargin": "0.00000000",
		   	"unrealizedProfit": "0.00000000",
		   	"walletBalance": "0.88308000"
		 }
	],
	"canDeposit": true,
	"canTrade": true,
	"canWithdraw": true,
	"feeTier": 2,
	"maxWithdrawAmount": "0.88308000",
	"totalInitialMargin": "0.00000000",
	"totalMaintenanceMargin": "0.00000000",
	"totalMarginBalance": "0.88308000",
	"totalOpenOrderInitialMargin": "0.00000000",
	"totalPositionInitialMargin": "0.00000000",
	"totalUnrealizedProfit": "0.00000000",
	"totalWalletBalance": "0.88308000",
	"updateTime": 1576756674610
 }

```


``GET /sapi/v1/sub-account/futures/account (HMAC SHA256)``

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
email | STRING | YES | [Sub-account email](#email-address)
recvWindow | LONG | NO	
timestamp | LONG | YES	




## Get Summary of Sub-account's Futures Account  (For Master Account)

> **Response**

```javascript
{
    "totalInitialMargin": "9.83137400", 
    "totalMaintenanceMargin": "0.41568700", 
    "totalMarginBalance": "23.03235621", 
    "totalOpenOrderInitialMargin": "9.00000000",
    "totalPositionInitialMargin": "0.83137400",
    "totalUnrealizedProfit": "0.03219710",
    "totalWalletBalance": "22.15879444",
    "asset": "USDT",
    "subAccountList":[
        {
            "email": "123@test.com",
            "totalInitialMargin": "9.00000000", 
            "totalMaintenanceMargin": "0.00000000", 
            "totalMarginBalance": "22.12659734", 
            "totalOpenOrderInitialMargin": "9.00000000",
            "totalPositionInitialMargin": "0.00000000",
            "totalUnrealizedProfit": "0.00000000",
            "totalWalletBalance": "22.12659734",
            "asset": "USDT"
        },
        { 
            "email": "345@test.com",
            "totalInitialMargin": "0.83137400", 
            "totalMaintMargin": "0.41568700", 
            "totalMarginBalance": "0.90575887", 
            "totalOpenOrderInitialMargin": "0.00000000",
            "totalPositionInitialMargin": "0.83137400",
            "totalUnrealizedProfit": "0.03219710",
            "totalWalletBalance": "0.87356177",
            "asset": "USDT"
        }
    ]
}

```




``GET /sapi/v1/sub-account/futures/accountSummary (HMAC SHA256)``

**Weight:**
20

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
recvWindow | LONG | NO	
timestamp | LONG | YES	





## Get Futures Postion-Risk of Sub-account (For Master Account)

> **Response**

```javascript
[
    {
        "entryPrice": "9975.12000",
        "leverage": "50",                   // current initial leverage
        "maxNotional": "1000000",      // notional value limit of current initial leverage
        "liquidationPrice": "7963.54",
        "markPrice": "9973.50770517",
        "positionAmount": "0.010",
        "symbol": "BTCUSDT",
        "unrealizedProfit": "-0.01612295"
    }
]
```

``GET /sapi/v1/sub-account/futures/positionRisk (HMAC SHA256)`` 

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
email | String | YES | [Sub-account email](#email-address)
recvWindow | LONG | NO	
timestamp | LONG | YES	




## Futures Transfer for Sub-account（For Master Account） 

> **Response**

```javascript
{
    "txnId":"2966662589"
}
```

``POST /sapi/v1/sub-account/futures/transfer (HMAC SHA256)`` 

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
email | String | YES | [Sub-account email](#email-address)
asset | STRING | YES | The asset being transferred, e.g., USDT
amount | DECIMAL | YES | The amount to be transferred
type| INT | YES | 1: transfer from spot main account to future account 2: transfer from future account to spot main account
timestamp | LONG | YES	


## Margin Transfer for Sub-account（For Master Account 

> **Response**

```javascript
{
    "txnId":"2966662589"
}
```

``POST /sapi/v1/sub-account/margin/transfer (HMAC SHA256)`` 

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
email | String | YES | [Sub-account email](#email-address)
asset | STRING | YES | The asset being transferred, e.g., BTC
amount | DECIMAL | YES | The amount to be transferred
type| INT | YES | 1: transfer from spot main account to margin account 2: transfer from margin account to spot main account
timestamp | LONG | YES	



## Transfer to Sub-account of Same Master（For Sub-account） 

> **Response**

```javascript
{
    "txnId":"2966662589"
}
```

``POST /sapi/v1/sub-account/transfer/subToSub (HMAC SHA256)`` 

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
toEmail | String | YES | [Sub-account email](#email-address)
asset | STRING | YES	
amount | DECIMAL | YES	
recvWindow | LONG | NO	
timestamp | LONG | YES	


## Transfer to Master（For Sub-account） 

> **Response**

```javascript
{
    "txnId":"2966662589"
}
```

``POST /sapi/v1/sub-account/transfer/subToMaster (HMAC SHA256)`` 

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
asset | STRING | YES	
amount | DECIMAL | YES	
recvWindow | LONG | NO	
timestamp | LONG | YES	


## Sub-account Transfer History (For Sub-account) 

> **Response**

```javascript
[

  {

    "counterParty”: "master",
    "email": "master@test.com",
    "type":  1,  // 1 for transfer in , 2 for transfer out
    "asset":"BTC",
    "qty":"1",
    "time":1544433325000
  },
  {
    "counterParty”: "subAccount",
    "email": "sub2@test.com",
    "type":  2,                                 
    "asset":"ETH",
    "qty":"2",
    "time":1544433326000
  }
]
```

``GET /sapi/v1/sub-account/transfer/subUserHistory (HMAC SHA256)`` 

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
asset | STRING | NO | If not sent, result of all assets will be returned
type | INT | NO | 1: transfer in, 2: transfer out
startTime | LONG | NO	
endTime | LONG | NO	
limit | INT | NO | Default 500
recvWindow | LONG | NO	
timestamp | LONG | YES	









# Market Data Endpoints

## Test Connectivity



> **Response:**

```javascript
{}
```

``
GET /api/v3/ping
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
GET /api/v3/time
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
  "timezone": "UTC",
  "serverTime": 1565246363776,
  "rateLimits": [
    {
      //These are defined in the `ENUM definitions` section under `Rate Limiters (rateLimitType)`.
      //All limits are optional
    }
  ],
  "exchangeFilters": [
    //These are the defined filters in the `Filters` section.
    //All filters are optional.
  ],
  "symbols": [
    {
      "symbol": "ETHBTC",
      "status": "TRADING",
      "baseAsset": "ETH",
      "baseAssetPrecision": 8,
      "quoteAsset": "BTC",
      "quotePrecision": 8,
      "quoteAssetPrecision": 8,
      "orderTypes": [
        "LIMIT",
        "LIMIT_MAKER",
        "MARKET",
        "STOP_LOSS",
        "STOP_LOSS_LIMIT",
        "TAKE_PROFIT",
        "TAKE_PROFIT_LIMIT"
      ],
      "icebergAllowed": true,
      "ocoAllowed": true,
      "isSpotTradingAllowed": true,
      "isMarginTradingAllowed": true,
      "filters": [
        //These are defined in the Filters section.
        //All filters are optional
      ],
      "permissions": [
         "SPOT",
         "MARGIN"
      ]
    }
  ]
}
```

``
GET /api/v3/exchangeInfo
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
GET /api/v3/depth
``

**Weight:**

Adjusted based on the limit:


Limit | Weight
------------ | ------------
5, 10, 20, 50, 100 | 1
500 | 5
1000 | 10
5000 | 50


**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
limit | INT | NO | Default 100; max 1000. Valid limits:[5, 10, 20, 50, 100, 500, 1000, 5000]


## Recent Trades List


> **Response:**

```javascript
[
  {
    "id": 28457,
    "price": "4.00000100",
    "qty": "12.00000000",
    "quoteQty": "48.000012",
    "time": 1499865549590,
    "isBuyerMaker": true,
    "isBestMatch": true
  }
]
```

``
GET /api/v3/trades
``

Get recent trades (up to last 500).

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
limit | INT | NO | Default 500; max 1000.



## Old Trade Lookup


> **Response:**

```javascript
[
  {
    "id": 28457,
    "price": "4.00000100",
    "qty": "12.00000000",
    "quoteQty": "48.000012",
    "time": 1499865549590, // Trade executed timestamp, as same as `T` in the stream
    "isBuyerMaker": true,
    "isBestMatch": true
  }
]
```

``
GET /api/v3/historicalTrades
``

Get older market trades.

**Weight:**
5

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
limit | INT | NO | Default 500; max 1000.
fromId | LONG | NO | Trade id to fetch from. Default gets most recent trades.

* `X-MBX-APIKEY` required


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
    "M": true           // Was the trade the best price match?
  }
]
```

``
GET /api/v3/aggTrades
``

Get compressed, aggregate trades. Trades that fill at the time, from the same
order, with the same price will have the quantity aggregated.

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
fromId | LONG | NO | id to get aggregate trades from INCLUSIVE.
startTime | LONG | NO | Timestamp in ms to get aggregate trades from INCLUSIVE.
endTime | LONG | NO | Timestamp in ms to get aggregate trades until INCLUSIVE.
limit | INT | NO | Default 500; max 1000.

* If startTime and endTime are sent, time between startTime and endTime must be less than 1 hour.
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
GET /api/v3/klines
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
limit | INT | NO | Default 500; max 1000.

* If startTime and endTime are not sent, the most recent klines are returned.




## Current Average Price




> **Response:**

```javascript
{
  "mins": 5,
  "price": "9.35751834"
}
```

``
GET /api/v3/avgPrice
``

Current average price for a symbol.

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |





## 24hr Ticker Price Change Statistics


> **Response:**

```javascript
{
  "symbol": "BNBBTC",
  "priceChange": "-94.99999800",
  "priceChangePercent": "-95.960",
  "weightedAvgPrice": "0.29628482",
  "prevClosePrice": "0.10002000",
  "lastPrice": "4.00000200",
  "lastQty": "200.00000000",
  "bidPrice": "4.00000000",
  "askPrice": "4.00000200",
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
    "symbol": "BNBBTC",
    "priceChange": "-94.99999800",
    "priceChangePercent": "-95.960",
    "weightedAvgPrice": "0.29628482",
    "prevClosePrice": "0.10002000",
    "lastPrice": "4.00000200",
    "lastQty": "200.00000000",
    "bidPrice": "4.00000000",
    "askPrice": "4.00000200",
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
GET /api/v3/ticker/24hr
``

24 hour rolling window price change statistics. **Careful** when accessing this with no symbol.

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
  "symbol": "LTCBTC",
  "price": "4.00000200"
}
```
> OR

```javascript
[
  {
    "symbol": "LTCBTC",
    "price": "4.00000200"
  },
  {
    "symbol": "ETHBTC",
    "price": "0.07946600"
  }
]
```


``
GET /api/v3/ticker/price
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
  "symbol": "LTCBTC",
  "bidPrice": "4.00000000",
  "bidQty": "431.00000000",
  "askPrice": "4.00000200",
  "askQty": "9.00000000"
}
```
> OR

```javascript
[
  {
    "symbol": "LTCBTC",
    "bidPrice": "4.00000000",
    "bidQty": "431.00000000",
    "askPrice": "4.00000200",
    "askQty": "9.00000000"
  },
  {
    "symbol": "ETHBTC",
    "bidPrice": "0.07946700",
    "bidQty": "9.00000000",
    "askPrice": "100000.00000000",
    "askQty": "1000.00000000"
  }
]
```

``
GET /api/v3/ticker/bookTicker
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



---
# Websocket Market Streams


* The base endpoint is: **wss://stream.binance.com:9443**
* Streams can be accessed either in a single raw stream or in a combined stream
* Raw streams are accessed at **/ws/\<streamName\>**
* Combined streams are accessed at **/stream?streams=\<streamName1\>/\<streamName2\>/\<streamName3\>**
* Combined stream events are wrapped as follows: **{"stream":"\<streamName\>","data":\<rawPayload\>}**
* All symbols for streams are **lowercase**
* A single connection to **stream.binance.com** is only valid for 24 hours; expect to be disconnected at the 24 hour mark
* The websocket server will send a `ping frame` every 3 minutes. If the websocket server does not receive a `pong frame` back from the connection within a 10 minute period, the connection will be disconnected. Unsolicited `pong frames` are allowed.

## Live Subscribing/Unsubscribing to streams

* The following data can be sent through the websocket instance in order to subscribe/unsubscribe from streams. Examples can be seen below.
* The `id` used in the JSON payloads is an unsigned INT used as an identifier to uniquely identify the messages going back and forth.
* In the response, if the `result` received is `null` this means the request sent was a success.

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
  "s": "BNBBTC",    // Symbol
  "a": 12345,       // Aggregate trade ID
  "p": "0.001",     // Price
  "q": "100",       // Quantity
  "f": 100,         // First trade ID
  "l": 105,         // Last trade ID
  "T": 123456785,   // Trade time
  "m": true,        // Is the buyer the market maker?
  "M": true         // Ignore
}
```

The Aggregate Trade Streams push trade information that is aggregated for a single taker order.

**Stream Name:** `<symbol>@aggTrade`

**Update Speed:** Real-time


## Trade Streams


> **Payload:**

```javascript
{
  "e": "trade",     // Event type
  "E": 123456789,   // Event time
  "s": "BNBBTC",    // Symbol
  "t": 12345,       // Trade ID
  "p": "0.001",     // Price
  "q": "100",       // Quantity
  "b": 88,          // Buyer order ID
  "a": 50,          // Seller order ID
  "T": 123456785,   // Trade time
  "m": true,        // Is the buyer the market maker?
  "M": true         // Ignore
}
```

The Trade Streams push raw trade information; each trade has a unique buyer and seller.

**Stream Name:** `<symbol>@trade`

**Update Speed:** Real-time








## Kline/Candlestick Streams


> **Payload:**

```javascript
{
  "e": "kline",     // Event type
  "E": 123456789,   // Event time
  "s": "BNBBTC",    // Symbol
  "k": {
    "t": 123400000, // Kline start time
    "T": 123460000, // Kline close time
    "s": "BNBBTC",  // Symbol
    "i": "1m",      // Interval
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

The Kline/Candlestick Stream push updates to the current klines/candlestick every second.

**Stream Name:** `<symbol>@kline_<interval>`

**Update Speed:** 2000ms

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




## Individual Symbol Mini Ticker Stream

> **Payload:**

```javascript
  {
    "e": "24hrMiniTicker",  // Event type
    "E": 123456789,         // Event time
    "s": "BNBBTC",          // Symbol
    "c": "0.0025",          // Close price
    "o": "0.0010",          // Open price
    "h": "0.0025",          // High price
    "l": "0.0010",          // Low price
    "v": "10000",           // Total traded base asset volume
    "q": "18"               // Total traded quote asset volume
  }
```

24hr rolling window mini-ticker statistics. These are NOT the statistics of the UTC day, but a 24hr rolling window for the previous 24hrs.

**Stream Name:** `<symbol>@miniTicker`

**Update Speed:** 1000ms







## All Market Mini Tickers Stream

> **Payload:**

```javascript
[
  {
    // Same as <symbol>@miniTicker payload
  }
]
```

24hr rolling window mini-ticker statistics for all symbols that changed in an array. These are NOT the statistics of the UTC day, but a 24hr rolling window for the previous 24hrs. Note that only tickers that have changed will be present in the array.

**Stream Name:** `!miniTicker@arr`

**Update Speed:** 1000ms






## Individual Symbol Ticker Streams


> **Payload:**

```javascript
{
  "e": "24hrTicker",  // Event type
  "E": 123456789,     // Event time
  "s": "BNBBTC",      // Symbol
  "p": "0.0015",      // Price change
  "P": "250.00",      // Price change percent
  "w": "0.0018",      // Weighted average price
  "x": "0.0009",      // First trade(F)-1 price (first trade before the 24hr rolling window)
  "c": "0.0025",      // Last price
  "Q": "10",          // Last quantity
  "b": "0.0024",      // Best bid price
  "B": "10",          // Best bid quantity
  "a": "0.0026",      // Best ask price
  "A": "100",         // Best ask quantity
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


24hr rollwing window ticker statistics for a single symbol. These are NOT the statistics of the UTC day, but a 24hr rolling window for the previous 24hrs.

**Stream Name:** `<symbol>@ticker`

**Update Speed:** 1000ms






## All Market Tickers Stream

> **Payload:**

```javascript
[
  {
    // Same as <symbol>@ticker payload
  }
]
```

24hr rolling window ticker statistics for all symbols that changed in an array. These are NOT the statistics of the UTC day, but a 24hr rolling window for the previous 24hrs. Note that only tickers that have changed will be present in the array.

**Stream Name:** `!ticker@arr`

**Update Speed:** 1000ms





## Individual Symbol Book Ticker Streams

> **Payload:**

```javascript
{
  "u":400900217,     // order book updateId
  "s":"BNBUSDT",     // symbol
  "b":"25.35190000", // best bid price
  "B":"31.21000000", // best bid qty
  "a":"25.36520000", // best ask price
  "A":"40.66000000"  // best ask qty
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








## Partial Book Depth Streams

> **Payload:**

```javascript
{
  "lastUpdateId": 160,  // Last update ID
  "bids": [             // Bids to be updated
    [
      "0.0024",         // Price level to be updated
      "10"              // Quantity
    ]
  ],
  "asks": [             // Asks to be updated
    [
      "0.0026",         // Price level to be updated
      "100"            // Quantity
    ]
  ]
}
```

Top **<levels\>** bids and asks, Valid **<levels\>** are 5, 10, or 20.

**Stream Names:** `<symbol>@depth<levels>` OR `<symbol>@depth<levels>@100ms`.  

**Update Speed:** 1000ms or 100ms







## Diff. Depth Stream


> **Payload:**

```javascript
{
  "e": "depthUpdate", // Event type
  "E": 123456789,     // Event time
  "s": "BNBBTC",      // Symbol
  "U": 157,           // First update ID in event
  "u": 160,           // Final update ID in event
  "b": [              // Bids to be updated
    [
      "0.0024",       // Price level to be updated
      "10"            // Quantity
    ]
  ],
  "a": [              // Asks to be updated
    [
      "0.0026",       // Price level to be updated
      "100"           // Quantity
    ]
  ]
}
```



**Stream Name:** `<symbol>@depth` OR `<symbol>@depth@100ms`

**Update Speed:** 1000ms or 100ms

Order book price and quantity depth updates used to locally manage an order book.




## How to manage a local order book correctly
1. Open a stream to **wss://stream.binance.com:9443/ws/bnbbtc@depth**.
2. Buffer the events you receive from the stream.
3. Get a depth snapshot from **https://www.binance.com/api/v3/depth?symbol=BNBBTC&limit=1000** .
4. Drop any event where `u` is <= `lastUpdateId` in the snapshot.
5. The first processed event should have `U` <= `lastUpdateId`+1 **AND** `u` >= `lastUpdateId`+1.
6. While listening to the stream, each new event's `U` should be equal to the previous event's `u`+1.
7. The data in each event is the **absolute** quantity for a price level.
8. If the quantity is 0, **remove** the price level.
9. Receiving an event that removes a price level that is not in your local order book can happen and is normal.











# Spot Account/Trade


## Test New Order (TRADE)


> **Response:**

```javascript
{}
```

``
POST /api/v3/order/test (HMAC SHA256)
``


Test new order creation and signature/recvWindow long.
Creates and validates a new order but does not send it into the matching engine.

**Weight:**
1

**Parameters:**

Same as `POST /api/v3/order`





## New Order  (TRADE)

> **Response ACK:**

```javascript
{
  "symbol": "BTCUSDT",
  "orderId": 28,
  "orderListId": -1, //Unless OCO, value will be -1
  "clientOrderId": "6gCrw2kRUAF9CvJDGP16IP",
  "transactTime": 1507725176595
}
```


> **Response RESULT:**

```javascript
{
  "symbol": "BTCUSDT",
  "orderId": 28,
  "orderListId": -1, //Unless OCO, value will be -1
  "clientOrderId": "6gCrw2kRUAF9CvJDGP16IP",
  "transactTime": 1507725176595,
  "price": "0.00000000",
  "origQty": "10.00000000",
  "executedQty": "10.00000000",
  "cummulativeQuoteQty": "10.00000000",
  "status": "FILLED",
  "timeInForce": "GTC",
  "type": "MARKET",
  "side": "SELL"
}
```

> **Response FULL:**

```javascript
{
  "symbol": "BTCUSDT",
  "orderId": 28,
  "orderListId": -1, //Unless OCO, value will be -1
  "clientOrderId": "6gCrw2kRUAF9CvJDGP16IP",
  "transactTime": 1507725176595,
  "price": "0.00000000",
  "origQty": "10.00000000",
  "executedQty": "10.00000000",
  "cummulativeQuoteQty": "10.00000000",
  "status": "FILLED",
  "timeInForce": "GTC",
  "type": "MARKET",
  "side": "SELL",
  "fills": [
    {
      "price": "4000.00000000",
      "qty": "1.00000000",
      "commission": "4.00000000",
      "commissionAsset": "USDT"
    },
    {
      "price": "3999.00000000",
      "qty": "5.00000000",
      "commission": "19.99500000",
      "commissionAsset": "USDT"
    },
    {
      "price": "3998.00000000",
      "qty": "2.00000000",
      "commission": "7.99600000",
      "commissionAsset": "USDT"
    },
    {
      "price": "3997.00000000",
      "qty": "1.00000000",
      "commission": "3.99700000",
      "commissionAsset": "USDT"
    },
    {
      "price": "3995.00000000",
      "qty": "1.00000000",
      "commission": "3.99500000",
      "commissionAsset": "USDT"
    }
  ]
}
```

``
POST /api/v3/order  (HMAC SHA256)
``

Send in a new order.

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
side | ENUM | YES |
type | ENUM | YES |
timeInForce | ENUM | NO |
quantity | DECIMAL | NO |
quoteOrderQty|DECIMAL|NO|
price | DECIMAL | NO |
newClientOrderId | STRING | NO | A unique id among open orders. Automatically generated if not sent.
stopPrice | DECIMAL | NO | Used with `STOP_LOSS`, `STOP_LOSS_LIMIT`, `TAKE_PROFIT`, and `TAKE_PROFIT_LIMIT` orders.
icebergQty | DECIMAL | NO | Used with `LIMIT`, `STOP_LOSS_LIMIT`, and `TAKE_PROFIT_LIMIT` to create an iceberg order.
newOrderRespType | ENUM | NO | Set the response JSON. `ACK`, `RESULT`, or `FULL`; `MARKET` and `LIMIT` order types default to `FULL`, all other orders default to `ACK`.
recvWindow | LONG | NO |The value cannot be greater than ```60000```
timestamp | LONG | YES |

Additional mandatory parameters based on `type`:

Type | Additional mandatory parameters
------------ | ------------
`LIMIT` | `timeInForce`, `quantity`, `price`
`MARKET` | `quantity` or `quoteOrderQty`
`STOP_LOSS` | `quantity`, `stopPrice`
`STOP_LOSS_LIMIT` | `timeInForce`, `quantity`,  `price`, `stopPrice`
`TAKE_PROFIT` | `quantity`, `stopPrice`
`TAKE_PROFIT_LIMIT` | `timeInForce`, `quantity`, `price`, `stopPrice`
`LIMIT_MAKER` | `quantity`, `price`

Other info:

* `LIMIT_MAKER` are `LIMIT` orders that will be rejected if they would immediately match and trade as a taker.
* `STOP_LOSS` and `TAKE_PROFIT` will execute a `MARKET` order when the `stopPrice` is reached.
* Any `LIMIT` or `LIMIT_MAKER` type order can be made an iceberg order by sending an `icebergQty`.
* Any order with an `icebergQty` MUST have `timeInForce` set to `GTC`.
* `MARKET` orders using `quantity` specifies how much a user wants to buy or sell based on the market price.
* `MARKET` orders using `quoteOrderQty` specifies the amount the user wants to spend (when buying) or receive (when selling) of the quote asset; the correct `quantity` will be determined based on the market liquidity and `quoteOrderQty`.
* `MARKET` orders using `quoteOrderQty` will not break `LOT_SIZE` filter rules; the order will execute a `quantity` that will have the notional value as close as possible to `quoteOrderQty`.
* same `newClientOrderId` can be accepted only when the previous one is filled, otherwise the order will be rejected.

Trigger order price rules against market price for both MARKET and LIMIT versions:

* Price above market price: `STOP_LOSS` `BUY`, `TAKE_PROFIT` `SELL`
* Price below market price: `STOP_LOSS` `SELL`, `TAKE_PROFIT` `BUY`




## Cancel Order (TRADE)


> **Response:**

```javascript
{
  "symbol": "LTCBTC",
  "origClientOrderId": "myOrder1",
  "orderId": 4,
  "orderListId": -1, //Unless part of an OCO, the value will always be -1.
  "clientOrderId": "cancelMyOrder1",
  "price": "2.00000000",
  "origQty": "1.00000000",
  "executedQty": "0.00000000",
  "cummulativeQuoteQty": "0.00000000",
  "status": "CANCELED",
  "timeInForce": "GTC",
  "type": "LIMIT",
  "side": "BUY"
}
```

``
DELETE /api/v3/order  (HMAC SHA256)
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
newClientOrderId | STRING | NO |  Used to uniquely identify this cancel. Automatically generated by default.
recvWindow | LONG | NO | The value cannot be greater than ```60000```
timestamp | LONG | YES |

Either `orderId` or `origClientOrderId` must be sent.


## Cancel all Open Orders on a Symbol (TRADE)

> **Response:**

```javascript
[
  {
    "symbol": "BTCUSDT",
    "origClientOrderId": "E6APeyTJvkMvLMYMqu1KQ4",
    "orderId": 11,
    "orderListId": -1,
    "clientOrderId": "pXLV6Hz6mprAcVYpVMTGgx",
    "price": "0.089853",
    "origQty": "0.178622",
    "executedQty": "0.000000",
    "cummulativeQuoteQty": "0.000000",
    "status": "CANCELED",
    "timeInForce": "GTC",
    "type": "LIMIT",
    "side": "BUY"
  },
  {
    "symbol": "BTCUSDT",
    "origClientOrderId": "A3EF2HCwxgZPFMrfwbgrhv",
    "orderId": 13,
    "orderListId": -1,
    "clientOrderId": "pXLV6Hz6mprAcVYpVMTGgx",
    "price": "0.090430",
    "origQty": "0.178622",
    "executedQty": "0.000000",
    "cummulativeQuoteQty": "0.000000",
    "status": "CANCELED",
    "timeInForce": "GTC",
    "type": "LIMIT",
    "side": "BUY"
  },
  {
    "orderListId": 1929,
    "contingencyType": "OCO",
    "listStatusType": "ALL_DONE",
    "listOrderStatus": "ALL_DONE",
    "listClientOrderId": "2inzWQdDvZLHbbAmAozX2N",
    "transactionTime": 1585230948299,
    "symbol": "BTCUSDT",
    "orders": [
      {
        "symbol": "BTCUSDT",
        "orderId": 20,
        "clientOrderId": "CwOOIPHSmYywx6jZX77TdL"
      },
      {
        "symbol": "BTCUSDT",
        "orderId": 21,
        "clientOrderId": "461cPg51vQjV3zIMOXNz39"
      }
    ],
    "orderReports": [
      {
        "symbol": "BTCUSDT",
        "origClientOrderId": "CwOOIPHSmYywx6jZX77TdL",
        "orderId": 20,
        "orderListId": 1929,
        "clientOrderId": "pXLV6Hz6mprAcVYpVMTGgx",
        "price": "0.668611",
        "origQty": "0.690354",
        "executedQty": "0.000000",
        "cummulativeQuoteQty": "0.000000",
        "status": "CANCELED",
        "timeInForce": "GTC",
        "type": "STOP_LOSS_LIMIT",
        "side": "BUY",
        "stopPrice": "0.378131",
        "icebergQty": "0.017083"
      },
      {
        "symbol": "BTCUSDT",
        "origClientOrderId": "461cPg51vQjV3zIMOXNz39",
        "orderId": 21,
        "orderListId": 1929,
        "clientOrderId": "pXLV6Hz6mprAcVYpVMTGgx",
        "price": "0.008791",
        "origQty": "0.690354",
        "executedQty": "0.000000",
        "cummulativeQuoteQty": "0.000000",
        "status": "CANCELED",
        "timeInForce": "GTC",
        "type": "LIMIT_MAKER",
        "side": "BUY",
        "icebergQty": "0.639962"
      }
    ]
  }
]
```

``
DELETE api/v3/openOrders
``

Cancels all active orders on a symbol.<br>
This includes OCO orders.

**Weight:**
1

**Parameters**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
recvWindow | LONG | NO | The value cannot be greater than ```60000```
timestamp | LONG | YES |



## Query Order (USER_DATA)


> **Response:**

```javascript
{
  "symbol": "LTCBTC",
  "orderId": 1,
  "orderListId": -1, //Unless OCO, value will be -1
  "clientOrderId": "myOrder1",
  "price": "0.1",
  "origQty": "1.0",
  "executedQty": "0.0",
  "cummulativeQuoteQty": "0.0",
  "status": "NEW",
  "timeInForce": "GTC",
  "type": "LIMIT",
  "side": "BUY",
  "stopPrice": "0.0",
  "icebergQty": "0.0",
  "time": 1499827319559,
  "updateTime": 1499827319559,
  "isWorking": true,
  "origQuoteOrderQty": "0.000000"
}
```


``
GET /api/v3/order (HMAC SHA256)
``

Check an order's status.

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
orderId | LONG | NO |
origClientOrderId | STRING | NO |
recvWindow | LONG | NO | The value cannot be greater than ```60000```
timestamp | LONG | YES |

Notes:

* Either `orderId` or `origClientOrderId` must be sent.
* For some historical orders `cummulativeQuoteQty` will be < 0, meaning the data is not available at this time.








## Current Open Orders (USER_DATA)


> **Response:**

```javascript
[
  {
    "symbol": "LTCBTC",
    "orderId": 1,
    "orderListId": -1, //Unless OCO, the value will always be -1
    "clientOrderId": "myOrder1",
    "price": "0.1",
    "origQty": "1.0",
    "executedQty": "0.0",
    "cummulativeQuoteQty": "0.0",
    "status": "NEW",
    "timeInForce": "GTC",
    "type": "LIMIT",
    "side": "BUY",
    "stopPrice": "0.0",
    "icebergQty": "0.0",
    "time": 1499827319559,
    "updateTime": 1499827319559,
    "isWorking": true,
    "origQuoteOrderQty": "0.000000"
  }
]
```

``
GET /api/v3/openOrders  (HMAC SHA256)
``

Get all open orders on a symbol. **Careful** when accessing this with no symbol.

**Weight:**
1 for a single symbol; **40** when the symbol parameter is omitted

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | NO |
recvWindow | LONG | NO | The value cannot be greater than ```60000```
timestamp | LONG | YES |

* If the symbol is not sent, orders for all symbols will be returned in an array.



## All Orders (USER_DATA)


> **Response:**

```javascript
[
  {
    "symbol": "LTCBTC",
    "orderId": 1,
    "orderListId": -1, //Unless OCO, the value will always be -1
    "clientOrderId": "myOrder1",
    "price": "0.1",
    "origQty": "1.0",
    "executedQty": "0.0",
    "cummulativeQuoteQty": "0.0",
    "status": "NEW",
    "timeInForce": "GTC",
    "type": "LIMIT",
    "side": "BUY",
    "stopPrice": "0.0",
    "icebergQty": "0.0",
    "time": 1499827319559,
    "updateTime": 1499827319559,
    "isWorking": true,
    "origQuoteOrderQty": "0.000000"
  }
]
```

``
GET /api/v3/allOrders (HMAC SHA256)
``

Get all account orders; active, canceled, or filled.

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
recvWindow | LONG | NO | The value cannot be greater than ```60000```
timestamp | LONG | YES |

**Notes:**

* If `orderId` is set, it will get orders >= that `orderId`. Otherwise most recent orders are returned.
* For some historical orders `cummulativeQuoteQty` will be < 0, meaning the data is not available at this time.




## New OCO (TRADE)



> **Response:**

```json
{
  "orderListId": 0,
  "contingencyType": "OCO",
  "listStatusType": "EXEC_STARTED",
  "listOrderStatus": "EXECUTING",
  "listClientOrderId": "JYVpp3F0f5CAG15DhtrqLp",
  "transactionTime": 1563417480525,
  "symbol": "LTCBTC",
  "orders": [
    {
      "symbol": "LTCBTC",
      "orderId": 2,
      "clientOrderId": "Kk7sqHb9J6mJWTMDVW7Vos"
    },
    {
      "symbol": "LTCBTC",
      "orderId": 3,
      "clientOrderId": "xTXKaGYd4bluPVp78IVRvl"
    }
  ],
  "orderReports": [
    {
      "symbol": "LTCBTC",
      "orderId": 2,
      "orderListId": 0,
      "clientOrderId": "Kk7sqHb9J6mJWTMDVW7Vos",
      "transactTime": 1563417480525,
      "price": "0.000000",
      "origQty": "0.624363",
      "executedQty": "0.000000",
      "cummulativeQuoteQty": "0.000000",
      "status": "NEW",
      "timeInForce": "GTC",
      "type": "STOP_LOSS",
      "side": "BUY",
      "stopPrice": "0.960664"
    },
    {
      "symbol": "LTCBTC",
      "orderId": 3,
      "orderListId": 0,
      "clientOrderId": "xTXKaGYd4bluPVp78IVRvl",
      "transactTime": 1563417480525,
      "price": "0.036435",
      "origQty": "0.624363",
      "executedQty": "0.000000",
      "cummulativeQuoteQty": "0.000000",
      "status": "NEW",
      "timeInForce": "GTC",
      "type": "LIMIT_MAKER",
      "side": "BUY"
    }
  ]
}
```


``
POST /api/v3/order/oco (HMAC SHA256)
``

**Weight**: 1

Send in a new OCO

**Parameters**:

Name |Type| Mandatory | Description
-----|-----|----------| -----------
symbol|STRING| YES|
listClientOrderId|STRING|NO| A unique Id for the entire orderList
side|ENUM|YES|
quantity|DECIMAL|YES|
limitClientOrderId|STRING|NO| A unique Id for the limit order
price|DECIMAL|YES|
limitIcebergQty|DECIMAL|NO|
stopClientOrderId |STRING|NO| A unique Id for the stop loss/stop loss limit leg
stopPrice |DECIMAL| YES
stopLimitPrice|DECIMAL|NO| If provided, `stopLimitTimeInForce` is required.
stopIcebergQty|DECIMAL|NO|
stopLimitTimeInForce|ENUM|NO| Valid values are ```GTC```/```FOK```/```IOC```
newOrderRespType|ENUM|NO| Set the response JSON.
recvWindow|LONG|NO| The value cannot be greater than ```60000```
timestamp|LONG|YES|


Other Info:

* Price Restrictions:
    * ```SELL```: Limit Price > Last Price > Stop Price
    * ```BUY```: Limit Price < Last Price < Stop Price
* Quantity Restrictions:
    * Both legs must have the same quantity
    * ```ICEBERG``` quantities however do not have to be the same




## Cancel OCO (TRADE)


> **Response:**

```javascript
{
  "orderListId": 0,
  "contingencyType": "OCO",
  "listStatusType": "ALL_DONE",
  "listOrderStatus": "ALL_DONE",
  "listClientOrderId": "C3wyj4WVEktd7u9aVBRXcN",
  "transactionTime": 1574040868128,
  "symbol": "LTCBTC",
  "orders": [
    {
      "symbol": "LTCBTC",
      "orderId": 2,
      "clientOrderId": "pO9ufTiFGg3nw2fOdgeOXa"
    },
    {
      "symbol": "LTCBTC",
      "orderId": 3,
      "clientOrderId": "TXOvglzXuaubXAaENpaRCB"
    }
  ],
  "orderReports": [
    {
      "symbol": "LTCBTC",
      "origClientOrderId": "pO9ufTiFGg3nw2fOdgeOXa",
      "orderId": 2,
      "orderListId": 0,
      "clientOrderId": "unfWT8ig8i0uj6lPuYLez6",
      "price": "1.00000000",
      "origQty": "10.00000000",
      "executedQty": "0.00000000",
      "cummulativeQuoteQty": "0.00000000",
      "status": "CANCELED",
      "timeInForce": "GTC",
      "type": "STOP_LOSS_LIMIT",
      "side": "SELL",
      "stopPrice": "1.00000000"
    },
    {
      "symbol": "LTCBTC",
      "origClientOrderId": "TXOvglzXuaubXAaENpaRCB",
      "orderId": 3,
      "orderListId": 0,
      "clientOrderId": "unfWT8ig8i0uj6lPuYLez6",
      "price": "3.00000000",
      "origQty": "10.00000000",
      "executedQty": "0.00000000",
      "cummulativeQuoteQty": "0.00000000",
      "status": "CANCELED",
      "timeInForce": "GTC",
      "type": "LIMIT_MAKER",
      "side": "SELL"
    }
  ]
}
```

``
DELETE /api/v3/orderList (HMAC SHA256)
``

**Weight**: 1

Cancel an entire Order List

**Parameters:**

Name| Type| Mandatory| Description
----| ----|------|------
symbol| STRING| YES|
orderListId|LONG|NO| Either ```orderListId``` or ```listClientOrderId``` must be provided
listClientOrderId|STRING|NO| Either ```orderListId``` or ```listClientOrderId``` must be provided
newClientOrderId|STRING|NO| Used to uniquely identify this cancel. Automatically generated by default
recvWindow|LONG|NO| The value cannot be greater than ```60000```
timestamp|LONG|YES|

Additional notes:

* Canceling an individual leg will cancel the entire OCO


## Query OCO (USER_DATA)


> **Response:**

```javascript
{
  "orderListId": 27,
  "contingencyType": "OCO",
  "listStatusType": "EXEC_STARTED",
  "listOrderStatus": "EXECUTING",
  "listClientOrderId": "h2USkA5YQpaXHPIrkd96xE",
  "transactionTime": 1565245656253,
  "symbol": "LTCBTC",
  "orders": [
    {
      "symbol": "LTCBTC",
      "orderId": 4,
      "clientOrderId": "qD1gy3kc3Gx0rihm9Y3xwS"
    },
    {
      "symbol": "LTCBTC",
      "orderId": 5,
      "clientOrderId": "ARzZ9I00CPM8i3NhmU9Ega"
    }
  ]
}
```


``
GET /api/v3/orderList (HMAC SHA256)
``

**Weight**: 1

Retrieves a specific OCO based on provided optional parameters

**Parameters**:

Name| Type|Mandatory| Description
----|-----|----|----------
orderListId|LONG|NO|  Either ```orderListId``` or ```listClientOrderId``` must be provided
origClientOrderId|STRING|NO| Either ```orderListId``` or ```listClientOrderId``` must be provided
recvWindow|LONG|NO| The value cannot be greater than ```60000```
timestamp|LONG|YES|




## Query all OCO (USER_DATA)



> **Response:**

```javascript
[
  {
    "orderListId": 29,
    "contingencyType": "OCO",
    "listStatusType": "EXEC_STARTED",
    "listOrderStatus": "EXECUTING",
    "listClientOrderId": "amEEAXryFzFwYF1FeRpUoZ",
    "transactionTime": 1565245913483,
    "symbol": "LTCBTC",
    "orders": [
      {
        "symbol": "LTCBTC",
        "orderId": 4,
        "clientOrderId": "oD7aesZqjEGlZrbtRpy5zB"
      },
      {
        "symbol": "LTCBTC",
        "orderId": 5,
        "clientOrderId": "Jr1h6xirOxgeJOUuYQS7V3"
      }
    ]
  },
  {
    "orderListId": 28,
    "contingencyType": "OCO",
    "listStatusType": "EXEC_STARTED",
    "listOrderStatus": "EXECUTING",
    "listClientOrderId": "hG7hFNxJV6cZy3Ze4AUT4d",
    "transactionTime": 1565245913407,
    "symbol": "LTCBTC",
    "orders": [
      {
        "symbol": "LTCBTC",
        "orderId": 2,
        "clientOrderId": "j6lFOfbmFMRjTYA7rRJ0LP"
      },
      {
        "symbol": "LTCBTC",
        "orderId": 3,
        "clientOrderId": "z0KCjOdditiLS5ekAFtK81"
      }
    ]
  }
]
```


``
GET /api/v3/allOrderList (HMAC SHA256)
``

**Weight**: 10

Retrieves all OCO based on provided optional parameters

**Parameters**

Name|Type| Mandatory| Description
----|----|----|---------
fromId|LONG|NO| If supplied, neither ```startTime``` or ```endTime``` can be provided
startTime|LONG|NO|
endTime|LONG|NO|
limit|INT|NO| Default Value: 500; Max Value: 1000
recvWindow|LONG|NO| The value cannot be greater than ```60000```
timestamp|LONG|YES|



## Query Open OCO (USER_DATA)



> **Response:**

```javascript
[
  {
    "orderListId": 31,
    "contingencyType": "OCO",
    "listStatusType": "EXEC_STARTED",
    "listOrderStatus": "EXECUTING",
    "listClientOrderId": "wuB13fmulKj3YjdqWEcsnp",
    "transactionTime": 1565246080644,
    "symbol": "1565246079109",
    "orders": [
      {
        "symbol": "LTCBTC",
        "orderId": 4,
        "clientOrderId": "r3EH2N76dHfLoSZWIUw1bT"
      },
      {
        "symbol": "LTCBTC",
        "orderId": 5,
        "clientOrderId": "Cv1SnyPD3qhqpbjpYEHbd2"
      }
    ]
  }
]
```


``
GET /api/v3/openOrderList (HMAC SHA256)
``

Weight: 2

**Parameters**

Name| Type|Mandatory| Description
----|-----|---|------------------
recvWindow|LONG|NO| The value cannot be greater than ```60000```
timestamp|LONG|YES|







## Account Information (USER_DATA)


> **Response:**

```javascript
{
  "makerCommission": 15,
  "takerCommission": 15,
  "buyerCommission": 0,
  "sellerCommission": 0,
  "canTrade": true,
  "canWithdraw": true,
  "canDeposit": true,
  "updateTime": 123456789,
  "accountType": "SPOT",
  "balances": [
    {
      "asset": "BTC",
      "free": "4723846.89208129",
      "locked": "0.00000000"
    },
    {
      "asset": "LTC",
      "free": "4763368.68006011",
      "locked": "0.00000000"
    }
  ]
  "permissions": [
    "SPOT"
  ]
}
```

``
GET /api/v3/account (HMAC SHA256)
``

Get current account information.

**Weight:**
5

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
recvWindow | LONG | NO | The value cannot be greater than ```60000```
timestamp | LONG | YES |



## Account Trade List (USER_DATA)


> **Response:**

```javascript
[
  {
    "symbol": "BNBBTC",
    "id": 28457,
    "orderId": 100234,
    "orderListId": -1, //Unless OCO, the value will always be -1
    "price": "4.00000100",
    "qty": "12.00000000",
    "quoteQty": "48.000012",
    "commission": "10.10000000",
    "commissionAsset": "BNB",
    "time": 1499865549590,
    "isBuyer": true,
    "isMaker": false,
    "isBestMatch": true
  }
]
```

``
GET /api/v3/myTrades  (HMAC SHA256)
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
fromId | LONG | NO | TradeId to fetch from. Default gets most recent trades.
limit | INT | NO | Default 500; max 1000.
recvWindow | LONG | NO | The value cannot be greater than ```60000```
timestamp | LONG | YES |

**Notes:**

* If `fromId` is set, it will get id >= that `fromId`.
Otherwise most recent orders are returned.





# Margin Account/Trade


## Margin Account Transfer (MARGIN)

> **Response:**

```javascript
{
    //transaction id
    "tranId": 100000001
}
```


``
POST /sapi/v1/margin/transfer (HMAC SHA256)
``

Execute transfer between spot account and margin account.

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
asset | STRING | YES | The asset being transferred, e.g., BTC
amount | DECIMAL | YES | The amount to be transferred
type | INT | YES | 1: transfer from main account to margin account 2: transfer from margin account to main account
recvWindow | LONG | NO  | The value cannot be greater than ```60000```
timestamp | LONG | YES




## Margin Account Borrow (MARGIN)

> **Response:**

```javascript
{
    //transaction id
    "tranId": 100000001
}
```


``
POST /sapi/v1/margin/loan (HMAC SHA256)
``

Apply for a loan.

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
asset | STRING | YES | 
amount | DECIMAL | YES | 
recvWindow | LONG | NO | The value cannot be greater than ```60000```
timestamp | LONG | YES



## Margin Account Repay (MARGIN)

> **Response:**

```javascript
{
    //transaction id
    "tranId": 100000001
}
```


``
POST /sapi/v1/margin/repay (HMAC SHA256)
``

Repay loan for margin account.

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
asset | STRING | YES | 
amount | DECIMAL | YES | 
recvWindow | LONG | NO | The value cannot be greater than ```60000```
timestamp | LONG | YES






## Query Margin Asset (MARKET_DATA)


> **Response:**

```javascript
{
	"assetFullName": "Binance Coin",
	"assetName": "BNB",
	"isBorrowable": false,
	"isMortgageable": true,
	"userMinBorrow": "0.00000000",
	"userMinRepay": "0.00000000"
}
```


``
GET /sapi/v1/margin/asset 
``

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
asset | STRING | YES |

* `X-MBX-APIKEY` required


## Query Margin Pair (MARKET_DATA)


> **Response:**

```javascript
{
   "id":323355778339572400,
   "symbol":"BTCUSDT",
   "base":"BTC",
   "quote":"USDT",
   "isMarginTrade":true,
   "isBuyAllowed":true,
   "isSellAllowed":true
}
```


``
GET /sapi/v1/margin/pair 
``

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |

* `X-MBX-APIKEY` required



## Get All Margin Assets (MARKET_DATA)


> **Response:**

```javascript
  [
      {
          "assetFullName": "USD coin",
          "assetName": "USDC",
          "isBorrowable": true,
          "isMortgageable": true,
          "userMinBorrow": "0.00000000",
          "userMinRepay": "0.00000000"
      },
      {
          "assetFullName": "BNB-coin",
          "assetName": "BNB",
          "isBorrowable": true,
          "isMortgageable": true,
          "userMinBorrow": "1.00000000",
          "userMinRepay": "0.00000000"
      },
      {
          "assetFullName": "Tether",
          "assetName": "USDT",
          "isBorrowable": true,
          "isMortgageable": true,
          "userMinBorrow": "1.00000000",
          "userMinRepay": "0.00000000"
      },
      {
          "assetFullName": "etherum",
          "assetName": "ETH",
          "isBorrowable": true,
          "isMortgageable": true,
          "userMinBorrow": "0.00000000",
          "userMinRepay": "0.00000000"
      },
      {
          "assetFullName": "Bitcoin",
          "assetName": "BTC",
          "isBorrowable": true,
          "isMortgageable": true,
          "userMinBorrow": "0.00000000",
          "userMinRepay": "0.00000000"
      }
  ]
```


``
GET /sapi/v1/margin/allAssets
``

**Weight:**
1

**Parameters:**

None



* `X-MBX-APIKEY` required


## Get All Margin Pairs (MARKET_DATA)

> **Response:**

```javascript
[
	{
		"base": "BNB",
  		"id": 351637150141315861,
  		"isBuyAllowed": true,
  		"isMarginTrade": true,
  		"isSellAllowed": true,
  		"quote": "BTC",
  		"symbol": "BNBBTC"
  	},
 	{
 		"base": "TRX",
  		"id": 351637923235429141,
  		"isBuyAllowed": true,
  		"isMarginTrade": true,
  		"isSellAllowed": true,
  		"quote": "BTC",
  		"symbol": "TRXBTC"
  	},
 	{
 		"base": "XRP",
  		"id": 351638112213990165,
  		"isBuyAllowed": true,
  		"isMarginTrade": true,
  		"isSellAllowed": true,
  		"quote": "BTC",
  		"symbol": "XRPBTC"
  	},
 	{
 		"base": "ETH",
  		"id": 351638524530850581,
  		"isBuyAllowed": true,
  		"isMarginTrade": true,
  		"isSellAllowed": true,
  		"quote": "BTC",
  		"symbol": "ETHBTC"
  	},
 	{
 		"base": "BNB",
  		"id": 376870400832855109,
  		"isBuyAllowed": true,
  		"isMarginTrade": true,
  		"isSellAllowed": true,
  		"quote": "USDT",
  		"symbol": "BNBUSDT"
  }
]
```



``
GET /sapi/v1/margin/allPairs 
``

**Weight:**
1

**Parameters:**

None


* `X-MBX-APIKEY` required




## Query Margin PriceIndex (MARKET_DATA)

> **Response:**

```javascript
{
   "calcTime": 1562046418000,
   "price": "0.00333930",
   "symbol": "BNBBTC"
}
```


``
GET /sapi/v1/margin/priceIndex 
``

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |

* `X-MBX-APIKEY` required






## Margin Account New Order (TRADE)

> **Response ACK:**

```javascript
{
  "symbol": "BTCUSDT",
  "orderId": 28,
  "clientOrderId": "6gCrw2kRUAF9CvJDGP16IP",
  "transactTime": 1507725176595
}
```
> **Response RESULT:**

```javascript
{
  "symbol": "BTCUSDT",
  "orderId": 28,
  "clientOrderId": "6gCrw2kRUAF9CvJDGP16IP",
  "transactTime": 1507725176595,
  "price": "1.00000000",
  "origQty": "10.00000000",
  "executedQty": "10.00000000",
  "cummulativeQuoteQty": "10.00000000",
  "status": "FILLED",
  "timeInForce": "GTC",
  "type": "MARKET",
  "side": "SELL"
}
```
> **Response FULL:**

```javascript
{
  "symbol": "BTCUSDT",
  "orderId": 28,
  "clientOrderId": "6gCrw2kRUAF9CvJDGP16IP",
  "transactTime": 1507725176595,
  "price": "1.00000000",
  "origQty": "10.00000000",
  "executedQty": "10.00000000",
  "cummulativeQuoteQty": "10.00000000",
  "status": "FILLED",
  "timeInForce": "GTC",
  "type": "MARKET",
  "side": "SELL",
  "marginBuyBorrowAmount": 5,       // will not returen if no margin trade happens
  "marginBuyBorrowAsset": "BTC",    // will not returen if no margin trade happens
  "fills": [
    {
      "price": "4000.00000000",
      "qty": "1.00000000",
      "commission": "4.00000000",
      "commissionAsset": "USDT"
    },
    {
      "price": "3999.00000000",
      "qty": "5.00000000",
      "commission": "19.99500000",
      "commissionAsset": "USDT"
    },
    {
      "price": "3998.00000000",
      "qty": "2.00000000",
      "commission": "7.99600000",
      "commissionAsset": "USDT"
    },
    {
      "price": "3997.00000000",
      "qty": "1.00000000",
      "commission": "3.99700000",
      "commissionAsset": "USDT"
    },
    {
      "price": "3995.00000000",
      "qty": "1.00000000",
      "commission": "3.99500000",
      "commissionAsset": "USDT"
    }
  ]
}
```


``
POST  /sapi/v1/margin/order (HMAC SHA256)
``
Post a new order for margin account.

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
side |	ENUM |YES |	BUY<br>SELL
type | ENUM | YES	
quantity | DECIMAL |	YES	
price |	DECIMAL | NO	
stopPrice | DECIMAL | NO | Used with `STOP_LOSS`, `STOP_LOSS_LIMIT`, `TAKE_PROFIT`, and `TAKE_PROFIT_LIMIT` orders.
newClientOrderId | STRING | NO | A unique id among open orders. Automatically generated if not sent.
icebergQty | DECIMAL | NO | Used with `LIMIT`, `STOP_LOSS_LIMIT`, and `TAKE_PROFIT_LIMIT` to create an iceberg order.
newOrderRespType | ENUM | NO | Set the response JSON. ACK, RESULT, or FULL; MARKET and LIMIT order types default to FULL, all other orders default to ACK.
sideEffectType | ENUM | NO | NO_SIDE_EFFECT, MARGIN_BUY, AUTO_REPAY; default NO_SIDE_EFFECT.
timeInForce | ENUM | NO | GTC,IOC,FOK
recvWindow | LONG | NO | The value cannot be greater than ```60000```
timestamp | LONG | YES



## Margin Account Cancel Order (TRADE)

> **Response:**

```javascript
{
  "symbol": "LTCBTC",
  "orderId": 28,
  "origClientOrderId": "myOrder1",
  "clientOrderId": "cancelMyOrder1",
  "price": "1.00000000",
  "origQty": "10.00000000",
  "executedQty": "8.00000000",
  "cummulativeQuoteQty": "8.00000000",
  "status": "CANCELED",
  "timeInForce": "GTC",
  "type": "LIMIT",
  "side": "SELL"
}
```


``
DELETE /sapi/v1/margin/order (HMAC SHA256)
``
Cancel an active order for margin account.

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
orderId | LONG | NO | 
origClientOrderId |	STRING | NO	
newClientOrderId |	STRING | NO | Used to uniquely identify this cancel. Automatically generated by default.
recvWindow | LONG | NO | The value cannot be greater than ```60000```
timestamp | LONG | YES

Either orderId or origClientOrderId must be sent.






## Get Transfer History (USER_DATA)

> **Response:**

```javascript
{
  "rows": [
    {
      "amount": "0.10000000",
      "asset": "BNB",
      "status": "CONFIRMED",
      "timestamp": 1566898617,
      "txId": 5240372201,
      "type": "ROLL_IN"
    },
    {
      "amount": "5.00000000",
      "asset": "USDT",
      "status": "CONFIRMED",
      "timestamp": 1566888436,
      "txId": 5239810406,
      "type": "ROLL_OUT"
    },
    {
      "amount": "1.00000000",
      "asset": "EOS",
      "status": "CONFIRMED",
      "timestamp": 1566888403,
      "txId": 5239808703,
      "type": "ROLL_IN"
    }
  ],
  "total": 3
}
```


``
GET /sapi/v1/margin/transfer (HMAC SHA256)
``

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
asset |	STRING | No
type | STRING | NO | Tranfer Type: ROLL_IN, ROLL_OUT
startTime |	LONG |	NO
endTime | LONG | NO	
current | LONG | NO | Currently querying page. Start from 1. Default:1
size |	LONG | NO |	Default:10 Max:100
recvWindow | LONG | NO | The value cannot be greater than ```60000```
timestamp | LONG | YES



* Response in descending order



## Query Loan Record (USER_DATA)

> **Response:**

```javascript
{
  "rows": [
    {
      "asset": "BNB",
      "principal": "0.84624403",
      "timestamp": 1555056425000,
      "status": "CONFIRMED"   //one of PENDING (pending to execution), CONFIRMED (successfully loaned), FAILED (execution failed, nothing happened to your account);
    }
  ],
  "total": 1
}
```


``
GET /sapi/v1/margin/loan (HMAC SHA256)
``


**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
asset |	STRING | YES	
txId | LONG | NO | the tranId in POST /sapi/v1/margin/loan
startTime |	LONG |	NO	
endTime | LONG | NO	
current | LONG | NO | Currently querying page. Start from 1. Default:1
size |	LONG | NO |	Default:10 Max:100
recvWindow | LONG | NO | The value cannot be greater than ```60000```
timestamp | LONG | YES

* txId or startTime must be sent. txId takes precedence.


* Response in descending order


## Query Repay Record (USER_DATA)


> **Response:**

```javascript
{
     "rows": [
         {
             "amount": "14.00000000",   //Total amount repaid
             "asset": "BNB",   
             "interest": "0.01866667",    //Interest repaid
             "principal": "13.98133333",   //Principal repaid
             "status": "CONFIRMED",   //one of PENDING (pending to execution), CONFIRMED (successfully loaned), FAILED (execution failed, nothing happened to your account)
             "timestamp": 1563438204000,
             "txId": 2970933056
         }
     ],
     "total": 1
}
```


``
GET /sapi/v1/margin/repay (HMAC SHA256)
``



**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
asset | STRING |	YES	
txId | LONG | NO | return of /sapi/v1/margin/repay 
startTime | LONG | NO	
endTime | LONG | NO	
current | LONG | NO	| Currently querying page. Start from 1. Default:1
size | LONG | NO | Default:10 Max:100
recvWindow | LONG | NO | The value cannot be greater than ```60000```
timestamp | LONG | YES

* txId or startTime must be sent. txId takes precedence.



* Response in descending order




## Get Interest History (USER_DATA)

> **Response:**

```javascript
  {
      "rows": [
          {
              "asset": "BNB",
              "interest": "0.02414667",
              "interestAccuredTime": 1566813600,
              "interestRate": "0.01600000",
              "principal": "36.22000000",
              "type": "ON_BORROW"
          },
          {
              "asset": "BNB",
              "interest": "0.02019334",
              "interestAccuredTime": 1566813600,
              "interestRate": "0.01600000",
              "principal": "30.29000000",
              "type": "ON_BORROW"
          },
          {
              "asset": "BNB",
              "interest": "0.02098667",
              "interestAccuredTime": 1566813600,
              "interestRate": "0.01600000",
              "principal": "31.48000000",
              "type": "ON_BORROW"
          },
          {
              "asset": "BNB",
              "interest": "0.02483334",
              "interestAccuredTime": 1566806400,
              "interestRate": "0.01600000",
              "principal": "37.25000000",
              "type": "ON_BORROW"
          },
          {
              "asset": "BNB",
              "interest": "0.02165334",
              "interestAccuredTime": 1566806400,
              "interestRate": "0.01600000",
              "principal": "32.48000000",
              "type": "ON_BORROW"
          }
      ],
      "total": 5
  }
```

``
GET /sapi/v1/margin/interestHistory (HMAC SHA256)
``


**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
asset |	STRING | No
startTime |	LONG |	NO	
endTime | LONG | NO	
current | LONG | NO | Currently querying page. Start from 1. Default:1
size |	LONG | NO |	Default:10 Max:100
recvWindow | LONG | NO | The value cannot be greater than ```60000```
timestamp | LONG | YES

* Response in descending order



## Get Force Liquidation Record (USER_DATA)


> **Response:**

```javascript
  {
      "rows": [
          {
              "avgPrice": "0.00388359",
              "executedQty": "31.39000000",
              "orderId": 180015097,
              "price": "0.00388110",
              "qty": "31.39000000",
              "side": "SELL",
              "symbol": "BNBBTC",
              "timeInForce": "GTC",
              "updatedTime": 1558941374745
          }
      ],
      "total": 1
  }
```

``
GET /sapi/v1/margin/forceLiquidationRec (HMAC SHA256)
``


**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
startTime |	LONG |	NO	
endTime | LONG | NO	
current | LONG | NO | Currently querying page. Start from 1. Default:1
size |	LONG | NO |	Default:10 Max:100
recvWindow | LONG | NO | The value cannot be greater than ```60000```
timestamp | LONG | YES

* Response in descending order






## Query Margin Account Details (USER_DATA)


> **Response:**

```javascript
{
      "borrowEnabled": true,
      "marginLevel": "11.64405625",
      "totalAssetOfBtc": "6.82728457",
      "totalLiabilityOfBtc": "0.58633215",
      "totalNetAssetOfBtc": "6.24095242",
      "tradeEnabled": true,
      "transferEnabled": true,
      "userAssets": [
          {
              "asset": "BTC",
              "borrowed": "0.00000000",
              "free": "0.00499500",
              "interest": "0.00000000",
              "locked": "0.00000000",
              "netAsset": "0.00499500"
          },
          {
              "asset": "BNB",
              "borrowed": "201.66666672",
              "free": "2346.50000000",
              "interest": "0.00000000",
              "locked": "0.00000000",
              "netAsset": "2144.83333328"
          },
          {
              "asset": "ETH",
              "borrowed": "0.00000000",
              "free": "0.00000000",
              "interest": "0.00000000",
              "locked": "0.00000000",
              "netAsset": "0.00000000"
          },
          {
              "asset": "USDT",
              "borrowed": "0.00000000",
              "free": "0.00000000",
              "interest": "0.00000000",
              "locked": "0.00000000",
              "netAsset": "0.00000000"
          }
      ]
}
```


``
GET /sapi/v1/margin/account (HMAC SHA256)
``


**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
recvWindow | LONG | NO | The value cannot be greater than ```60000```
timestamp | LONG | YES |




## Query Margin Account's Order (USER_DATA)


> **Response:**

```javascript
{
   "clientOrderId": "ZwfQzuDIGpceVhKW5DvCmO",
   "cummulativeQuoteQty": "0.00000000",
   "executedQty": "0.00000000",
   "icebergQty": "0.00000000",
   "isWorking": true,
   "orderId": 213205622,
   "origQty": "0.30000000",
   "price": "0.00493630",
   "side": "SELL",
   "status": "NEW",
   "stopPrice": "0.00000000",
   "symbol": "BNBBTC",
   "time": 1562133008725,
   "timeInForce": "GTC",
   "type": "LIMIT",
   "updateTime": 1562133008725
}
```

``
GET /sapi/v1/margin/order (HMAC SHA256)
``

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
orderId | STRING | NO |	
origClientOrderId | STRING | NO	|
recvWindow | LONG | NO | The value cannot be greater than ```60000```
timestamp | LONG | YES


* Either orderId or origClientOrderId must be sent.
* For some historical orders cummulativeQuoteQty will be < 0, meaning the data is not available at this time.



## Query Margin Account's Open Order (USER_DATA)


> **Response:**

```javascript
[
   {
       "clientOrderId": "qhcZw71gAkCCTv0t0k8LUK",
       "cummulativeQuoteQty": "0.00000000",
       "executedQty": "0.00000000",
       "icebergQty": "0.00000000",
       "isWorking": true,
       "orderId": 211842552,
       "origQty": "0.30000000",
       "price": "0.00475010",
       "side": "SELL",
       "status": "NEW",
       "stopPrice": "0.00000000",
       "symbol": "BNBBTC",
       "time": 1562040170089,
       "timeInForce": "GTC",
       "type": "LIMIT",
       "updateTime": 1562040170089
	}
]
```

``
GET /sapi/v1/margin/openOrders (HMAC SHA256)
``


**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | NO |
recvWindow | LONG | NO | The value cannot be greater than ```60000```
timestamp | LONG | YES

* If the symbol is not sent, orders for all symbols will be returned in an array.
* When all symbols are returned, the number of requests counted against the rate limiter is equal to the number of symbols currently trading on the exchange.




## Query Margin Account's All Order (USER_DATA)

> **Response:**

```javascript
[
      {
          "clientOrderId": "D2KDy4DIeS56PvkM13f8cP",
          "cummulativeQuoteQty": "0.00000000",
          "executedQty": "0.00000000",
          "icebergQty": "0.00000000",
          "isWorking": false,
          "orderId": 41295,
          "origQty": "5.31000000",
          "price": "0.22500000",
          "side": "SELL",
          "status": "CANCELED",
          "stopPrice": "0.18000000",
          "symbol": "BNBBTC",
          "time": 1565769338806,
          "timeInForce": "GTC",
          "type": "TAKE_PROFIT_LIMIT",
          "updateTime": 1565769342148
      },
      {
          "clientOrderId": "gXYtqhcEAs2Rn9SUD9nRKx",
          "cummulativeQuoteQty": "0.00000000",
          "executedQty": "0.00000000",
          "icebergQty": "1.00000000",
          "isWorking": true,
          "orderId": 41296,
          "origQty": "6.65000000",
          "price": "0.18000000",
          "side": "SELL",
          "status": "CANCELED",
          "stopPrice": "0.00000000",
          "symbol": "BNBBTC",
          "time": 1565769348687,
          "timeInForce": "GTC",
          "type": "LIMIT",
          "updateTime": 1565769352226
      },
      {
          "clientOrderId": "duDq1BqohhcMmdMs9FSuDy",
          "cummulativeQuoteQty": "0.39450000",
          "executedQty": "2.63000000",
          "icebergQty": "0.00000000",
          "isWorking": true,
          "orderId": 41297,
          "origQty": "2.63000000",
          "price": "0.00000000",
          "side": "SELL",
          "status": "FILLED",
          "stopPrice": "0.00000000",
          "symbol": "BNBBTC",
          "time": 1565769358139,
          "timeInForce": "GTC",
          "type": "MARKET",
          "updateTime": 1565769358139
      }

]
```

``
GET /sapi/v1/margin/allOrders (HMAC SHA256)
``


**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
orderId | LONG | NO	
startTime |	LONG | NO	
endTime | LONG | NO	
limit |	INT | NO | Default 500; max 1000.
recvWindow | LONG | NO | The value cannot be greater than ```60000```
timestamp | LONG | YES

* If orderId is set, it will get orders >= that orderId. Otherwise most recent orders are returned.     
* For some historical orders cummulativeQuoteQty will be < 0, meaning the data is not available at this time.



## Query Margin Account's Trade List (USER_DATA)


> **Response:**

```javascript
[
	{
		"commission": "0.00006000",
		"commissionAsset": "BTC",
		"id": 34,
		"isBestMatch": true,
		"isBuyer": false,
		"isMaker": false,
		"orderId": 39324,
		"price": "0.02000000",
		"qty": "3.00000000",
		"symbol": "BNBBTC",
		"time": 1561973357171
	}
]
```


``
GET  /sapi/v1/margin/myTrades (HMAC SHA256)
``


**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
startTime |	LONG | NO	
endTime | LONG | NO	
fromId | LONG | NO | TradeId to fetch from. Default gets most recent trades.
limit |	INT | NO | Default 500; max 1000.
recvWindow | LONG | NO | The value cannot be greater than ```60000```
timestamp | LONG | YES



* If fromId is set, it will get orders >= that fromId. Otherwise most recent orders are returned.




## Query Max Borrow (USER_DATA)


> **Response:**

```javascript
{
    "amount": "1.69248805"
}
```

``
GET /sapi/v1/margin/maxBorrowable (HMAC SHA256)
``

**Weight:**
5

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
asset | STRING | YES |
recvWindow | LONG | NO | The value cannot be greater than ```60000```
timestamp | LONG | YES



## Query Max Transfer-Out Amount (USER_DATA)


> **Response:**

```javascript
 {
      "amount": "3.59498107"
 }
```

``
GET /sapi/v1/margin/maxTransferable (HMAC SHA256)
``


**Weight:**
5

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
asset | STRING | YES |
recvWindow | LONG | NO | The value cannot be greater than ```60000```
timestamp | LONG | YES







# User Data Streams


* The base API endpoint is: **https://api.binance.com**
* A User Data Stream `listenKey` is valid for 60 minutes after creation.
* Doing a `PUT` on a `listenKey` will extend its validity for 60 minutes.
* Doing a `DELETE` on a `listenKey` will close the stream and invalidate the `listenKey`.
* Doing a `POST` on an account with an active `listenKey` will return the currently active `listenKey` and extend its validity for 60 minutes.
* The base websocket endpoint is: **wss://stream.binance.com:9443**
* User Data Streams are accessed at **/ws/\<listenKey\>** or **/stream?streams=\<listenKey\>**
* A single connection to **stream.binance.com** is only valid for 24 hours; expect to be disconnected at the 24 hour mark


## LISTEN KEY (SPOT)

### Create a ListenKey (USER_STREAM)

> **Response:**

```javascript
{
  "listenKey": "pqia91ma19a5s61cv6a81va65sdf19v8a65a1a5s61cv6a81va65sdf19v8a65a1"
}
```


``
POST /api/v3/userDataStream 
``

Start a new user data stream. The stream will close after 60 minutes unless a keepalive is sent. If the account has an active `listenKey`, that `listenKey` will be returned and its validity will be extended for 60 minutes.

**Weight:**
1

**Parameters:**

NONE



### Ping/Keep-alive a ListenKey (USER_STREAM)

> **Response:**

```javascript
{}
```

``
PUT /api/v3/userDataStream
``

Keepalive a user data stream to prevent a time out. User data streams will close after 60 minutes. It's recommended to send a ping about every 30 minutes.

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
listenKey | STRING | YES


### Close a ListenKey (USER_STREAM)

> **Response:**

```javascript
{}
```

``
DELETE /api/v3/userDataStream
``

Close out a user data stream.

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
listenKey | STRING | YES




## LISTEN KEY (MARGIN)

### Create a ListenKey (USER_STREAM)


> **Response:**

```javascript
{"listenKey":  "T3ee22BIYuWqmvne0HNq2A2WsFlEtLhvWCtItw6ffhhdmjifQ2tRbuKkTHhr"}
```


``
POST  /sapi/v1/userDataStream
``


**Weight:**
1

**Parameters:**

NONE



### Ping/Keep-alive a ListenKey (USER_STREAM)

> **Response:**

```javascript
{}
```

``
PUT  /sapi/v1/userDataStream
``



**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
listenKey | STRING | YES |



### Close a ListenKey (USER_STREAM)

> **Response:**

```javascript
{}
```

``
DELETE  /sapi/v1/userDataStream
``


**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
listenKey | STRING | YES |








## Payload: Account Update
Account state is updated with the `outboundAccountInfo` event.

> **Payload:**

```javascript
{
  "e": "outboundAccountInfo",   // Event type
  "E": 1499405658849,           // Event time
  "m": 0,                       // Maker commission rate (bips)
  "t": 0,                       // Taker commission rate (bips)
  "b": 0,                       // Buyer commission rate (bips)
  "s": 0,                       // Seller commission rate (bips)
  "T": true,                    // Can trade?
  "W": true,                    // Can withdraw?
  "D": true,                    // Can deposit?
  "u": 1499405658848,           // Time of last account update
  "B": [                        // Balances array
    {
      "a": "LTC",               // Asset
      "f": "17366.18538083",    // Free amount
      "l": "0.00000000"         // Locked amount
    },
    {
      "a": "BTC",
      "f": "10537.85314051",
      "l": "2.19464093"
    },
    {
      "a": "ETH",
      "f": "17902.35190619",
      "l": "0.00000000"
    },
    {
      "a": "BNC",
      "f": "1114503.29769312",
      "l": "0.00000000"
    },
    {
      "a": "NEO",
      "f": "0.00000000",
      "l": "0.00000000"
    }
  ]
  "P": [                        // Permissions
        "SPOT"
  ]  
}
```


An additional event `outboundAccountPosition` is sent any time an account balance has changed and contains the assets that were possibly changed by the event that generated the balance change.

> **Payload:**

```javascript
{
  "e": "outboundAccountPosition", //Event type
  "E": 1564034571105,             //Event Time
  "u": 1564034571073,             //Time of last account update
  "B": [                          //Balances Array
    {
      "a": "ETH",                 //Asset
      "f": "10000.000000",        //Free
      "l": "0.000000"             //Locked
    }
  ]
}
```

## Payload: Balance Update

Balance Update occurs during the following:

* Deposits or withdrawals from the account
* Transfer of funds between accounts (e.g. Spot to Margin)

> **Payload**

```javascript
{
  "e": "balanceUpdate",         //Event Type
  "E": 1573200697110,           //Event Time
  "a": "BTC",                   //Asset
  "d": "100.00000000",          //Balance Delta
  "T": 1573200697068            //Clear Time
}
```


## Payload: Order Update
Orders are updated with the `executionReport` event.

Check the [Public API Definitions](#public-api-definitions) and below for relevant enum definitions.

Average price can be found by doing `Z` divided by `z`.

> **Payload:**

```javascript
{
  "e": "executionReport",        // Event type
  "E": 1499405658658,            // Event time
  "s": "ETHBTC",                 // Symbol
  "c": "mUvoqJxFIILMdfAW5iGSOW", // Client order ID
  "S": "BUY",                    // Side
  "o": "LIMIT",                  // Order type
  "f": "GTC",                    // Time in force
  "q": "1.00000000",             // Order quantity
  "p": "0.10264410",             // Order price
  "P": "0.00000000",             // Stop price
  "F": "0.00000000",             // Iceberg quantity
  "g": -1,                       // OrderListId
  "C": null,                     // Original client order ID; This is the ID of the order being canceled
  "x": "NEW",                    // Current execution type
  "X": "NEW",                    // Current order status
  "r": "NONE",                   // Order reject reason; will be an error code.
  "i": 4293153,                  // Order ID
  "l": "0.00000000",             // Last executed quantity
  "z": "0.00000000",             // Cumulative filled quantity
  "L": "0.00000000",             // Last executed price
  "n": "0",                      // Commission amount
  "N": null,                     // Commission asset
  "T": 1499405658657,            // Transaction time
  "t": -1,                       // Trade ID
  "I": 8641984,                  // Ignore
  "w": true,                     // Is the order on the book?
  "m": false,                    // Is this trade the maker side?
  "M": false,                    // Ignore
  "O": 1499405658657,            // Order creation time
  "Z": "0.00000000",             // Cumulative quote asset transacted quantity
  "Y": "0.00000000",             // Last quote asset transacted quantity (i.e. lastPrice * lastQty)
  "Q": "0.00000000"              // Quote Order Qty
}
```

**Execution types:**

* NEW - The order has been accepted into the engine.
* CANCELED - The order has been canceled by the user. 
* REPLACED (currently unused)
* REJECTED - The order has been rejected and was not processed. (This is never pushed into the User Data Stream)
* TRADE - Part of the order or all of the order's quantity has filled.
* EXPIRED - The order was canceled according to the order type's rules (e.g. LIMIT FOK orders with no fill, LIMIT IOC or MARKET orders that partially fill) or by the exchange, (e.g. orders canceled during liquidation, orders canceled during maintenance)


If the order is an OCO, an event will be displayed named `ListStatus` in addition to the `executionReport` event.

> **Payload**

```javascript
{
  "e": "listStatus",                //Event Type
  "E": 1564035303637,               //Event Time
  "s": "ETHBTC",                    //Symbol
  "g": 2,                           //OrderListId
  "c": "OCO",                       //Contingency Type
  "l": "EXEC_STARTED",              //List Status Type
  "L": "EXECUTING",                 //List Order Status
  "r": "NONE",                      //List Reject Reason
  "C": "F4QN4G8DlFATFlIUQ0cjdD",    //List Client Order ID
  "T": 1564035303625,               //Transaction Time
  "O": [                            //An array of objects
    {
      "s": "ETHBTC",                //Symbol
      "i": 17,                      // orderId
      "c": "AJYsMjErWJesZvqlJCTUgL" //ClientOrderId
    },
    {
      "s": "ETHBTC",
      "i": 18,
      "c": "bfYPSQdLoqAJeNrOr9adzq"
    }
  ]
}
```





# Savings Endpoints

* The endpoints below allow you to interact with Binance Savings, previously known as Binance Lending.
* For more information on this, please refer to the [Binance Savings page](https://www.binance.com/en/lending)

## Get Flexible Product List (USER_DATA)

> **Response:**

```javascript
[
	{
	 	"asset": "BTC",
	  	"avgAnnualInterestRate": "0.00250025",
	  	"canPurchase": true,
	  	"canRedeem": true,
	  	"dailyInterestPerThousand": "0.00685000",
	  	"featured": true,
	  	"minPurchaseAmount": "0.01000000",
	  	"productId": "BTC001",
	  	"purchasedAmount": "16.32467016",
	  	"status": "PURCHASING",
	  	"upLimit": "200.00000000",
	  	"upLimitPerUser": "5.00000000"
	},
	{
		"asset": "BUSD",
	  	"avgAnnualInterestRate": "0.01228590",
	  	"canPurchase": true,
	  	"canRedeem": true,
	  	"dailyInterestPerThousand": "0.03836000",
	  	"featured": true,
	  	"minPurchaseAmount": "0.10000000",
	  	"productId": "BUSD001",
	  	"purchasedAmount": "10.38932339",
	  	"status": "PURCHASING",
	  	"upLimit": "100000.00000000",
	  	"upLimitPerUser": "50000.00000000"
	}
]
```

``
GET /sapi/v1/lending/daily/product/list (HMAC SHA256)
``


**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
status | ENUM | NO | "ALL", "SUBSCRIBABLE", "UNSUBSCRIBABLE"; default "ALL"	
featured | STRING | NO | “ALL”, "true"; default "ALL"
recvWindow | LONG | NO | The value cannot be greater than ```60000```
timestamp | LONG | YES



## Get Left Daily Purchase Quota of Flexible Product (USER_DATA)

> **Response:**

```javascript
{
	"asset": "BUSD", 
	"leftQuota": "50000.00000000"
}
```

``
GET /sapi/v1/lending/daily/userLeftQuota (HMAC SHA256)
``


**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
productId | STRING | YES | 
recvWindow | LONG | NO | The value cannot be greater than ```60000```
timestamp | LONG | YES



## Purchase Flexible Product (USER_DATA)

> **Response:**

```javascript
{
	"purchaseId": 40607
}
```

``
POST /sapi/v1/lending/daily/purchase (HMAC SHA256)
``


**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
productId | STRING | YES | 
amount | DECIMAL | YES
recvWindow | LONG | NO | The value cannot be greater than ```60000```
timestamp | LONG | YES




## Get Left Daily Redemption Quota of Flexible Product (USER_DATA)

> **Response:**

```javascript
{
	"asset": "USDT",
 	"dailyQuota": "10000000.00000000",
 	"leftQuota": "0.00000000",
 	"minRedemptionAmount": "0.10000000"
}
```

``
GET /sapi/v1/lending/daily/userRedemptionQuota (HMAC SHA256)
``

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
productId | STRING | YES | 
type | ENUM | YES | "FAST", “NORMAL“
recvWindow | LONG | NO | The value cannot be greater than ```60000```
timestamp | LONG | YES



## Redeem Flexible Product (USER_DATA)

> **Response:**

```javascript
{}
```

``
POST /sapi/v1/lending/daily/redeem (HMAC SHA256)
``

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
productId | STRING | YES | 
amount | DECIMAL | YES |
type | ENUM | YES | "FAST", “NORMAL“
recvWindow | LONG | NO | The value cannot be greater than ```60000```
timestamp | LONG | YES




## Get Flexible Product Position (USER_DATA)

> **Response:**

```javascript
[
	{
		"annualInterestRate": "0.02600000",
	  	"asset": "USDT",
	  	"avgAnnualInterestRate": "0.02599895",
	  	"canRedeem": true,
	  	"dailyInterestRate": "0.00007123",
	  	"freeAmount": "75.46000000",
	  	"freezeAmount": "0.00000000", // abandoned
	  	"lockedAmount": "0.00000000", // abandoned
	  	"productId": "USDT001",
	  	"productName": "USDT",
	  	"redeemingAmount": "0.00000000",
	  	"todayPurchasedAmount": "0.00000000",
	  	"totalAmount": "75.46000000",
	  	"totalInterest": "0.22759183"
	}
]
```

``
GET /sapi/v1/lending/daily/token/position (HMAC SHA256)
``

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
asset | STRING | YES | 
recvWindow | LONG | NO | The value cannot be greater than ```60000```
timestamp | LONG | YES




## Get Fixed and Customized Fixed Project List(USER_DATA)

> **Response:**

```javascript
[
	{
		"asset": "USDT",
	  	"displayPriority": 1,
	  	"duration": 90,
	  	"interestPerLot": "1.35810000",
	  	"interestRate": "0.05510000",
	  	"lotSize": "100.00000000",
	  	"lotsLowLimit": 1,
	  	"lotsPurchased": 74155,
	  	"lotsUpLimit": 80000,
	  	"maxLotsPerUser": 2000,
	  	"needKyc": False,
	  	"projectId": "CUSDT90DAYSS001",
	  	"projectName": "USDT",
	  	"status": "PURCHASING",
	  	"type": "CUSTOMIZED_FIXED",
	  	"withAreaLimitation": False
	}
]
```

``
GET /sapi/v1/lending/project/list (HMAC SHA256)
``


**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
asset | STRING | NO |
type | ENUM | YES | "REGULAR", "CUSTOMIZED_FIXED"
status | ENUM | NO | 	"ALL", "SUBSCRIBABLE", "UNSUBSCRIBABLE"; default "ALL" 
isSortAsc | BOOLEAN | NO  | default "true"
sortBy| ENUM | NO | "START_TIME", "LOT_SIZE", "INTEREST_RATE", "DURATION"; default "START_TIME"
current | LONG | NO | Currently querying page. Start from 1. Default:1
size | LONG | NO | 	Default:10, Max:100
recvWindow | LONG | NO | The value cannot be greater than ```60000```
timestamp | LONG | YES






## Purchase Customized Fixed Project  (USER_DATA)

> **Response:**

```javascript
{
	purchaseId: "18356"
}
```

``
POST /sapi/v1/lending/customizedFixed/purchase (HMAC SHA256)
``


**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
projectId | STRING | YES |
lot | LONG | YES | 
recvWindow | LONG | NO | The value cannot be greater than ```60000```
timestamp | LONG | YES






## Get Customized Fixed Project Position (USER_DATA)

> **Response:**

```javascript
[
	{
		"asset": "USDT",
	  	"canTransfer": True,
	  	"createTimestamp": 1587010770000,
	  	"duration": 14,
	  	"endTime": 1588291200000,
	  	"interest": "0.19950000",
	  	"interestRate": "0.05201250",
	  	"lot": 1,
	  	"positionId": 51724,
	  	"principal": "100.00000000",
	  	"projectId": "CUSDT14DAYSS001",
	  	"projectName": "USDT",
	  	"purchaseTime": 1587010771000,
	  	"redeemDate": "2020-05-01",
	  	"startTime": 1587081600000,
	  	"status": "HOLDING",
	  	"type": "CUSTOMIZED_FIXED"
	}
]
```

``
GET /sapi/v1/lending/project/position/list (HMAC SHA256)
``

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
asset | STRING | YES | 
projectId | STRING | NO |
status | ENUM | NO | "HOLDING", "REDEEMED"
recvWindow | LONG | NO | The value cannot be greater than ```60000```
timestamp | LONG | YES





## Lending Account (USER_DATA)

> **Response:**

```javascript
{
	"positionAmountVos": [
		{
			"amount": "75.46000000",
   			"amountInBTC": "0.01044819",
   			"amountInUSDT": "75.46000000",
   			"asset": "USDT"
   		},
  		{
  			"amount": "1.67072036",
   			"amountInBTC": "0.00023163",
   			"amountInUSDT": "1.67289230",
   			"asset": "BUSD"
   		}
   	],
 	"totalAmountInBTC": "0.01067982",
 	"totalAmountInUSDT": "77.13289230",
 	"totalFixedAmountInBTC": "0.00000000",
 	"totalFixedAmountInUSDT": "0.00000000",
 	"totalFlexibleInBTC": "0.01067982",
 	"totalFlexibleInUSDT": "77.13289230"
 }
```

``
GET /sapi/v1/lending/union/account (HMAC SHA256)
``

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------ 
recvWindow | LONG | NO | The value cannot be greater than ```60000```
timestamp | LONG | YES





## Get Purchase Record (USER_DATA)

> **Response:**

> Flexible Products

```javascript
[
 	{
 		"amount": "100.00000000",
  		"asset": "USDT",
  		"createTime": 1575018510000,
  		"lendingType": "DAILY",
  		"productName": "USDT",
  		"purchaseId": 26055,
  		"status": "SUCCESS"
  	}
]
```
> Fixed Products

```javascript
[
	{
		"amount": "100.00000000",
  		"asset": "USDT",
  		"createTime": 1575018453000,
  		"lendingType": "REGULAR",
  		"lot": 1,
  		"productName": "【Special】USDT 7D (8%)",
  		"purchaseId": 36857,
  		"status": "SUCCESS"
  	}
]
```

``
GET /sapi/v1/lending/union/purchaseRecord (HMAC SHA256)
``

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------ 
lendingType | ENUM | YES | "DAILY" for flexible, "REGULAR" for fixed, "CUSTOMIZED_FIXED" for customized fixed
asset | STRING | NO | 
startTime | LONG | NO |
endTime | LONG | NO |
current | LONG | NO | Currently querying page. Start from 1. Default:1
size | LONG | NO | 	Default:10, Max:100
recvWindow | LONG | NO | The value cannot be greater than ```60000```
timestamp | LONG | YES




## Get Redemption Record (USER_DATA)

> **Response:**

> Flexible Products

```javascript
[
	{
		"amount": "10.54000000",
  		"asset": "USDT",
	  	"createTime": 1577257222000,
	  	"principal": "10.54000000",
	  	"projectId": "USDT001",
	  	"projectName": "USDT",
	  	"status": "PAID",
	  	"type": "FAST"
	 }
]
```

> Fixed Products

```javascript
[
	{
		"amount": "0.07070000",
	  	"asset": "USDT",
	  	"createTime": 1566200161000,
	  	"interest": "0.00070000",
	  	"principal": "0.07000000",
	  	"projectId": "test06",
	  	"projectName": "USDT 1 day (10% anniualized)",
	  	"startTime": 1566198000000,
	  	"status": "PAID"
	 }
]
```


``
GET /sapi/v1/lending/union/redemptionRecord (HMAC SHA256)
``

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------ 
lendingType | ENUM | YES | "DAILY" for flexible, "REGULAR" for fixed, "CUSTOMIZED_FIXED" for customized fixed
asset | STRING | NO | 
startTime | LONG | NO |
endTime | LONG | NO |
current | LONG | NO | Currently querying page. Start from 1. Default:1
size | LONG | NO | 	Default:10, Max:100
recvWindow | LONG | NO | The value cannot be greater than ```60000```
timestamp | LONG | YES



## Get Interest History (USER_DATA)

> **Response:**


```javascript
[
	{
		"asset": "BUSD",
  		"interest": "0.00006408",
  		"lendingType": "DAILY",
  		"productName": "BUSD",
  		"time": 1577233578000
  	},
 	{
 		"asset": "USDT",
  		"interest": "0.00687654",
  		"lendingType": "DAILY",
  		"productName": "USDT",
  		"time": 1577233562000
  	}
]
```

``
GET /sapi/v1/lending/union/interestHistory (HMAC SHA256)
``

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------ 
lendingType | ENUM | YES | "DAILY" for flexible, "REGULAR" for fixed, "CUSTOMIZED_FIXED" for customized fixed
asset | STRING | NO | 
startTime | LONG | NO |
endTime | LONG | NO |
current | LONG | NO | Currently querying page. Start from 1. Default:1
size | LONG | NO | 	Default:10, Max:100
recvWindow | LONG | NO | The value cannot be greater than ```60000```
timestamp | LONG | YES







# Mining Endpoints

* The endpoints below allow to interact with Binance Pool.
* For more information on this, please refer to the [Binance Pool page](https://pool.binance.com/en)

## Acquiring Algorithm (MARKET_DATA)

>**Response:**

```javascript
{
  "code": 0,
  "msg": "",
  "data": [
    {
      "algoName": "sha256",
      "algoId": 1,
      "poolIndex": 0,
      "unit": "h/s"
    }
  ]
}
```

`GET /sapi/v1/mining/pub/algoList`

**Weight:**  
1  
**Parameter:**
None



## Acquiring CoinName (MARKET_DATA)

`GET /sapi/v1/mining/pub/coinList`

**Weight:**  
1  
**Parameter:**
None

>**Response:**  

```javascript
{
  "code": 0,
  "msg": "",
  "data": [
    {
      "coinName": "BTC",
      "coinId": 1,
      "poolIndex": 0,
      "algoId": 1,
      "algoName": "sha256"
    }
  ]
}
```




## Request for Detail Miner List (USER_DATA)


>**Response:**

```javascript
{
  "code": 0,
  "msg": "",
  "data": [
    {
      "workerName": "bhdc1.16A10404B",
      "type": "H_hashrate",
      "hashrateDatas": [
        {
          "time": 1587902400000,
          "hashrate": "0",
          "reject": 0
        },
        {
          "time": 1587906000000,
          "hashrate": "0",
          "reject": 0
        },
        .......
      ]
    },
    {
      "workerName": "bhdc1.16A10404B",
      "type": "D_hashrate",
      "hashrateDatas": [
        {
          "time": 1587902400000,
          "hashrate": "0",
          "reject": 0
        },
        {
          "time": 1587906000000,
          "hashrate": "0",
          "reject": 0
        },
        .......
      ]
    }
  ]
}

```

`GET /sapi/v1/mining/worker/detail  (HMAC SHA256)`

**Weight:**
5

**Parameter:**

Name | Type | Mandatory | Description | For Example 
------------ | ------------ | ------------ | ------------ | ------------ 
algo | STRING | YES | Algorithm(sha256) | sha256
userName | STRING | YES | Mining account | test
workerName | STRING | YES | Miner’s name（required） |bhdc1.16A10404B



## Request for Miner List (USER_DATA)


>**Response:**

```javascript
{
  "code": 0,
  "msg": "",
  "data": {
    "workerDatas": [
      {
        "workerId": "1420554439452400131",
        "workerName": "2X73",
        "status": 3,
        "hashRate": 0,
        "dayHashRate": 0,
        "rejectRate": 0,
        "lastShareTime": 1587712919000
      },
      {
        "workerId": "7893926126382807951",
        "workerName": "AZDC1.1A10101",
        "status": 2,
        "hashRate": 29711247541680,
        "dayHashRate": 12697781298013.66,
        "rejectRate": 0,
        "lastShareTime": 1587969727000
      },
     ......
    ],
    "totalNum": 18530,
    "pageSize": 20
  }
}
```

`GET /sapi/v1/mining/worker/list  (HMAC SHA256)`

**Weight:**
5

**Parameter:**

Name | Type | Mandatory | Description | For Example 
------------ | ------------ | ------------ | ------------ | ------------ 
algo | STRING | YES | Algorithm(sha256) | sha256
userName | STRING | YES | Mining account | test
pageIndex | INTEGER | NO | Page number，default is first page，start form 1             |
sort | INTEGER | NO | sort sequence（default=0）0 positive sequence，1 negative sequence |
sortColumn | INTEGER | NO | sort sequence（default=0）0 positive sequence，1 negative sequence |
workerStatus | INTEGER | NO | miners status（default=0）0 all，1 valid，2 invalid，3failure |



## Revenue List (USER_DATA)


>**Response:**

```javascript
{
  "code": 0,
  "msg": "",
  "data": {
    "accountProfits": [
      {
        "time": 1586188800000,
        "dayHashRate": 129129903378244,
        "profitAmount": 8.6083060304,
        "status": 2
      },
      {
        "time": 1586102400000,
        "dayHashRate": 129253700243001,
        "profitAmount": 30.8638644339,
        "status": 2
      },
      {
        "time": 1586102400000,
        "dayHashRate": 130393934523657,
        "profitAmount": 29.699802832,
        "status": 2
      }
    ],
    "totalNum": 3,
    "pageSize": 20
  }
}
```

`GET /sapi/v1/mining/payment/list  (HMAC SHA256)`

**Weight:**
5

**Parameter:**

Name | Type | Mandatory | Description | For Example 
------------ | ------------ | ------------ | ------------ | ------------ 
algo | STRING | YES | Algorithm(sha256) | sha256
userName | STRING | YES | Mining account | test
coin | STRING | NO | 币种名称 |
startDate | Long | NO | miners status（default=0）0 all，1 valid，2      |
endDate | Long | NO | Search date ms stamp，same default search all    |
pageIndex | INTEGER | NO | Page number，default is first page，start form 1 |




## Statistic List (USER_DATA)



>**Response:**

```javascript
{
  "code": 0,
  "msg": "",
  "data": {
    "fifteenMinHashRate": "457835490067496409.00000000",
    "dayHashRate": "214289268068874127.65000000",
    "validNum": 0,
    "invalidNum": 17562,
    "profitToday": "1.79483247",
    "profitYesterday": "1.79483247",
    "userName": "test",
    "unit": "h/s",
    "algo": "sha256"
  }
}
```


`GET /sapi/v1/mining/statistics/user/status (HMAC SHA256)`

**Weight:**
5

**Parameter:**

Name | Type | Mandatory | Description | For Example 
------------ | ------------ | ------------ | ------------ | ------------ 
algo | STRING | YES | Algorithm(sha256) | sha256
userName | STRING | YES | Mining account | test



## Account List (USER_DATA)


>**Response:**

```javascript
{
  "code": 0,
  "msg": "",
  "data": [
    {
      "type": "H_hashrate",
      "userName": "test",
      "list": [
        {
          "time": 1585267200000,
          "hashrate": "0.00000000",
          "reject": "0.00000000"
        },
        {
          "time": 1585353600000,
          "hashrate": "0.00000000",
          "reject": "0.00000000"
        }
        ......
      ]
    },
    {
      "type": "D_hashrate",
      "userName": "test",
      "list": [
        {
          "time": 1587906000000,
          "hashrate": "0.00000000",
          "reject": "0.00000000"
        },
        {
          "time": 1587909600000,
          "hashrate": "0.00000000",
          "reject": "0.00000000"
        }    ......
      ]
    }
  ]
}
```


`GET /sapi/v1/mining/statistics/user/list (HMAC SHA256)`


**Weight:**
5

**Parameter:**

Name | Type | Mandatory | Description | For Example 
------------ | ------------ | ------------ | ------------ | ------------ 
algo | STRING | YES | Algorithm(sha256) | sha256
userName | STRING | YES | Mining account | test





# Error Codes

> The error JSON payload:
 
```javascript
{
  "code":-1121,
  "msg":"Invalid symbol."
}
```

Errors consist of two parts: an error code and a message. Codes are universal, but messages can vary. 
 



## 10xx - General Server or Network issues
### -1000 UNKNOWN
 * An unknown error occured while processing the request.

### -1001 DISCONNECTED
 * Internal error; unable to process your request. Please try again.

### -1002 UNAUTHORIZED
 * You are not authorized to execute this request.

### -1003 TOO_MANY_REQUESTS
 * Too many requests queued.
 * Too much request weight used; please use the websocket for live updates to avoid polling the API.
 * Too much request weight used; current limit is %s request weight per %s %s. Please use the websocket for live updates to avoid polling the API.
 * Way too much request weight used; IP banned until %s. Please use the websocket for live updates to avoid bans.

### -1006 UNEXPECTED_RESP
 * An unexpected response was received from the message bus. Execution status unknown.

### -1007 TIMEOUT
 * Timeout waiting for response from backend server. Send status unknown; execution status unknown.

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

### -1099 Not found, authenticated, or authorized
 * This replaces error code -1999


## 11xx - 2xxx Request issues
### -1100 ILLEGAL_CHARS
 * Illegal characters found in a parameter.
 * Illegal characters found in parameter `%s`; legal range is `%s`.

### -1101 TOO_MANY_PARAMETERS
 * Too many parameters sent for this endpoint.
 * Too many parameters; expected `%s` and received `%s`.
 * Duplicate values for a parameter detected.

### -1102 MANDATORY_PARAM_EMPTY_OR_MALFORMED
 * A mandatory parameter was not sent, was empty/null, or malformed.
 * Mandatory parameter `%s` was not sent, was empty/null, or malformed.
 * Param `%s` or `%s` must be sent, but both were empty/null!

### -1103 UNKNOWN_PARAM
 * An unknown parameter was sent.

### -1104 UNREAD_PARAMETERS
 * Not all sent parameters were read.
 * Not all sent parameters were read; read `%s` parameter(s) but was sent `%s`.

### -1105 PARAM_EMPTY
 * A parameter was empty.
 * Parameter `%s` was empty.

### -1106 PARAM_NOT_REQUIRED
 * A parameter was sent when not required.
 * Parameter `%s` sent when not required.

### -1111 BAD_PRECISION
 * Precision is over the maximum defined for this asset.

### -1112 NO_DEPTH
 * No orders on book for symbol.

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
 * Data sent for paramter `%s` is not valid.

### -1131 BAD_RECV_WINDOW
 * recvWindow must be less than 60000

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

## 3xxx-4xxx SAPI-specific issues
### -3021 PAIR_ADMIN_BAN_TRADE
 * Margin account are not allowed to trade this trading pair.

### -3022 ACCOUNT_BAN_TRADE
 * You account's trading is banned.

### -3023 WARNING_MARGIN_LEVEL
 * You can't transfer out/place order under current margin level.

### -3024 FEW_LIABILITY_LEFT
 *  The unpaid debt is too small after this repayment.

### -3025 INVALID_EFFECTIVE_TIME
 * Your input date is invalid.

### -3026 VALIDATION_FAILED
 * Your input param is invalid.

### -3027 NOT_VALID_MARGIN_ASSET
 * Not a valid margin asset.

### -3028 NOT_VALID_MARGIN_PAIR
 * Not a valid margin pair.

### -3029 TRANSFER_FAILED
 * Transfer failed.

### -3036 ACCOUNT_BAN_REPAY
 * This account is not allowed to repay.

### -3037 PNL_CLEARING
 * PNL is clearing. Wait a second.

### -3038 LISTEN_KEY_NOT_FOUND
 * Listen key not found.

### -3042 PRICE_INDEX_NOT_FOUND
 * PriceIndex not available for this margin pair.

### -3999 NOT_WHITELIST_USER
 * This function is only available for invited users.

### -4001 CAPITAL_INVALID
 * Invalid operation.

### -4002 CAPITAL_IG
 * Invalid get.

### -4003 CAPITAL_IEV
 * Your input email is invalid.

### -4004 CAPITAL_UA
 * You don't login or auth.

### -4005 CAPAITAL_TOO_MANY_REQUEST
 * Too many new requests.

### -4006 CAPITAL_ONLY_SUPPORT_PRIMARY_ACCOUNT
 * Support main account only.

### -4007 CAPITAL_ADDRESS_VERIFICATION_NOT_PASS
 * Address validation is not passed.

### -4008 CAPITAL_ADDRESS_TAG_VERIFICATION_NOT_PASS
 * Address tag validation is not passed.

## 6XXX - Savings Issues
### -6001 DAILY_PRODUCT_NOT_EXIST
 * Daily product not exists.

### -6003 DAILY_PRODUCT_NOT_ACCESSIBLE
 * Product not exist or you don't have permission

### -6004 DAILY_PRODUCT_NOT_PURCHASABLE
 * Product not in purchase status

### -6005 DAILY_LOWER_THAN_MIN_PURCHASE_LIMIT
 * Smaller than min purchase limit

### -6006 DAILY_REDEEM_AMOUNT_ERROR
 * Redeem amount error

### -6007 DAILY_REDEEM_TIME_ERROR
 * Not in redeem time

### -6008 DAILY_PRODUCT_NOT_REDEEMABLE
 * Product not in redeem status

### -6009 REQUEST_FREQUENCY_TOO_HIGH
 * Request frequency too high

### -6011 EXCEEDED_USER_PURCHASE_LIMIT
 * Exceeding the maximum num allowed to purchase per user

### -6012 BALANCE_NOT_ENOUGH
 * Balance not enough

### -6013 PURCHASING_FAILED
 * Purchasing failed

### -6014 UPDATE_FAILED
 * Exceed up-limit allowed to purchased

### -6015 EMPTY_REQUEST_BODY
 * Empty request body

### -6016 PARAMS_ERR
 * Parameter err

### -6017 NOT_IN_WHITELIST
 * Not in whitelist

### -6018 ASSET_NOT_ENOUGH
 * Asset not enough

### -6019 PENDING
 * Need confirm

## -9xxx Filter failures
Error message | Description
------------ | ------------
"Filter failure: PRICE_FILTER" | `price` is too high, too low, and/or not following the tick size rule for the symbol.
"Filter failure: PERCENT_PRICE" | `price` is X% too high or X% too low from the average weighted price over the last Y minutes.
"Filter failure: LOT_SIZE" | `quantity` is too high, too low, and/or not following the step size rule for the symbol.
"Filter failure: MIN_NOTIONAL" | `price` * `quantity` is too low to be a valid order for the symbol.
"Filter failure: ICEBERG_PARTS" | `ICEBERG` order would break into too many parts; icebergQty is too small.
"Filter failure: MARKET_LOT_SIZE" | `MARKET` order's `quantity` is too high, too low, and/or not following the step size rule for the symbol.
"Filter failure: MAX_NUM_ORDERS" | Account has too many open orders on the symbol.
"Filter failure: MAX_ALGO_ORDERS" | Account has too many open stop loss and/or take profit orders on the symbol.
"Filter failure: MAX_NUM_ICEBERG_ORDERS" | Account has too many open iceberg orders on the symbol.
"Filter failure: EXCHANGE_MAX_NUM_ORDERS" | Account has too many open orders on the exchange.
"Filter failure: EXCHANGE_MAX_ALGO_ORDERS" | Account has too many open stop loss and/or take profit orders on the exchange.

## Order Rejection Issues

Error messages like these are indicated when the error is coming specifically from the matching engine:
   
* `1010_ERROR_MSG_RECEIVED`
* `-2010 NEW_ORDER_REJECTED`
* `2011 CANCEL_REJECTED`

The following messages which will indicate the specific error:

Error message | Description
------------ | ------------
"Unknown order sent." | The order (by either `orderId`, `clientOrderId`, `origClientOrderId`) could not be found.
"Duplicate order sent." | The `clientOrderId` is already in use.
"Market is closed." | The symbol is not trading.
"Account has insufficient balance for requested action." | Not enough funds to complete the action.
"Market orders are not supported for this symbol." | `MARKET` is not enabled on the symbol.
"Iceberg orders are not supported for this symbol." | `icebergQty` is not enabled on the symbol
"Stop loss orders are not supported for this symbol." | `STOP_LOSS` is not enabled on the symbol
"Stop loss limit orders are not supported for this symbol." | `STOP_LOSS_LIMIT` is not enabled on the symbol
"Take profit orders are not supported for this symbol." | `TAKE_PROFIT` is not enabled on the symbol
"Take profit limit orders are not supported for this symbol." | `TAKE_PROFIT_LIMIT` is not enabled on the symbol
"Price * QTY is zero or less." | `price` * `quantity` is too low
"IcebergQty exceeds QTY." | `icebergQty` must be less than the order quantity
"This action disabled is on this account." | Contact customer support; some actions have been disabled on the account.
"Unsupported order combination" | The `orderType`, `timeInForce`, `stopPrice`, and/or `icebergQty` combination isn't allowed.
"Order would trigger immediately." | The order's stop price is not valid when compared to the last traded price.
"Cancel order is invalid. Check origClientOrderId and orderId." | No `origClientOrderId` or `orderId` was sent in.
"Order would immediately match and take." | `LIMIT_MAKER` order type would immediately match and trade, and not be a pure maker order.
"The relationship of the prices for the orders is not correct." | The prices set in the `OCO` is breaking the Price rules. <br> The rules are: <br> `SELL Orders`: Limit Price > Last Price > Stop Price <br>`BUY Orders`: Limit Price < Last Price < Stop Price
"OCO orders are not supported for this symbol" | `OCO` is not enabled on the symbol.


# Notes
## Request Parameters

### Email Address
Email address should be encoded. e.g. `alice@test.com` should be encoded into `alice%40test.com`
