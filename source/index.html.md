---
title: Binance API Documentation
language_tabs: # must be one of https://git.io/vQNgJ
  #- shell
  #- javascript
  #- jason

toc_footers:
  - <a href='https://www.binance.com/cn/'>Binance Exchange</a>

includes:

search: true

---


# 更新日志

<font size=4>**2020-05-06**</font>

* 新增矿池接口:
	* `GET /sapi/v1/mining/pub/algoList`
	* `GET /sapi/v1/mining/pub/coinList`
	* `GET /sapi/v1/mining/worker/detail`
	* `GET /sapi/v1/mining/worker/list`
	* `GET /sapi/v1/mining/payment/list`
	* `GET /sapi/v1/mining/statistics/user/status`
	* `GET /sapi/v1/mining/statistics/user/list` 

---


<font size=4>**2020-05-03**</font>

* 杠杆交易部分接口的新的请求限制
  * 涉及的接口:
    * `POST /sapi/v1/margin/transfer`
    * `POST /sapi/v1/margin/loan`
    * `POST /sapi/v1/margin/repay`
  * 限制为每2秒一次。
  * 超过限制的请求会收到HTTP Status Code `429`。

---

<font size=4>**2020-05-01**</font>

* 从2020-05-01 UTC 00:00开始, 所有交易对都会有最多200个挂单的限制, 体现在过滤器[MAX_NUM_ORDERS](https://binance-docs.github.io/apidocs/spot/cn/#cc81fff589)上.
  * 已经存在的挂单不会被移除或者撤销。
  * 单交易对(`symbol`)的挂单数量达到或超过200的账号, 无法在此交易对上下新的订单, 除非挂单数量低于200。
  * OCO订单在被触发成`LIMIT`订单, 或者被触发成`STOP_LOSS`(或者`STOP_LOSS_LIMIT`)前, 被认为是2个挂单量. 一旦OCO订单被触发, 就只被算作一个挂单。

---

<font size=4>**2020-04-25**</font>

<font size=4>现货 API</font>

* 添加新字段 `permissions`
    * 这个字段定义了对于账户、交易对(`symbol`)的交易权限。
    * `permissions` 是个enum数组, 可能的值:
      * `SPOT`
      * `MARGIN`
    * 在未来的版本(v4)中, `permissions` 将会在 `GET api/v3/exchangeInfo` 中替换 `isSpotTradingAllowed` 和 `isMarginTradingAllowed`。
    * 如果账户想在一个交易对下做交易, 账户和交易对必须同时拥有对应的权限。
* 接口 `GET api/v3/exchangeInfo` 的更新
    *  添加新字段 `permissions`。
    *  添加新字段 `quoteAssetPrecision`。此字段和 `quotePrecision` 重复。在未来的版本(v4)中 `quotePrecision` 会被移除。
* 接口 `GET api/v3/account` 的更新
    * 添加新字段 `permissions` 。
* 添加新接口 `DELETE api/v3/openOrders`
    * 此接口便于用户撤销单一交易对的所有挂单, 包括OCO的挂单。
* 如果交易对处于 `BREAK` 或者 `HALT` 状态, 挂单也可以被撤销。

<font size=4> 用户数据 STREAM </font>

* `OutboundAccountInfo` 消息会显示一个新字段 `P`, 用来显示账户的交易权限。

---

<font size=4>**2020-04-23**</font>

WEB SOCKET 连接限制

* Websocket服务器每秒最多接受5个消息。消息包括:
	* PING帧
	* PONG帧
	* JSON格式的消息, 比如订阅, 断开订阅.
* 如果用户发送的消息超过限制，连接会被断开连接。反复被断开连接的IP有可能被服务器屏蔽。
* 单个连接最多可以订阅 **1024** 个Streams。

---

<font size=4>**2020-04-16**</font>

* 币安宝接口``GET /sapi/v1/lending/daily/token/position``返回内容新增字段：
	* `todayPurchasedAmount` 表示用户今日申购的活期产品数量

* 新增以下币安宝接口用以支持灵活定期产品:
	* ``GET /sapi/v1/lending/project/list``
	* ``POST /sapi/v1/lending/customizedFixed/purchase``
	* ``GET /sapi/v1/lending/project/position/list`` 


---



<font size=4>**2020-04-02**</font>

* 接口 ``GET /sapi/v1/capital/config/getall`` 返回内容新增字段：
	* `minConfirm` 表示资产上账所需的最小确认数
	* `unLockConfirm` 表示资产解锁需所需确认数

---


<font size=4>**2020-03-24**</font>

* 添加过滤器 `MAX_POSITION`.
    * 这个过滤器定义账户允许的基于`base asset`的最大仓位。一个用户的仓位可以定义为如下资产的总和:
        * `base asset`的可用余额
        * `base asset`的锁定余额
        * 所有处于open的买单的数量总和

    * 如果用户的仓位大于最大的允许仓位，买单会被拒绝。

---

<font size=4>**2020-03-13**</font>

* 新增可选参数 `transactionFeeFlag` 于以下提币接口:
	* ``POST /sapi/v1/capital/withdraw/apply``
	* ``POST /wapi/v3/withdraw.html``

---


<font size=4>**2020-02-05**</font>

* 新增子账户相关接口:
	* ``POST /sapi/v1/sub-account/futures/transfer``: 对子账户实施futures账户划转
	* ``POST /sapi/v1/sub-account/margin/transfer``: 对子账户实施margin账户划转
	* ``POST /sapi/v1/sub-account/transfer/subToSub``: 向兄弟子账户划转
	* ``POST /sapi/v1/sub-account/transfer/subToMaster``: 向母账户划转
	* ``GET /sapi/v1/sub-account/transfer/subUserHistory``: 子账户获取自身划转历史
	
---


<font size=4>**2020-01-15**</font>

* 接口``POST /wapi/v3/withdraw.html`` 新增参数 `withdrawOrderId`: 用户自定义提币id

* 接口``GET /wapi/v3/withdrawHistory.html`` 返回内容新增字段 `withdrawOrderId`: 该笔提币的用户自定义id


---
<font size=4>**2019-12-25**</font>

* 新增币安宝接口：
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

* 新增请求时间间隔于以下接口    
``GET  /sapi/v1/capital/withdraw/history``,    
``GET /wapi/v3/withdrawHistory.html``,    
``GET /sapi/v1/capital/deposit/hisrec`` and    
``GET /wapi/v3/depositHistory.html``:
	* 默认`startTime`为当前时间起90天前， 默认`endTime`为当前时间；
	* 请注意`startTime` 与 `endTime` 的默认时间戳，保证请求时间间隔不超过90天；
	* 同时提交`startTime` 与 `endTime`间隔不得超过90天.


<font size=4>**2019-12-18**</font>

* 新增接口用以获取账户每日资产快照:   
	`GET /sapi/v1/accountSnapshot`

---
<font size=4>**2019-11-30**</font>

* 接口`POST  /sapi/v1/margin/order (HMAC SHA256)`新增参数`sideEffectType`，可选内容如下:
	* `NO_SIDE_EFFECT`: 普通交易订单;
	* `MARGIN_BUY`: 自动借款交易订单;
	* `AUTO_REPAY`: 自动还款交易订单.

* New field `marginBuyBorrowAmount` and `marginBuyBorrowAsset` in `FULL` response to `POST  /sapi/v1/margin/order (HMAC SHA256)`

---


<font size=4>**2019-11-28**</font>

* 新增SAPI接口用以关闭账户站内划转功能：    
``
POST /sapi/v1/account/disableFastWithdrawSwitch (HMAC SHA256)
``
* 新增SAPI接口用以开启账户站内划转功能：     
``
POST /sapi/v1/account/enableFastWithdrawSwitch (HMAC SHA256)
``

---
<font size=4>**2019-11-22**</font>

* “报价总额市价单”作为新的市价单方式已在各交易对投入使用。
    * “报价总额市价单” 允许用户在市价单`MARKET`中设置总的购买投入金额或卖出预计回收金额 `quoteOrderQty`。
    * “报价总额市价单”不会突破`LOT_SIZE`的限制规则; 报单会按给定的`quoteOrderQty`尽可能接近地被执行。
    * 以`BNBBTC`交易对为例:
        * On the `BUY` side, the order will buy as many BNB as `quoteOrderQty` BTC can.
        * 买单: 给定`quoteOrderQty`的BTC会被用来市价买入尽可能多的BNB。 
        * On the `SELL` side, the order will sell as much BNB as needed to receive `quoteOrderQty` BTC.
        * 卖单: 持有BNB会被尽可能多地以市价卖出以获取给定`quoteOrderQty`的BTC。

        
---

<font size=4>**2019-11-19**</font>

* `GET /sapi/v1/sub-account/margin/account` 返回内容新增:
 	`marginTradeCoeffVo` 其中包括
	* `forceLiquidationBar`: 强平风险率;
	* `marginCallBar`: 补仓风险率;
	* `normalBar`: 初始风险率


---
<font size=4>**2019-11-13**</font>

<font size=4>Rest API </font>

* "api/v3/exchangeInfo" 新增内容:
    * `quoteOrderQtyMarketAllowed`
    * `baseCommissionDecimalPlaces`
    * `quoteCommissionDecimalPlaces`
* `MARKET` orders （市价单）新增可选参数: `quoteOrderQty`指定买入或卖出的报价数量，不可与 `quantity`（数量）同时使用.
    * 能够有效配合该参数使用`MARKET` orders（市价单）的确切时间和进一步详细信息将由后续声明予以通告。
* 所有订单查询接口增加新的返回内容：`origQuoteOrderQty` (e.g. GET api/v3/allOrders)

```json
	{
      "code": -1128,
      "msg": "Combination of optional parameters invalid. Recommendation: 'stopLimitTimeInForce' should also be sent."
	}
```

* 错误代码更新: -1128
    * 发送`OCO`订单中有`stopLimitPrice`但是没有`stopLimitTimeInForce`，将会受到错误信息:

* 错误代码更新: -1003, 明确了使用请求权重作为限制而不是请求数量。

    


**v1 接口将被弃用**:

2020年一季度末，以下接口将被移除。目前文档已经将这些接口更新为v3版本。

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

**以下接口将不会移植到v3版本，请使用新接口予以替换**

<table>
<tr>
<th>旧的 V1 接口</th>
<th>新的 V3 接口</th>
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

* 事件`executionReport`（订单更新）更新内容:
    * 如果 C 值为空, 将返回 `null`, 而不是`"null"`.
    * 新增返回值 Q, 表示 `quoteOrderQty`.

* 新增事件类型`balanceUpdate`（余额更新）
    * 当资金存入或从帐户中提取时，发生余额更新。

<font size=4> WEB SOCKET STREAM</font>

* WSS 现在支持实时订阅和取消数据流。

---




<font size=4>**2019-11-08**</font>

* 新增以下sapi接口用以管理子账户的杠杆与期货：
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

* 新增管理子账户充值功能相关的sapi接口
  * `GET /sapi/v1/capital/deposit/subAddress (HMAC SHA256))`: 获取子账户充值地址。
  * `GET /sapi/v1/capital/deposit/subHisrec (HMAC SHA256))`: 获取子账户充值记录。

---

<font size=4>**2019-10-29**</font>

* 新增钱包提币功能相关的sapi接口
  * `POST /sapi/v1/capital/withdraw/apply (HMAC SHA256)`: 提币。
  * `Get /sapi/v1/capital/withdraw/history (HMAC SHA256)`: 获取提币历史(支持多网络)。
 
---
<font size=4>**2019-10-14**</font>

* 新增钱包功能相关的sapi接口
  * `GET /sapi/v1/capital/config/getall (HMAC SHA256)`: 获取针对用户的所有币种信息。
  * `GET /sapi/v1/capital/deposit/hisrec (HMAC SHA256)`: 获取充值历史(支持多网络)。
  * `GET /sapi/v1/capital/deposit/address (HMAC SHA256)`: 获取充值地址(支持多网络).

---
<font size=4>**2019-10-11**</font>

* `POST /wapi/v3/withdraw.html`,增加参数 `network`,支持多网络提币。


---
<font size=4>**2019-09-09**</font>

* 新增bookTicker行情流: `<symbol>@bookTicker` 与`!bookTicker`. 

---
<font size=4>**2019-09-03**</font>

* 更新频率达到100ms的更快的 order book 信息流选项: `<symbol>@depth@100ms` 和 `<symbol>@depth#@100ms`
* `Websocket Market Streams` 增加 `Update Speed` 更新速度

---
<font size=4>**2019-08-16**</font>

* 10000 `限额`  的接口已被临时删除： GET api/v1/depth 

* 在2017年第四季度，以下接口已被弃用并将其从API文档中删除。 从此版本开始，以下接口已从API中永久删除。 对于原始变更日志如有遗漏，我们深表歉意:
    * GET api/v1/order
    * GET api/v1/openOrders
    * POST api/v1/order
    * DELETE api/v1/order
    * GET api/v1/allOrders
    * GET api/v1/account
    * GET api/v1/myTrades

* 在此存储库的文档中描述的流、接口、参数、有效负载等均被 **官方认证** 且 **得到支持**。 任何其他流、接口、参数或有效负载等的使用**不受支持， 自行使用的风险将由您自己承担，没有任何保证**。

---
<font size=4>**2019-09-15**</font>

<font size=4>Rest API</font>

* 新订单类型: OCO ("One Cancels the Other")
    * 一个 OCO 有 2 个订单: (在财务术语中也称为 legs)
        * ```STOP_LOSS``` 或 ```STOP_LOSS_LIMIT``` leg
        * ```LIMIT_MAKER``` leg

    * 价格限制:
        * ```SELL Orders``` : 限价 > 成交价>止损价
        * ```BUY Orders``` : 限价<成交价<止损价
        * 如前所述，价格必须“横跨”交易品种的最后交易价格。 例如：如果最后价格是10:
            * 卖出OCO的限制价格必须大于10，止损价格小于10。
            * 买入OCO的限制价格必须小于10，止损价格大于10。

    * 数量限制:
        * 两个 legs 的数量必须相同。
        * 但是，`ICEBERG`的数量不必相同。

    * 执行顺序:
        * 如果触发了`LIMIT_MAKER`，则在取消止损leg之前将首先执行限价支路。
        * 如果市场价格移动到将触发“ STOP_LOSS”或“ STOP_LOSS_LIMIT”，则在执行“ STOP_LOSS”支路之前，限价单支路将被取消。

    * 取消一个 OCO 订单
        * 取消任一订单的 leg 将取消整个 OCO 订单.
        * 可通过```orderListId``` 或 ```listClientOrderId```取消整个 OCO 订单。

    * OCO的新枚举:
        1. ```ListStatusType```
            * ```RESPONSE``` - 当ListStatus响应失败的操作时使用。 （下单或取消订单）
            * ```EXEC_STARTED``` - 在下订单列表或列表状态更新时使用。
            * ```ALL_DONE``` - 当订单清单完成执行且不再有效时使用。
        1. ```ListOrderStatus```
            * ```EXECUTING``` - 在下订单列表或列表状态更新时使用。
            * ```ALL_DONE``` - 当订单清单完成执行且不再有效时使用。
            * ```REJECT``` - 当ListStatus响应失败的操作时使用。 （下单或取消订单）
        1. ```ContingencyType```
            * ```OCO``` - 指定订单列表的类型。

    * 新的接口:
        * POST api/v3/order/oco
        * DELETE api/v3/orderList
        * GET api/v3/orderList

* ```recvWindow``` cannot exceed 60000.
* New `intervalLetter` values for headers:
    * SECOND => S
    * MINUTE => M
    * HOUR => H
    * DAY => D
* 新标头“ X-MBX-USED-WEIGHT-（intervalNum）（intervalLetter）”将为（intervalNum）（intervalLetter）速率限制器提供您当前使用的请求权重。 例如，如果设置了一分钟的请求速率权重限制器，则响应中将获得一个“ X-MBX-USED-WEIGHT-1M”标头。 旧标头X-MBX-USED-WEIGHT仍将返回，并代表一分钟请求速率权重限制的当前使用权重。
* 新标头“ X-MBX-ORDER-COUNT-（intervalNum）（intervalLetter）”会在任何有效的订单位置上更新，并跟踪该间隔的当前订单数； 拒绝/不成功的订单不保证在响应中具有`X-MBX-ORDER-COUNT-**`标头。
    
    * 例如： “ X-MBX-ORDER-COUNT-1S”用于“每1秒钟的订单”，`X-MBX-ORDER-COUNT-1D`用于“每1天的订单”
* GET api / v1 / depth现在支持`limit` 5000和10000; 权重分别是50和100。
* GET api / v1 / exchangeInfo具有一个新参数“ ocoAllowed”。 

<font size=4>用户数据流</font>

* ```executionReport```事件现在包含具有`orderListId``的“ g”； 对于非OCO订单，它将设置为-1。
* 新事件类型`listStatus`; `listStatus`是在更新任何OCO订单时发送的。
* 新事件类型`outboundAccountPosition`; 每当帐户余额发生变化时，就会发送`outboundAccountPosition`，并包含可能导致余额发生变化的事件（存款，提款，交易，下单或取消）更改的资产。


<font size=4>新的错误码</font>

* **-1131 BAD_RECV_WINDOW**
    * ```recvWindow``` 必须小于 60000
* **-1099未被找到，被认证或被授权**
     *替换错误代码-1999


<font size=4>新的-2011错误内容</font>

* **OCO_BAD_ORDER_PARAMS**
    * 其中一个订单的参数不正确。
* **OCO_BAD_PRICES**
    * 订单价格之间的关系不正确。
* **UNSUPPORTED_ORD_OCO**
    * 此交易对不支持OCO订单。

---
<font size=4>**2019-03-12**</font>

<font size=4>Rest API</font>

* X-MBX-USED-WEIGHT标头已添加到Rest API响应中。
* Retry-After标头已添加到Rest API 418和429响应中。
* 取消Rest API时，如果交易对的“状态”不是“ TRADING”，则现在可以返回“errorCode” -1013或-2011。
*`api/v1/depth`不再具有被忽略和为空的[[]`。
*`api/v3/myTrades`现在返回`quoteQty`; 价格*交易数量。
  
<font size=4>Websocket 流</font>

* `<symbol>@depth` 和 `<symbol>@depthX` 流不再具有被忽略且为空的“ []”。
  
<font size=4>系统改进</font>

* 匹配引擎稳定性/可靠性改进。
* Rest API性能改进。

---
<font size=4>**2018-11-13**</font>

<font size=4>Rest API</font>

* 现在可以在限制交易期间通过Rest API取消订单。
* 新的过滤器：`PERCENT_PRICE`，`MARKET_LOT_SIZE`，`MAX_NUM_ICEBERG_ORDERS`。
* 添加了`RAW_REQUST`速率限制。 限制取决于`X`分钟内的请求数量（不考虑重量）。
* 无交易对查询的`/api/v3/ticker/price`权重增加到2。
* `/api/v3/ticker/bookTicker`对于无符号查询增加了2的权重。
* `DELETE /api/v3/order`现在将返回订单最终状态的执行报告。
* `MIN_NOTIONAL`过滤器有两个新参数：
  * `applyToMarket`（过滤器是否应用于MARKET订单）
  * `avgPriceMins`（平均价格的分钟数）。
* `intervalNum`已添加到`/api/v1/exchangeInfo`限制中。 `intervalNum`描述间隔的数量。 例如：`intervalNum` 5，带有`interval`分钟，表示“每5分钟”。
  
<font size=4>平均价格的计算规则解释:</font>

1. 前5分钟的所有交易/数量交易的（数量*价格）。

2. 如果最近5分钟内没有交易，则以5分钟窗口外发生的第一笔交易为准。
    例如，如果最后一次交易是在20分钟前，则该交易的价格为5分钟的平均值。

3. 如果代码上没有交易，则没有平均价格，因此无法下达市价单。对于在MIN_NOTIONAL过滤器上启用了applyToMarket的新交易对，除非有至少一笔交易，才能下达市价单。

4. 当前的平均价格可以在这里查看：`https://api.binance.com/api/v3/avgPrice?symbol=<symbol>`
   例如:
   `https://api.binance.com/api/v3/avgPrice?symbol=BNBUSDT`

<font size=4>用户数据流</font>

* 将“最后报价资产交易量”（作为变量“ Y”）添加到执行报告中。 代表`lastPrice` 
* `lastQty`（`L` *`l`）。

---
<font size=4>**2018-07-18**</font>

<font size=4>Rest API</font>

* 新的过滤器：`ICEBERG_PARTS`
* ` post api/v3/order`为newOrderRespType`的新默认值。 ACK，RESULT或FULL； “ MARKET”和“ LIMIT”订单类型默认为“ FULL”，所有其他订单默认为“ ACK”。
* POST api/v3 order`RESULT`和`FULL`响应现在具有“ cummulativeQuoteQty”
* GET/api/v3/ openOrders的交易对权重减少到40。
* GET/api/v3/ ticker / 24hr，且交易对权重未降低至40。
* GET/api/v1/ trades的最大交易量增加到1000。
* GET/api/v1/ historicalTrades的最大交易量增加到1000。
* GET/api/v1/ aggTrades的最大总交易量增加到1000。
* GET/api/v1/ klines的最大总交易量增加到1000。
* 剩余的API订单查询现在返回`updateTime`，它代表订单的最后更新时间； time是订单创建时间。
* 订单查找接口现在将返回“ cummulativeQuoteQty”。如果“ cummulativeQuoteQty”小于0，则表示该时间该数据不可用。
* REQUESTS速率限制类型更改为REQUEST_WEIGHT。从逻辑上讲，此限制始终是请求权重，并且其先前的名称引起混乱。

<font size=4>用户数据流</font>

* 在订单响应和执行报告中添加了“ cummulativeQuoteQty”字段（作为变量“ Z”）。 表示已花费（使用“买入”订单）或已收到（使用“卖出”订单）的“报价”的累计金额。 历史订单在该字段中的值将小于0，这表明该数据目前不可用。 “ cummulativeQuoteQty”除以“ cummulativeQty”将得出订单的平均价格。
* `O`（订单创建时间）添加到执行报告中

---
<font size=4>**2018-01-23**</font>

* GET/api/v1/ historicalTrades权重降低到5
* GET/api/v1/ aggTrades权重降至1
* GET/api/v1/ klines权重降至1
* GET/api/v1/ticker / 24hr，所有交易品种的权重降低到交易交易品种的数量/ 2
* GET/api/v3/ allOrders权重降低到5
* GET/api/v3/ myTrades权重降低到5
* GET/api/v3/帐户权重降低到5
* GET/api/v1/深度限制= 500重量减少到5
* GET/api/v1/深度限制= 1000重量减少到10
* -1003错误消息已更新，可将用户定向到websocket

---
<font size=4>**2018-01-20**</font>

* GET/api/v1/ticker / 24hr单个符号权重降至1
* GET/api/v3/ openOrders所有交易对权重下降至交易交易品种数量/ 2
* GET/api/v3/allOrders权重降低到15
* GET/api/v3/ myTrades权重降低到15
* GET/api/v3/订单权重降至1
* myTrades现在将返回自交易/清洗交易的双方

---
<font size=4>**2018-01-14**</font>

* GET/api/v1/aggTrades权重更改为2
* GET/api/v1/klines权重更改为2
* GET/api/v3/订单权重更改为2
* GET/api/v3/ allOrders权重更改为20
* GET/api/v3/帐户权重更改为20
* GET/api/v3/ myTrades权重更改为20
* GET/api/v3/ historicalTrades权重更改为20


# 介绍

## API Key 设置

* 很多接口需要API Key才可以访问. 请参考[这个页面](https://binance.zendesk.com/hc/zh-cn/articles/360002502072-%E5%A6%82%E4%BD%95%E5%88%9B%E5%BB%BAAPI)来设置API Key.
* 设置API Key的同时，为了安全，建议设置IP访问白名单.
* **永远不要把你的API key/secret告诉给任何人**

<aside class="warning">
如果不小心泄露了API key，请立刻删除此Key, 并可以另外生产新的Key.
</aside>

## API Key 权限设置

* 新创建的API的默认权限是 `允许交易`, 可以用来下单。
* 如果需要通过API提款, 需要在UI修改权限, 选中 `允许提现`。

## 账户

### 现货账户

新注册的币安账号都会有一个现货(`SPOT`)账号。

### 杠杆账户

为了开设杠杆(`MARGIN`)账户, 可以参考[Binance杠杆交易账户设置指南](https://www.binance.vision/zh/tutorials/binance-margin-trading-guide)

## 现货测试网

用户可以使用现货的测试网来体验`SPOT`交易. 现在只能通过API来交易。

更多信息请参考[现货测试网](https://testnet.binance.vision/)。

## Postman Collections

现在你可以通过`Postman collection`来快速体验、使用API接口。<br/>
如果想了解更多如果使用Postman，请访问[Binance API Postman](https://github.com/binance-exchange/binance-api-postman)

## 联系我们

* [币安API电报群](https://t.me/Binance_api_Chinese)
    * 咨询关于API或者Websockets性能方面的问题.
    * 咨询文档中没有提及的API问题.
* [币安开发者社区](https://dev.binance.vision/)
    * 咨询关于API/Websockets代码实现，或者任何API/Websockets的问题.
* [币安客服](https://www.binance.com/cn/support-center)
    * 咨询关于账户，钱包，2FA等.


# 基本信息
## API 基本信息

* 接口可能需要用户的 API Key，如何创建API-KEY请参考[这里](https://www.binance.com/cn/support/articles/360002502072)
* 本篇列出接口的baseurl: **https://api.binance.com**
* 所有接口的响应都是 JSON 格式。
* 响应中如有数组，数组元素以时间**升序**排列，越早的数据越提前。  
* 所有时间、时间戳均为UNIX时间，单位为**毫秒**。

### HTTP 返回代码
* HTTP `4XX` 错误码用于指示错误的请求内容、行为、格式。问题在于请求者。
* HTTP `403` 错误码表示违反WAF限制(Web应用程序防火墙)。
* HTTP `429` 错误码表示警告访问频次超限，即将被封IP。
* HTTP `418` 表示收到429后继续访问，于是被封了。
* HTTP `5XX` 错误码用于指示Binance服务侧的问题。    
  使用接口 `/wapi/v3` 时, HTTP `504` 表示API服务端已经向业务核心提交了请求但未能获取响应，特别需要注意的是504代码不代表请求失败，而是未知。很可能已经得到了执行，也有可能执行失败，需要做进一步确认。

### 错误代码

* 使用接口 `/api/v3`, 以及 `/sapi/v1/margin`时, 每个接口都有可能抛出异常; 

> API 与 SAPI 的错误代码返回形式如下:

```javascript
{
  "code": -1121,
  "msg": "Invalid symbol."
}
```

* 当使用`/wapi/v3`, 任何接口都可能返回错误; 

> WAPI 的错误代码返回形式如下:

```javascript
{
  "success": false,
  "msg": "Invalid symbol."
}
```


* 具体的错误码及其解释在 [错误代码](#cf68bca02a).

### 接口的基本信息

* `GET` 方法的接口, 参数必须在 `query string`中发送。
* `POST`, `PUT`, 和 `DELETE` 方法的接口,参数可以在内容形式为`application/x-www-form-urlencoded`的 `query string` 中发送，也可以在 `request body` 中发送。 如果你喜欢，也可以混合这两种方式发送参数。
* 对参数的顺序不做要求。
* 但如果同一个参数名在query string和request body中都有，query string中的会被优先采用。
  
---
## 访问限制
### 访问限制基本信息
* 以下 是`intervalLetter` 作为头部值:
    * SECOND => S
    * MINUTE => M
    * HOUR => H
    * DAY => D
   
* 在/api/v3/exchangeInfo`rateLimits数组中包含与交易的有关RAW_REQUEST，REQUEST_WEIGHT和ORDER速率限制相关的对象。这些在“ Rate limiters（rateLimitType）”下的“ ENUM definitions”部分中进一步定义。

* 违反任何一个速率限制时，将返回429。

### IP 访问限制
* 每个请求将包含一个`X-MBX-USED-WEIGHT-(intervalNum)(intervalLetter)`的头，其中包含当前IP所有请求的已使用权重。
* 每个路由都有一个“权重”，该权重确定每个接口计数的请求数。较重的接口和对多个交易对进行操作的接口将具有较重的“权重”。
* 收到429时，您有责任作为API退回而不向其发送更多的请求。
* **如果屡次违反速率限制和/或在收到429后未能退回，将导致API的IP被禁（http状态418）。**
* 频繁违反限制，封禁时间会逐渐延长 ，**对于重复违反者，将会被封从2分钟到3天**。
* `Retry-After`的头会与带有418或429的响应发送，并且会给出**以秒为单位**的等待时长（如果是429）以防止禁令，或者如果是418，直到禁令结束。
* **访问限制是基于IP的，而不是API Key**

<aside class="notice">
建议您尽可能多地使用websocket消息获取相应数据，以减少请求带来的访问限制压力。
</aside>


###下单频率限制
* 每个成功的下单回报将包含一个`X-MBX-ORDER-COUNT-(intervalNum)(intervalLetter)`的头，其中包含当前账户已用的下单限制数量。
* 被拒绝或不成功的下单并不保证回报中包含以上头内容。
* **下单频率限制是基于每个账户计数的。**

---
## 接口鉴权类型
* 每个接口都有自己的鉴权类型，鉴权类型决定了访问时应当进行何种鉴权。
* 鉴权类型会在本文档中各个接口名称旁声明，如果没有特殊声明即默认为 `NONE`。
* 如果需要 API-keys，应当在HTTP头中以 `X-MBX-APIKEY`字段传递。
* API-keys 与 secret-keys **是大小写敏感的**。
* API-keys可以被配置为只拥有访问一些接口的权限。
 例如, 一个 API-key 仅可用于发送交易指令, 而另一个 API-key 则可访问除交易指令外的所有路径。
* 默认 API-keys 可访问所有鉴权路径.

鉴权类型 | 描述
------------ | ------------
NONE | 不需要鉴权的接口
TRADE | 需要有效的 API-Key 和签名
USER_DATA | 需要有效的 API-Key 和签名
USER_STREAM | 需要有效的 API-Key
MARKET_DATA | 需要有效的 API-Key


* `TRADE`, `MARGIN` 和`USER_DATA` 接口是 签名（SIGNED）接口.

---
## SIGNED (TRADE、USER_DATA AND MARGIN) Endpoint security
* 调用`SIGNED` 接口时，除了接口本身所需的参数外，还需要在`query string` 或 `request body`中传递 `signature`, 即签名参数。
* 签名使用`HMAC SHA256`算法. API-KEY所对应的API-Secret作为 `HMAC SHA256` 的密钥，其他所有参数作为`HMAC SHA256`的操作对象，得到的输出即为签名。
* `签名` **大小写不敏感**.
* “ totalParams”定义为与“ request body”串联的“ query string”。

### 时间同步安全
* 签名接口均需要传递 `timestamp`参数，其值应当是请求发送时刻的unix时间戳（毫秒）。
* 服务器收到请求时会判断请求中的时间戳，如果是5000毫秒之前发出的，则请求会被认为无效。这个时间空窗值可以通过发送可选参数 `recvWindow`来定义。

> 逻辑伪代码如下:

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

**关于交易时效性** 互联网状况并不完全稳定可靠,因此你的程序本地到币安服务器的时延会有抖动。这是我们设置`recvWindow`的目的所在，如果你从事高频交易，对交易时效性有较高的要求，可以灵活设置`recvWindow`以达到你的要求。

<aside class="notice">
推荐使用5秒以下的 recvWindow! 最多不能超过 60秒!
</aside>

### POST /api/v3/order 的示例
以下是在linux bash环境下使用 echo openssl 和curl工具实现的一个调用接口下单的示例 apikey、secret仅供示范

Key | Value
------------ | ------------
apiKey | vmPUZE6mv9SD5VNHk4HlWFsOr6aKE2zvsw0MuIgwCIPy6utIco14y7Ju91duEh8A
secretKey | NhqPtmdSJYdKjVHjA7PZj4Mge3R5YNiP1e3UZjInClVN65XAbvqqM6A7H5fATj0j


参数 | 取值
------------ | ------------
symbol | LTCBTC
side | BUY
type | LIMIT
timeInForce | GTC
quantity | 1
price | 0.1
recvWindow | 5000
timestamp | 1499827319559


#### 示例 1: 所有参数通过 request body 发送

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


#### 示例 2: 所有参数通过 query string 发送

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
* **queryString:**  

symbol=LTCBTC   
&side=BUY   
&type=LIMIT   
&timeInForce=GTC   
&quantity=1   
&price=0.1   
&recvWindow=5000   
&timestamp=1499827319559


#### 示例 3: 混合使用 query string 和 request body

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


请注意，签名与示例3不同。
“ GTC”和“ quantity = 1”之间没有＆。


### POST的签名接口示例/wapi/v3/withdraw.html
这是一个分步示例，说明如何从
使用`echo`，`openssl`和`curl`的Linux命令行。

Key | Value
------------ | ------------
apiKey | vmPUZE6mv9SD5VNHk4HlWFsOr6aKE2zvsw0MuIgwCIPy6utIco14y7Ju91duEh8A
secretKey | NhqPtmdSJYdKjVHjA7PZj4Mge3R5YNiP1e3UZjInClVN65XAbvqqM6A7H5fATj0j


参数 | 取值
------------ | ------------
asset | ETH
address  |0x6915f16f8791d0a1cc2bf47c13a6b2a92000504b
addressTag | 1 (Secondary address identifier for coins like XRP,XMR etc.)
amount | 1
recvWindow | 5000
name | addressName (Description of the address)
timestamp | 1508396497000
signature  | 157fb937ec848b5f802daa4d9f62bea08becbf4f311203bda2bd34cd9853e320


#### 示例 1: 所有参数通过 query string 发送

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

注意，对于`wapi`，参数必须以查询字符串的形式发送。

---

## 公开 API 参数
### 术语
* `base asset` 指一个交易对的交易对象，即写在靠前部分的资产名。
* `quote asset` 指一个交易对的定价资产，即写在靠后部分的资产名。

### 枚举定义
**交易对状态 (状态):**

* PRE_TRADING 交易前
* TRADING 交易中
* POST_TRADING 交易后
* END_OF_DAY 
* HALT
* AUCTION_MATCH
* BREAK

**交易对类型:**

* SPOT 现货

**订单状态 (状态):**

* NEW 新建订单
* PARTIALLY_FILLED 部分成交
* FILLED 全部成交
* CANCELED 已撤销
* PENDING_CANCEL  撤销中（目前并未使用）
* REJECTED 订单被拒绝
* EXPIRED 订单过期（根据timeInForce参数规则）

**OCO 状态 (状态类型集):**

* RESPONSE 当ListStatus响应失败的操作时使用。 （订单完成或取消订单）
* EXEC_STARTED 当已经下单或者订单有更新时
* ALL_DONE 当订单执行结束或者不在激活状态

**OCO 订单状态 (订单状态集):**

* EXECUTING 当已经下单或者订单有更新时
* ALL_DONE 当订单执行结束或者不在激活状态
* REJECT 当订单状态响应失败（订单完成或取消订单）

**指定订单的类型**

* OCO 选择性委托订单


**订单类型 (orderTypes, type):**

* LIMIT 限价单
* MARKET 市价单
* STOP_LOSS 止损单
* STOP_LOSS_LIMIT 限价止损单
* TAKE_PROFIT 止盈单
* TAKE_PROFIT_LIMIT 限价止盈单
* LIMIT_MAKER 限价卖单

**订单方向 (方向):**

* BUY 买入
* SELL 卖出

**有效方式 (timeInForce):**

* GTC 成交为止
* IOC 无法立即成交的部分就撤销
* FOK 无法全部立即成交就撤销

**K线间隔:**

m -> 分钟; h -> 小时; d -> 天; w -> 周; M -> 月

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

**限制种类 (rateLimitType)**

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

* REQUEST_WEIGHT 单位时间请求权重之和上限

* ORDERS 单位时间下单次数限制

* RAW_REQUESTS 单位时间请求次数上限

**限制间隔 (interval)**

* SECOND 秒
* MINUTE 分
* DAY 天

---
## 过滤器
过滤器，即Filter，定义了一系列交易规则。 共有两类，分别是针对交易对的过滤器`symbol filters`，和针对整个交易所的过滤器 `exchange filters`


### 交易对过滤器
#### PRICE_FILTER 价格过滤器

> **/exchangeInfo 响应中的格式:**

```javascript
  {
    "filterType": "PRICE_FILTER",
    "minPrice": "0.00000100",
    "maxPrice": "100000.00000000",
    "tickSize": "0.00000100"
  }
```

`价格过滤器` 用于检测订单中 `price` 参数的合法性。包含以下三个部分:

* `minPrice` 定义了 `price`/`stopPrice` 允许的最小值。
* `maxPrice` 定义了 `price`/`stopPrice` 允许的最大值。
* `tickSize` 定义了 `price`/`stopPrice` 的步进间隔，即price必须等于minPrice+(tickSize的整数倍)

以上每一项均可为0，为0时代表这一项不再做限制。

逻辑伪代码如下:

* `price` >= `minPrice` 
* `price` <= `maxPrice`
* (`price`-`minPrice`) % `tickSize` == 0


#### PERCENT_PRICE 价格振幅过滤器

> **/exchangeInfo 响应中的格式:**

```javascript
  {
    "filterType": "PERCENT_PRICE",
    "multiplierUp": "5",
    "multiplierDown": "0.2",
    "avgPriceMins": 5
  }
```

`PERCENT_PRICE`过滤器基于先前交易的平均值来定义价格的有效范围。   
`avgPriceMins`是计算平均价格的分钟数。 0表示使用最后的价格。

为了通过“价格百分比”，“价格”必须符合以下条件：

* `price` <=`weightedAveragePrice` *`multiplierUp`
* `price`> =`weightedAveragePrice` *`multiplierDown`



#### LOT_SIZE 订单尺寸


> **/exchangeInfo 响应中的格式:**

```javascript
  {
    "filterType": "LOT_SIZE",
    "minQty": "0.00100000",
    "maxQty": "100000.00000000",
    "stepSize": "0.00100000"
  }
```

Lots是拍卖术语，`LOT_SIZE` 过滤器对订单中的 `quantity` 也就是数量参数进行合法性检查。包含三个部分:

* `minQty` 表示 `quantity`/`icebergQty` 允许的最小值。
* `maxQty` 表示 `quantity`/`icebergQty` 允许的最大值。
* `stepSize` 表示 `quantity`/`icebergQty` 允许的步进值。

逻辑伪代码如下:

* `quantity` >= `minQty`
* `quantity` <= `maxQty`
* (`quantity`-`minQty`) % `stepSize` == 0


#### MIN_NOTIONAL 最小名义价值(成交额)

> **/exchangeInfo 响应中的格式:**

```javascript
  {
    "filterType": "MIN_NOTIONAL",
    "minNotional": "0.00100000",
    "applyToMarket": true,
    "avgPriceMins": 5
  }
```

MIN_NOTIONAL过滤器定义了交易对订单所允许的最小名义价值(成交额)。
订单的名义价值是`价格`*`数量`。   
`applyToMarket`确定 `MIN_NOTIONAL`过滤器是否也将应用于`MARKET`订单。   
由于`MARKET`订单没有价格，因此会在最后`avgPriceMins`分钟内使用平均价格。   
`avgPriceMins`是计算平均价格的分钟数。 0表示使用最后的价格。 



#### ICEBERG_PARTS 冰山订单拆分数

> **/exchangeInfo 响应中的格式:**

```javascript
  {
    "filterType": "ICEBERG_PARTS",
    "limit": 10
  }
```

`ICEBERG_PARTS` 代表冰山订单最多可以拆分成多少个小订单。   
计算方法为 `向上取整(qty / icebergQty)`。


#### MARKET_LOT_SIZE 市价订单尺寸

> ***/exchangeInfo 响应中的格式:**

```javascript
  {
    "filterType": "MARKET_LOT_SIZE",
    "minQty": "0.00100000",
    "maxQty": "100000.00000000",
    "stepSize": "0.00100000"
  }
```


`MARKET_LOT_SIZE`过滤器为交易对上的`MARKET`订单定义了`数量`（即拍卖中的“手数”）规则。 共有3部分：

* `minQty`定义了允许的最小`quantity`。
* `maxQty`定义了允许的最大数量。
* `stepSize`定义了可以增加/减少数量的间隔。

为了通过`market lot size`，`quantity`必须满足以下条件：

* `quantity` >= `minQty`
* `quantity` <= `maxQty`
* (`quantity`-`minQty`) % `stepSize` == 0



#### MAX_NUM_ORDERS 最多订单数

> **/exchangeInfo 响应中的格式:**

```javascript
  {
    "filterType": "MAX_NUM_ORDERS",
    "limit": 25
  }
```

定义了某个交易对最多允许的挂单数量（不包括已关闭的订单)       
普通订单与条件订单均计算在内



#### MAX_NUM_ALGO_ORDERS 最多条件单数

> **/exchangeInfo 响应中的格式:**

```javascript
  {
    "filterType": "MAX_NUM_ALGO_ORDERS",
    "maxNumAlgoOrders": 5
  }
```

`MAX_NUM_ALGO_ORDERS`过滤器定义允许账户在交易对上开设的“algo”订单的最大数量。    
"Algo”订单是`STOP_LOSS`，`STOP_LOSS_LIMIT`，`TAKE_PROFIT`和`TAKE_PROFIT_LIMIT`止盈止损单。



#### MAX_NUM_ICEBERG_ORDERS 最多冰山单数
`MAX_NUM_ICEBERG_ORDERS`过滤器定义了允许在交易对上开设账户的`ICEBERG`订单的最大数量。     
`ICEBERG`订单是icebergQty大于0的任何订单。.

> **/exchangeInfo 响应中的格式:**

```javascript
  {
    "filterType": "MAX_NUM_ICEBERG_ORDERS",
    "maxNumIcebergOrders": 5
  }
```

### MAX_POSITION 过滤器

这个过滤器定义账户允许的基于`base asset`的最大仓位。一个用户的仓位可以定义为如下资产的总和:
1. `base asset`的可用余额
1. `base asset`的锁定余额
1. 所有处于open的买单的数量总和

如果用户的仓位大于最大的允许仓位，买单会被拒绝。

> **/exchangeInfo 响应中的格式:**

```javascript
{
  "filterType": "MAX_POSITION",
  "maxPosition": "10.00000000"
}
```

### 交易所级别过滤器
#### EXCHANGE_MAX_NUM_ORDERS 最多订单数

> **/exchangeInfo 响应中的格式:**

```javascript
  {
    "filterType": "EXCHANGE_MAX_NUM_ORDERS",
    "maxNumOrders": 1000
  }
```

`MAX_NUM_ORDERS`过滤器定义了允许在交易对上开设账户的最大订单数。    
请注意，此过滤器同时计算“algo”订单和常规订单。



#### EXCHANGE_MAX_NUM_ALGO_ORDERS 交易最大ALGO订单数

> **/exchangeInfo 响应中的格式:**

```javascript
  {
    "filterType": "EXCHANGE_MAX_ALGO_ORDERS",
    "maxNumAlgoOrders": 200
  }
```

`MAX_ALGO_ORDERS`过滤器定义了允许在交易上开设账户的“algo”订单的最大数量。    
“Algo”订单是`STOP_LOSS`，`STOP_LOSS_LIMIT`，`TAKE_PROFIT`和`TAKE_PROFIT_LIMIT`订单。

---

# 钱包接口

## 系统状态 (System)


> **响应**

```javascript
{ 
    "status": 0,              // 0: 正常，1：系统维护
    "msg": "normal"           // normal|system maintenance
}
```

``
GET /wapi/v3/systemStatus.html
``

获取系统状态。



## 获取所有币信息 (USER_DATA)

获取针对用户的所有（Binance支持充提操作的）币种信息。

> **响应**

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
	   			"depositDesc": "Wallet Maintenance, Deposit Suspended", // 仅在充值关闭时返回
	   			"depositEnable": false,
	   			"isDefault": false,        
	   			"memoRegex": "^[0-9A-Za-z\\-_]{1,120}$",
	   			"minConfirm": 1,  // 上账所需的最小确认数
	   			"name": "BEP2",
	   			"network": "BNB",            
	   			"resetAddressStatus": false,
	   			"specialTips": "Both a MEMO and an Address are required to successfully deposit your BEP2-BTCB tokens to Binance.",
	   			"unLockConfirm": 0,  // 解锁需要的确认数 
	   			"withdrawDesc": "Wallet Maintenance, Withdrawal Suspended", // 仅在提现关闭时返回
	   			"withdrawEnable": false,
	   			"withdrawFee": "0.00000220",
	   			"withdrawMin": "0.00000440"
	   		},
	  		{
	  			"addressRegex": "^[13][a-km-zA-HJ-NP-Z1-9]{25,34}$|^(bc1)[0-9A-Za-z]{39,59}$",
	   			"coin": "BTC",
	   			"depositEnable": true,
	   			"insertTime": 1563532929000,
	   			"isDefault": true,
	   			"memoRegex": "",
	   			"minConfirm": 1,  // 上账所需的最小确认数
	   			"name": "BTC",
	   			"network": "BTC",
	   			"resetAddressStatus": false,
	   			"specialTips": "",
	   			"unLockConfirm": 0,  // 解锁需要的确认数 
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



**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
recvWindow | LONG | NO	
timestamp | LONG | YES	


## 查询每日资产快照 (USER_DATA)

> **响应**

```javascript
{
   "code":200, // 200表示返回正确，否则即为错误码
   "msg":"", // 与错误码对应的报错信息
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

> 或

```javascript
{
   "code":200, // 200表示返回正确，否则即为错误码
   "msg":"", // 与错误码对应的报错信息
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
> 或

```javascript
{
   "code":200, // 200表示返回正确，否则即为错误码
   "msg":"", // 与错误码对应的报错信息
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

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
type | STRING | YES | "SPOT", "MARGIN", "FUTURES"
startTime	| LONG | NO	
endTime | LONG | NO	
limit | INT | NO | min 5, max 30, default 5
recvWindow | LONG | NO 
timestamp | LONG | YES 





## 关闭站内划转 (USER_DATA)

> **响应**

```
{}
```

``
POST /sapi/v1/account/disableFastWithdrawSwitch (HMAC SHA256)
``

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
recvWindow | LONG | NO 
timestamp | LONG | YES 

* **注意:**

	此请求会关闭您账户的站内快速划转。您需要为api-key开通“trade”权限才能发送此请求。
	





## 开启站内划转 (USER_DATA)

> **响应**

```
{}
```

``
POST /sapi/v1/account/enableFastWithdrawSwitch (HMAC SHA256)
``

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
recvWindow | LONG | NO 
timestamp | LONG | YES 
 
* 此请求会开启您账户的站内快速划转。您需要为api-key开通“trade”权限才能发送此请求。
* 开启以后, 如果收款方为币安账户地址，转账费用为0, 速度快, 不需要提交上链请求。



## 提币[SAPI]

> **响应**

```javascript
{
    "id":"7213fea8e94b4a5593d507237e5a555b"
}
```

``
POST /sapi/v1/capital/withdraw/apply (HMAC SHA256)
``

Submit a withdraw request.

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
coin	|  STRING |	YES	
withdrawOrderId | STRING | NO |  自定义提币ID
network | STRING | NO | 提币网络
address	 | STRING | YES	| 提币地址
addressTag | STRING | NO | 某些币种例如 XRP,XMR 允许填写次级地址标签
amount | DECIMAL | YES	| 数量
transactionFeeFlag | BOOLEAN | NO | 当站内转账时免手续费, `true`: 手续费归资金转入方; `false`: 手续费归资金转出方; . 默认 `false`.
name | STRING | NO | 地址的备注，填写该参数后会加入该币种的提现地址簿。地址簿上限为20，超出后会造成提现失败。
recvWindow | LONG | NO	
timestamp | LONG | YES	

* 如果不发送 `network`, 将按该币种默认网络返回结果；
* 可以在接口`Get /sapi/v1/capital/config/getall (HMAC SHA256)`的返回值中某币种的`networkList` 获取 `network`网络字段和`isDefault`是否为默认网络。




## 提币

> **响应**

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

提交提现请求。

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
asset	|  STRING |	YES	 | 
withdrawOrderId | STRING | NO |  自定义提币ID
network | STRING | NO | 提币网络
address	 | STRING | YES	| 提币地址
addressTag | STRING | NO | 某些币种例如 XRP,XMR 允许填写次级地址标签
amount | DECIMAL | YES	
transactionFeeFlag | BOOLEAN | NO | 当站内转账时免手续费, `true`: 手续费归资金转入方; `false`: 手续费归资金转出方; . 默认 `false`.
name | STRING | NO | 地址的备注，填写该参数后会加入该币种的提现地址簿。地址簿上限为20，超出后会造成提现失败。
recvWindow | LONG | NO	
timestamp | LONG | YES	

* 如果不发送 `network`, 将按该币种默认网络返回结果；
* 可以在接口`Get /sapi/v1/capital/config/getall (HMAC SHA256)`的返回值中某币种的`networkList` 获取 `network`网络字段和`isDefault`是否为默认网络。


## 获取充值历史（支持多网络） (USER_DATA)

> **响应**

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


**权重:**
1

**参数:**

名称| 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
coin | STRING | NO	
status | INT | NO | 0(0:pending,6: credited but cannot withdraw, 1:success)
startTime | LONG | NO | 默认当前时间90天前的时间戳
endTime | LONG | NO | 默认当前时间戳
offset	| INT | NO | default:0
limit | INT | NO | 
recvWindow | LONG | NO	
timestamp | LONG | YES	

* 请注意`startTime` 与 `endTime` 的默认时间戳，保证请求时间间隔不超过90天.
* 同时提交``startTime`` 与 ``endTime``间隔不得超过90天.



## 获取充值历史 (USER_DATA)

> **响应**

```javascript
{
    "depositList": [
        {
            "insertTime": 1508198532000,   //币安系统记录该笔充值的时间
            "amount": 0.04670582,   //充值金额
            "asset": "ETH",   //充值资产
            "address": "0x6915f16f8791d0a1cc2bf47c13a6b2a92000504b",   //充值来源地址
            "txId": "0xdf33b22bdb2b28b1f75ccd201a4a4m6e7g83jy5fc5d5a9d1340961598cfcb0a1",   //充值交易id
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



**权重:**
1

**参数:**

名称| 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
asset | STRING | NO	
status | INT | NO | 0(0:充值进行中,1:充值已经成功)
startTime | LONG | NO | 默认当前时间90天前的时间戳
endTime | LONG | NO	| 默认当前时间戳
recvWindow | LONG | NO	
timestamp | LONG | YES	

* 请注意`startTime` 与 `endTime` 的默认时间戳，保证请求时间间隔不超过90天.
* 同时提交``startTime`` 与 ``endTime``间隔不得超过90天.


## 获取提币历史 (支持多网络)  (USER_DATA)


> **响应**

```javascript
[
	{
		"address": "0x94df8b352de7f46f64b01d3666bf6e936e44ce60",
  		"amount": "8.91000000",   // 提现转出金额
  		"applyTime": "2019-10-12 11:12:02",  // UTC 时间
  		"coin": "USDT",
  		"id": "b6ae22b3aa844210a7041aee7589627c",  // 该笔提现在币安的id
  		"withdrawOrderId": "WITHDRAWtest123", // 自定义ID, 如果没有则不返回该字段
  		"network": "ETH",
  		"transferType": 0 // 1: 站内转账, 0: 站外转账    
  		"status": 6,
  		"txId": "0xb5ef8c13b968a406cc62a93a8bd80f9e9a906ef1b3fcf20a2e48573c17659268"   // 提现交易id
  	},
 	{
 		"address": "1FZdVHtiBqMrWdjPyRPULCUceZPJ2WLCsB",
  		"amount": "0.00150000",
  		"applyTime": "2019-09-24 12:43:45",
  		"coin": "BTC",
  		"id": "156ec387f49b41df8724fa744fa82719",
  		"network": "BTC",
  		"transferType": 0,  // 1: 站内转账, 0: 站外转账
  		"status": 6,
  		"txId": "60fd9007ebfddc753455f95fafa808c4302c836e4d1eebc5a132c36c1d8ac354"
  	}
]
```

``
GET  /sapi/v1/capital/withdraw/history (HMAC SHA256)
``


**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
coin | STRING | NO	
status | INT | NO | 0(0:已发送确认Email,1:已被用户取消 2:等待确认 3:被拒绝 4:处理中 5:提现交易失败 6 提现完成)
offset | INT | NO
limit | INT | NO
startTime | LONG | NO | 默认当前时间90天前的时间戳
endTime | LONG | NO	| 默认当前时间戳
recvWindow | LONG | NO	
timestamp | LONG | YES	

* 支持多网络提币前的历史记录可能不会返回``network``字段.
* 请注意`startTime` 与 `endTime` 的默认时间戳，保证请求时间间隔不超过90天.
* 同时提交``startTime`` 与 ``endTime``间隔不得超过90天.





## 获取提币历史 (USER_DATA)


> **响应**

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
            "id":"7213fea8e94b4a5534ggsd237e5a555b",   //该笔提现在币安的id
            "withdrawOrderId": "withdrawtest",    // 该笔提现自定义id
            "amount": 999.9999,   //提现转出金额
            "transactionFee": 0.0001,   //提现手续费
            "address": "463tWEBn5XZJSxLU34r6g7h8jtxuNcDbjLSjkn3XAXHCbLrTTErJrBWYgHJQyrCwkNgYvyV3z8zctJLPCZy24jvb3NiTcTJ",   //提现目的地址
            "addressTag": "342341222",   //提现备注 只对某些币种存在
            "txId": "b3c6219639c8ae3f9cf010cdc24fw7f7yt8j1e063f9b4bd1a05cb44c4b6e2509",   //提现交易id
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


**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
asset | STRING | NO	
status | INT | NO | 0(0:已发送确认Email,1:已被用户取消 2:等待确认 3:被拒绝 4:处理中 5:提现交易失败 6 提现完成)
startTime | LONG | NO | 默认当前时间90天前的时间戳
endTime | LONG | NO	默认当前时间戳
recvWindow | LONG | NO	
timestamp | LONG | YES	

* 请注意`startTime` 与 `endTime` 的默认时间戳，保证请求时间间隔不超过90天.
* 同时提交``startTime`` 与 ``endTime``间隔不得超过90天.




## 获取充值地址 (支持多网络) (USER_DATA)

> **响应**

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

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
coin | STRING | YES	
network| STRING |NO | 
recvWindow | LONG | NO	
timestamp | LONG | YES	

* 如果不发送 `network`, 将按该币种默认网络返回结果；
* 可以在接口`Get /sapi/v1/capital/config/getall (HMAC SHA256)`的返回值中某币种的`networkList` 获取 `network`网络字段和`isDefault`是否为默认网络。




## 获取充值地址 (USER_DATA)


> **响应**

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


**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
asset | STRING | YES	
status | Boolean |NO
recvWindow | LONG | NO	
timestamp | LONG | YES	



## 账户状态 (USER_DATA)


> **响应**

```javascript
{
    "msg": "Order failed:Low Order fill rate! Will be reactivated after 5 minutes.",    //msg中给出了账户状态，左边的例子表示由于成交率过低被暂停交易权限。
    "success": true,
    "objs": [
        "5"
    ]
}
```

``
GET /wapi/v3/accountStatus.html
``

获取账户状态详情。

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
recvWindow | LONG | NO	
timestamp | LONG | YES	




## 账户API交易状态(USER_DATA)

> **响应**

```javascript
{
    "success": true,     // 查询结果
    "status": {          // 账户API交易状态详情
        "isLocked": false,   // API交易功能是否被锁
        "plannedRecoverTime": 0,  // API交易功能被锁情况下的预计恢复时间
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
        "updateTime": 1547630471725   
    }
}
```

``
GET /wapi/v3/apiTradingStatus.html
``


获取 api 账户交易状态详情。



**权重:**
1


**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
recvWindow | LONG | NO  
timestamp | LONG | YES  


## 小额资产转换BNB历史 (USER_DATA)


> **响应**

```javascript
{
    "success": true, 
    "results": {
        "total": 2,   //共计发生过的转换笔数
        "rows": [
            {
                "transfered_total": "0.00132256",   //本次转换所得BNB
                "service_charge_total": "0.00002699",   //本次转换手续费(BNB)
                "tran_id": 4359321,
                "logs": [           //本次转换的细节
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
                "logs": [       
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



**权重:**
1


**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
recvWindow | LONG | NO  
timestamp | LONG | YES  





## 小额资产转换 (USER_DATA)


> **响应**

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
POST /sapi/v1/asset/dust (HMAC SHA256)
``

把小额资产转换成 BNB.

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
asset | ARRAY | YES | 正在转换的资产。 例如：asset = BTC＆asset = USDT
recvWindow | LONG | NO |
timestamp | LONG | YES |




## 资产利息记录 (USER_DATA)


> **响应**

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
GET /sapi/v1/asset/assetDividend (HMAC SHA256)
``

获取资产利息记录。

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
asset | STRING | NO |
startTime | LONG | NO |
endTime | LONG | NO |
limit | INT | NO | Default 20, max 500
recvWindow | LONG | NO |
timestamp | LONG | YES |




## 上架资产详情 (USER_DATA)



> **响应**

```javascript
{
    "success": true,
    "assetDetail": {
        "CTR": {
            "minWithdrawAmount": "70.00000000",   //最小提现数量
            "depositStatus": false,   //是否可以充值(只有所有网络都关闭充值才为false)
            "withdrawFee": 35,   // 提现手续费
            "withdrawStatus": true,    //是否开放提现(只有所有网络都关闭提币才为false)
            "depositTip": "Delisted, Deposit Suspended"   //暂停充值的原因(如果暂停才有这一项)
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



**权重:**
1


**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
recvWindow | LONG | NO  
timestamp | LONG | YES  

* 充提币信息，建议查询 ``GET /sapi/v1/capital/config/getall`` 获取详情。




## 交易手续费率查询 (USER_DATA)


> **响应**

```javascript
{
	"tradeFee": [{
		"symbol": "ADABNB",
		"maker": 0.9000,
		"taker": 1.0000
	}, {
		"symbol": "BNBBTC",
		"maker": 0.3000,
		"taker": 0.3000
	}],
	"success": true
}
```

``
GET  /wapi/v3/tradeFee.html (HMAC SHA256)
``



**权重:**
1


**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
recvWindow | LONG | NO  
timestamp | LONG | YES  
symbol | STRING | NO




# 子母账户接口

* 这些接口的文档适用于[企业账户](https://www.binance.com/cn/support/articles/360020371872)。
* 关于如何成为企业用户，请参考: [企业账户申请](https://www.binance.com/cn/support/articles/360015552032)。

## 查询子账户列表(适用主账户)


> **响应**

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




**权重:**
1


**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
email | STRING | NO | 子账户邮箱 [备注](#request-email-address)
status | STRING | NO | 子账户状态: 启用或禁用
page | INT | NO | 默认值: 1
limit | INT | NO | 默认值: 500
recvWindow | LONG | NO  
timestamp | LONG | YES  



## 查询子账户划转历史(适用主账户)


> **响应**

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



**权重:**
1


**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
email | STRING | YES | 子账户邮箱 [备注](#request-email-address)
startTime | LONG | NO | 默认返回100天内历史记录
endTime | LONG | NO | 默认返回100天内历史记录
page | INT | NO | 默认值: 1
limit | INT | NO | 默认值: 500
recvWindow | LONG | NO  
timestamp | LONG | YES  



## 执行子账户划转(适用主账户)


> **响应**

```javascript
{
    "success":true,
    "txnId":"2966662589"
}

```

``
POST   /wapi/v3/sub-account/transfer.html (HMAC SHA256)
``

**权重:**
1


**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
fromEmail | STRING | YES | 发送者邮箱 [备注](#request-email-address)
toEmail | STRING | YES | 接收者邮箱 [备注](#request-email-address)
asset | STRING | YES
amount | DECIMAL | YES
recvWindow | LONG | NO  
timestamp | LONG | YES  




## 查询子账户资产(适用主账户)


> **响应**

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

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
email | STRING | YES  | 子账户邮箱 [备注](#request-email-address)
recvWindow | LONG | NO  
timestamp | LONG | YES  







## 获取子账户充值地址(适用主账户)


> **响应**

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



**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
email | STRING | YES  | 子账户邮箱 [备注](#request-email-address)
coin | STRING | YES|
network | STRING | NO
recvWindow | LONG | NO  
timestamp | LONG | YES  



## 获取子账户充值记录 (适用主账户)


> **响应**

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



**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
email | STRING | YES  | 子账户邮箱 [备注](#request-email-address)
coin | STRING | NO	
status | INT | NO | 0(0:pending,6: credited but cannot withdraw, 1:success)
startTime | LONG | NO	
endTime | LONG | NO	
limit | INT | NO
offset	| INT | NO | default:0
recvWindow | LONG | NO	
timestamp | LONG | YES




## 查询子账户Margin/Futures状态  (适用主账户)

> **响应**

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

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
email | STRING |	NO | 子账户邮箱 [备注](#request-email-address)
recvWindow | LONG |	NO	
timestamp | LONG |	YES	

* 如果不提交子账户email，返回所有子账户情况。 



##为子账户开通Margin  (适用主账户)

> **响应**

```javascript
{

    "email":"123@test.com",

    "isMarginEnabled": true

}
```

``POST /sapi/v1/sub-account/margin/enable (HMAC SHA256)``


**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
email | STRING | YES |	子账户邮箱 [备注](#request-email-address)
recvWindow | LONG |	NO	
timestamp | LONG | YES	



##查询子账户Margin账户详情  (适用主账户)

> **响应**

```javascript
{
      "email":"123@test.com",
      "marginLevel": "11.64405625",
      "totalAssetOfBtc": "6.82728457",
      "totalLiabilityOfBtc": "0.58633215",
      "totalNetAssetOfBtc": "6.24095242",
      "marginTradeCoeffVo": {
			"forceLiquidationBar": "1.10000000",  // 强平风险率
			"marginCallBar": "1.50000000",        // 补仓风险率
			"normalBar": "2.00000000"		      // 初始风险率
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

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
email | STRING | YES |	子账户邮箱 [备注](#request-email-address)
recvWindow |LONG | NO	
timestamp	LONG	YES	


##查询子账户Margin账户汇总  (适用主账户)


> **响应**

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


**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
recvWindow | LONG |	NO	
timestamp | LONG | YES	



##为子账户开通Futures  (适用主账户)

> **响应**

```javascript
{

    "email":"123@test.com",

    "isFuturesEnabled": true  // true or false

}
```

``POST /sapi/v1/sub-account/futures/enable (HMAC SHA256)``


**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
email | STRING | YES | 	子账户邮箱 [备注](#request-email-address)
recvWindow | LONG | NO	
timestamp | LONG | YES	



##查询子账户Futures账户详情  (适用主账户)

> **响应**

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

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
email | STRING | YES | 子账户邮箱 [备注](#request-email-address)
recvWindow | LONG | NO	
timestamp | LONG | YES	




##查询子账户Futures账户汇总  (适用主账户)

> **响应**

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

**权重:**
20

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
recvWindow | LONG | NO	
timestamp | LONG | YES	



##查询子账户合约持仓信息 (仅适用主账户)

> **响应**

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

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
email | STRING | YES | 子账户邮箱 [备注](#request-email-address)
recvWindow | LONG | NO	
timestamp | LONG | YES	


## 子账户Futures划转（仅适用主账户） 

> **响应**

```javascript
{
    "txnId":"2966662589"
}
```

``POST /sapi/v1/sub-account/futures/transfer (HMAC SHA256)`` 

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
email | String | YES | 子账户邮箱 [备注](#request-email-address)
asset | STRING | YES | 划转资产, e.g., USDT
amount | DECIMAL | YES | 划转数量
type| INT | YES | 1: transfer from spot main account to future account 2: transfer from future account to spot main account
timestamp | LONG | YES	


## 子账户Margin划转（仅适用主账户） 

> **响应**

```javascript
{
    "txnId":"2966662589"
}
```

``POST /sapi/v1/sub-account/margin/transfer (HMAC SHA256)`` 

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
email | String | YES | 子账户邮箱 [备注](#request-email-address)
asset | STRING | YES | 划转资产, e.g., USDT
amount | DECIMAL | YES | 划转数量
type| INT | YES | 1: transfer from spot main account to margin account 2: transfer from margin account to spot main account
timestamp | LONG | YES	



## 向共同主账户下的子账户主动划转（仅适用子账户） 

> **响应**

```javascript
{
    "txnId":"2966662589"
}
```

``POST /sapi/v1/sub-account/transfer/subToSub (HMAC SHA256)`` 

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
toEmail | STRING | YES | 接收者子邮箱地址 [备注](#request-email-address)
asset | STRING | YES	
amount | DECIMAL | YES	
recvWindow | LONG | NO	
timestamp | LONG | YES	


## 向主账户主动划转（仅适用子账户） 

> **响应**

```javascript
{
    "txnId":"2966662589"
}
```

``POST /sapi/v1/sub-account/transfer/subToMaster (HMAC SHA256)`` 

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
asset | STRING | YES	
amount | DECIMAL | YES	
recvWindow | LONG | NO	
timestamp | LONG | YES	


## 查询子账户划转历史 (仅适用子账户) 

> **响应**

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

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
asset | STRING | NO | 如不提供，返回所有asset 划转记录
type | INT | NO | 1: transfer in, 2: transfer out; 如不提供，返回所有方向划转记录
startTime | LONG | NO	
endTime | LONG | NO	
limit | INT | NO | 默认值: 500
recvWindow | LONG | NO	
timestamp | LONG | YES	





# 行情接口

## 测试服务器连通性



> **响应**

```javascript
{}
```

``
GET /api/v3/ping
``

测试能否联通 Rest API。

**权重:**

1

**参数:**

NONE



## 获取服务器时间


> **响应**

```javascript
{
  "serverTime": 1499827319559
}
```

``
GET /api/v3/time
``

测试能否联通 Rest API 并 获取服务器时间。

**权重:**
1

**参数:**

NONE



## 交易规范信息


> **响应**

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

获取交易规则和交易对信息。

**权重:**
1

**参数:**

NONE





## 深度信息


> **响应**

```javascript
{
  "lastUpdateId": 1027024,
  "bids": [
    [
      "4.00000000",     // 价位
      "431.00000000"    // 挂单量
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

**权重:**

基于限制调整:


限制 | 权重
------------ | ------------
5, 10, 20, 50, 100 | 1
500 | 5
1000 | 10
5000 | 50


**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
limit | INT | NO | 默认 100; 最大 1000. 可选值:[5, 10, 20, 50, 100, 500, 1000, 5000]





## 近期成交列表


> **响应**

```javascript
[
  {
    "id": 28457,
    "price": "4.00000100",
    "qty": "12.00000000",
    "time": 1499865549590, // 交易成交时间, 和websocket中的T一致.
    "isBuyerMaker": true,
    "isBestMatch": true
  }
]
```

``
GET /api/v3/trades
``

获取近期成交

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
limit | INT | NO | 默认 500; 最大值 1000.



## 查询历史成交


> **响应**

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
GET /api/v3/historicalTrades
``

获取历史成交。

**权重:**
5

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
limit | INT | NO | 默认 500; 最大值 1000.
fromId | LONG | NO | 从哪一条成交id开始返回. 缺省返回最近的成交记录。

* 需要提交`X-MBX-APIKEY` 


## 近期成交（归集）


> **响应**

```javascript
[
  {
    "a": 26129,         // 归集成交ID
    "p": "0.01633102",  // 成交价
    "q": "4.70443515",  // 成交量
    "f": 27781,         // 被归集的首个成交ID
    "l": 27781,         // 被归集的末个成交ID
    "T": 1498793709153, // 成交时间
    "m": true,          // 是否为主动卖出单
    "M": true           // 是否为最优撮合单(可忽略，目前总为最优撮合)
  }
]
```

``
GET /api/v3/aggTrades
``

归集交易与逐笔交易的区别在于，同一价格、同一方向、同一时间的trade会被聚合为一条

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
fromId | LONG | NO | 从包含fromId的成交id开始返回结果
startTime | LONG | NO | 从该时刻之后的成交记录开始返回结果
endTime | LONG | NO | 返回该时刻为止的成交记录
limit | INT | NO | 默认 500; 最大 1000.

* 如果发送startTime和endTime，间隔必须小于一小时。
* 如果没有发送任何筛选参数(fromId, startTime,endTime)，默认返回最近的成交记录



## K线数据


> **响应**

```javascript
[
  [
    1499040000000,      // 开盘时间
    "0.01634790",       // 开盘价
    "0.80000000",       // 最高价
    "0.01575800",       // 最低价
    "0.01577100",       // 收盘价(当前K线未结束的即为最新价)
    "148976.11427815",  // 成交量
    1499644799999,      // 收盘时间
    "2434.19055334",    // 成交额
    308,                // 成交笔数
    "1756.87402397",    // 主动买入成交量
    "28.46694368",      // 主动买入成交额
    "17928899.62484339" // 请忽略该参数
  ]
]
```

``
GET /api/v3/klines
``

每根K线代表一个交易对。   
每根K线的开盘时间可视为唯一ID

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
interval | ENUM | YES |
startTime | LONG | NO |
endTime | LONG | NO |
limit | INT | NO | 默认 500; 最大 1000.

* 如果未发送 startTime 和 endTime ，默认返回最近的交易。



## 当前平均价格

> **响应**

```javascript
{
  "mins": 5,
  "price": "9.35751834"
}
```

``
GET /api/v3/avgPrice
``


**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |





## 24hr 价格变动情况


> **响应**

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
  "firstId": 28385,   // 首笔成交id
  "lastId": 28460,    // 末笔成交id
  "count": 76         // 成交笔数
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
    "firstId": 28385,  
    "lastId": 28460,   
    "count": 76      
  }
]
```


``
GET /api/v3/ticker/24hr
``

24 小时滚动窗口价格变动数据。 请注意，不携带symbol参数会返回全部交易对数据，不仅数据庞大，而且权重极高

**权重:**

1 单一交易对;    
**40** 交易对参数缺失

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
symbol | STRING | NO |

* 请注意，不携带symbol参数会返回全部交易对数据




## 最新价格


> **响应**

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

获取交易对最新价格

**权重:**

1 单一交易对;    
**2** 交易对参数缺失

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
symbol | STRING | NO |

* 不发送交易对参数，则会返回所有交易对信息



## 当前最优挂单


> **响应**

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

返回当前最优的挂单(最高买单，最低卖单)

**权重:**

1 单一交易对;    
**2** 交易对参数缺失

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
symbol | STRING | NO |

* 不发送交易对参数，则会返回所有交易对信息



---
# Websocket 行情推送


* 本篇所列出的所有wss接口的baseurl为: **wss://stream.binance.com:9443**
* Streams有单一原始 stream 或组合 stream
* 单一原始 streams 格式为 **/ws/\<streamName\>**
* 组合streams的URL格式为 **/stream?streams=\<streamName1\>/\<streamName2\>/\<streamName3\>**
* 订阅组合streams时，事件payload会以这样的格式封装: **{"stream":"\<streamName\>","data":\<rawPayload\>}**
* stream名称中所有交易对均为 **小写**
* 每个到 **stream.binance.com** 的链接有效期不超过24小时，请妥善处理断线重连。
* 每3分钟，服务端会发送ping帧，客户端应当在10分钟内回复pong帧，否则服务端会主动断开链接。允许客户端发送不成对的pong帧(即客户端可以以高于10分钟每次的频率发送pong帧保持链接)。


## 实时订阅/取消数据流

* 以下数据可以通过websocket发送以实现订阅或取消订阅数据流。示例如下。
* 响应内容中的`id`是无符号整数，作为往来信息的唯一标识。
* 如果相应内容中的 `result` 为 `null`，表示请求发送成功。

### 订阅一个信息流

> **响应**

  ```javascript
  {
    "result": null,
    "id": 1
  }
  ```

* **请求**

  	{    
    	"method": "SUBSCRIBE",    
    	"params":     
    	[   
      	"btcusdt@aggTrade",    
      	"btcusdt@depth"     
    	],    
    	"id": 1   
  	}



### 取消订阅一个信息流

> **响应**
  
  ```javascript
  {
    "result": null,
    "id": 312
  }
  ```

* **请求**

  {   
    "method": "UNSUBSCRIBE",    
    "params":     
    [    
      "btcusdt@depth"   
    ],    
    "id": 312   
  }



### 已订阅信息流

> **响应**
  
  ```javascript
  {
    "result": [
      "btcusdt@aggTrade"
    ],
    "id": 3
  }
  ```


* **请求**

  {   
    "method": "LIST_SUBSCRIPTIONS",    
    "id": 3   
  }     
 


### 设定属性
当前，唯一可以设置的属性是设置是否启用`combined`(“组合”)信息流。   
当使用`/ws/`（“原始信息流”）进行连接时，combined属性设置为`false`，而使用 `/stream/`进行连接时则将属性设置为`true`。


> **响应**
  
  ```javascript
{
  "result": null,
  "id": 5
}
  ```

* **请求**

  {    
    "method": "SET_PROPERTY",    
    "params":     
    [   
      "combined",    
      true   
    ],    
    "id": 5   
  }




### 检索属性

> **响应**

  ```javascript
  {
    "result": true, // Indicates that combined is set to true.
    "id": 2
  }
  ```
  
* **请求**
  
  {   
    "method": "GET_PROPERTY",    
    "params":     
    [   
      "combined"   
    ],    
    "id": 2   
  }   
 



###错误信息

错误信息 | 描述
---|---
{"code": 0, "msg": "Unknown property"} |  `SET_PROPERTY` 或 `GET_PROPERTY`中应用的参数无效
{"code": 1, "msg": "Invalid value type: expected Boolean"} | 仅接受`true`或`false`
{"code": 2, "msg": "Invalid request: property name must be a string"}| 提供的属性名无效
{"code": 2, "msg": "Invalid request: request ID must be an unsigned integer"}| 参数`id`未提供或`id`值是无效类型
{"code": 2, "msg": "Invalid request: unknown variant %s, expected one of `SUBSCRIBE`, `UNSUBSCRIBE`, `LIST_SUBSCRIPTIONS`, `SET_PROPERTY`, `GET_PROPERTY` at line 1 column 28"} | 错字提醒，或提供的值不是预期类型
{"code": 2, "msg": "Invalid request: too many parameters"}| 数据中提供了不必要参数
{"code": 2, "msg": "Invalid request: property name must be a string"} | 未提供属性名
{"code": 2, "msg": "Invalid request: missing field `method` at line 1 column 73"} | 数据未提供`method`
{"code":3,"msg":"Invalid JSON: expected value at line %s column %s"} | JSON 语法有误.




## 归集交易流


> **Payload:**

```javascript
{
  "e": "aggTrade",  // 事件类型
  "E": 123456789,   // 事件时间
  "s": "BNBBTC",    // 交易对
  "a": 12345,       // 归集交易ID
  "p": "0.001",     // 成交价格
  "q": "100",       // 成交数量
  "f": 100,         // 被归集的首个交易ID
  "l": 105,         // 被归集的末次交易ID
  "T": 123456785,   // 成交时间
  "m": true,        // 买方是否是做市方。如true，则此次成交是一个主动卖出单，否则是一个主动买入单。
  "M": true         // 请忽略该字段
}
```

归集交易 stream 推送交易信息，是对单一订单的集合。

**Stream 名称:** `<symbol>@aggTrade`   
**Update Speed:** 实时


## 逐笔交易

> **Payload:**

```javascript
{
  "e": "trade",     // 事件类型
  "E": 123456789,   // 事件时间
  "s": "BNBBTC",    // 交易对
  "t": 12345,       // 交易ID
  "p": "0.001",     // 成交价格
  "q": "100",       // 成交数量
  "b": 88,          // 买方的订单ID
  "a": 50,          // 卖方的订单ID
  "T": 123456785,   // 成交时间
  "m": true,        // 买方是否是做市方。如true，则此次成交是一个主动卖出单，否则是一个主动买入单。
  "M": true         // 请忽略该字段
}
```

**Stream Name:** `<symbol>@trade`

逐笔交易推送每一笔成交的信息。**成交**，或者说交易的定义是仅有一个吃单者与一个挂单者相互交易




## K线 Streams

> **Payload:**

```javascript
{
  "e": "kline",     // 事件类型
  "E": 123456789,   // 事件时间
  "s": "BNBBTC",    // 交易对
  "k": {
    "t": 123400000, // 这根K线的起始时间
    "T": 123460000, // 这根K线的结束时间
    "s": "BNBBTC",  // 交易对
    "i": "1m",      // K线间隔
    "f": 100,       // 这根K线期间第一笔成交ID
    "L": 200,       // 这根K线期间末一笔成交ID
    "o": "0.0010",  // 这根K线期间第一笔成交价
    "c": "0.0020",  // 这根K线期间末一笔成交价
    "h": "0.0025",  // 这根K线期间最高成交价
    "l": "0.0015",  // 这根K线期间最低成交价
    "v": "1000",    // 这根K线期间成交量
    "n": 100,       // 这根K线期间成交数量
    "x": false,     // 这根K线是否完结（是否已经开始下一根K线）
    "q": "1.0000",  // 这根K线期间成交额
    "V": "500",     // 主动买入的成交量
    "Q": "0.500",   // 主动买入的成交额
    "B": "123456"   // 忽略此参数
  }
}
```

K线stream逐秒推送所请求的K线种类(最新一根K线)的更新。

**Stream Name:** `<symbol>@kline_<interval>`    

**Update Speed:** 2000ms

**K线图间隔参数:**

m -> 分钟; h -> 小时; d -> 天; w -> 周; M -> 月

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




## 按 Symbol 的精简Ticker

> **Payload:**

```javascript
  {
    "e": "24hrMiniTicker",  // 事件类型
    "E": 123456789,         // 事件时间
    "s": "BNBBTC",          // 交易对
    "c": "0.0025",          // 最新成交价格
    "o": "0.0010",          // 24小时前开始第一笔成交价格
    "h": "0.0025",          // 24小时内最高成交价
    "l": "0.0010",          // 24小时内最低成交加
    "v": "10000",           // 成交量
    "q": "18"               // 成交额
  }
```

按Symbol刷新的最近24小时精简ticker信息

**Stream 名称:** `<symbol>@miniTicker`

**Update Speed:** 1000ms



## 全市场所有Symbol的精简Ticker

> **Payload:**
```javascript
[
  {
    // 数组每一个元素对应一个交易对，内容与 \<symbol\>@miniTicker相同
  }
]
```

同上，只是推送所有交易对.需要注意的是，只有更新的ticker才会被推送.

**Stream名称:** !miniTicker@arr

**Update Speed:** 1000ms



## 按Symbol的完整Ticker


> **Payload:**

```javascript
{
  "e": "24hrTicker",  // 事件类型
  "E": 123456789,     // 事件时间
  "s": "BNBBTC",      // 交易对
  "p": "0.0015",      // 24小时价格变化
  "P": "250.00",      // 24小时价格变化（百分比）
  "w": "0.0018",      // 平均价格
  "x": "0.0009",      // 整整24小时之前，向前数的最后一次成交价格
  "c": "0.0025",      // 最新成交价格
  "Q": "10",          // 最新成交交易的成交量
  "b": "0.0024",      // 目前最高买单价
  "B": "10",          // 目前最高买单价的挂单量
  "a": "0.0026",      // 目前最低卖单价
  "A": "100",         // 目前最低卖单价的挂单量
  "o": "0.0010",      // 整整24小时前，向后数的第一次成交价格
  "h": "0.0025",      // 24小时内最高成交价
  "l": "0.0010",      // 24小时内最低成交加
  "v": "10000",       // 24小时内成交量
  "q": "18",          // 24小时内成交额
  "O": 0,             // TODO 0 ??? 统计开始时间？？？
  "C": 86400000,      // TODO 3600*24*1000，24小时的毫秒数 ??? 统计关闭时间？？？
  "F": 0,             // 24小时内第一笔成交交易ID
  "L": 18150,         // 24小时内最后一笔成交交易ID
  "n": 18151          // 24小时内成交数
}
```


每秒推送单个交易对的过去24小时滚动窗口标签统计信息。

**Stream 名称:** `<symbol>@ticker`

**Update Speed:** 1000ms



## 全市场所有交易对的完整Ticker

> **Payload:**

```javascript
[
  {
    // Same as <symbol>@ticker payload
  }
]
```

**Stream Name:** `!ticker@arr`

**Update Speed:** 1000ms

推送全市场所有交易对刷新的24小时完整ticker信息。需要注意的是，没有更新的ticker不会被推送。




## 按Symbol的最优挂单信息

> **Payload:**

```javascript
{
  "u":400900217,     // order book updateId
  "s":"BNBUSDT",     // 交易对
  "b":"25.35190000", // 买单最优挂单价格
  "B":"31.21000000", // 买单最优挂单数量
  "a":"25.36520000", // 卖单最优挂单价格
  "A":"40.66000000"  // 卖单最优挂单数量
}
```


实时推送指定交易对最优挂单信息

**Stream Name:** `<symbol>@bookTicker`

**Update Speed:** 实时





## 全市场最优挂单信息

> **Payload:**

```javascript
{
  // Same as <symbol>@bookTicker payload
}
```

实时推送所有交易对交易对最优挂单信息

**Stream Name:** `!bookTicker`

**Update Speed:** 实时





## 有限档深度信息

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

每秒或每100毫秒推送有限档深度信息。levels表示几档买卖单信息, 可选 5/10/20档

**Stream Names:** `<symbol>@depth<levels>` 或 `<symbol>@depth<levels>@100ms`.  

**Update Speed:** 1000ms 或 100ms







## 增量深度信息


> **Payload:**

```javascript
{
  "e": "depthUpdate", // 事件类型
  "E": 123456789,     // 事件时间
  "s": "BNBBTC",      // 交易对
  "U": 157,           // 从上次推送至今新增的第一个 update Id
  "u": 160,           // 从上次推送至今新增的最后一个 update Id
  "b": [              // 变动的买单深度
    [
      "0.0024",       // 变动的价格档位
      "10"            // 数量
    ]
  ],
  "a": [              // 变动的卖单深度
    [
      "0.0026",       // 变动的价格档位
      "100"           // 数量
    ]
  ]
}
```

每秒或每100毫秒推送orderbook的变化部分（如果有）

**Stream Name:** `<symbol>@depth` 或 `<symbol>@depth@100ms`

**Update Speed:** 1000ms 或 100ms






## 如何正确在本地维护一个orderbook副本
1. 订阅 **wss://stream.binance.com:9443/ws/bnbbtc@depth**
2. 开始缓存收到的更新。同一个价位，后收到的更新覆盖前面的。
3. 访问Rest接口 **https://www.binance.com/api/v1/depth?symbol=BNBBTC&limit=1000** 获得一个1000档的深度快照
4. 将目前缓存到的信息中`u` <= 步骤3中获取到的快照中的`lastUpdateId`的部分丢弃(丢弃更早的信息，已经过期)。
5. 将深度快照中的内容更新到本地orderbook副本中，并从websocket接收到的第一个`U` <= `lastUpdateId`+1 **且** `u` >= `lastUpdateId`+1 的event开始继续更新本地副本。
6. 每一个新event的`U`应该恰好等于上一个event的`u`+1，否则可能出现了丢包，请从step3重新进行初始化。
7. 每一个event中的挂单量代表这个价格目前的挂单量**绝对值**，而不是相对变化。
8. 如果某个价格对应的挂单量为0，表示该价位的挂单已经撤单或者被吃，应该移除这个价位。






# 现货账户和交易接口


## 测试下单 (TRADE)


> **响应**

```javascript
{}
```

``
POST /api/v3/order/test (HMAC SHA256)
``


用于测试订单请求，但不会提交到撮合引擎

**权重:**
1

**参数:**

同于 `POST /api/v3/order`





## 下单  (TRADE)

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

发送下单。

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
side | ENUM | YES |
type | ENUM | YES |
timeInForce | ENUM | NO |
quantity | DECIMAL | NO |
quoteOrderQty|DECIMAL|NO|
price | DECIMAL | NO |
newClientOrderId | STRING | NO | 客户自定义的唯一订单ID。 如果未发送，则自动生成
stopPrice | DECIMAL | NO | 仅 `STOP_LOSS`, `STOP_LOSS_LIMIT`, `TAKE_PROFIT`, 和`TAKE_PROFIT_LIMIT` 需要此参数。
icebergQty | DECIMAL | NO | 仅使用 `LIMIT`, `STOP_LOSS_LIMIT`, 和 `TAKE_PROFIT_LIMIT` 创建新的 iceberg 订单时需要此参数
newOrderRespType | ENUM | NO | 设置响应JSON。 ACK，RESULT或FULL； “MARKET”和“ LIMIT”订单类型默认为“FULL”，所有其他订单默认为“ACK”。
recvWindow | LONG | NO |赋值不能大于 ```60000```
timestamp | LONG | YES |

基于订单 `type`不同，强制要求某些参数:

类型 | 强制要求的参数
------------ | ------------
`LIMIT` | `timeInForce`, `quantity`, `price`
`MARKET` | `quantity` or `quoteOrderQty`
`STOP_LOSS` | `quantity`, `stopPrice`
`STOP_LOSS_LIMIT` | `timeInForce`, `quantity`,  `price`, `stopPrice`
`TAKE_PROFIT` | `quantity`, `stopPrice`
`TAKE_PROFIT_LIMIT` | `timeInForce`, `quantity`, `price`, `stopPrice`
`LIMIT_MAKER` | `quantity`, `price`

其他信息:

* `LIMIT_MAKER`是`LIMIT`订单，如果它们立即匹配并成为吃单方将被拒绝。
* 当触发`stopPrice`时，`STOP_LOSS`和`TAKE_PROFIT`将执行`MARKET`订单。
* 任何`LIMIT`或`LIMIT_MAKER`类型的订单都可以通过发送`icebergQty`而成为`iceberg`订单。
* 任何带有`icebergQty`的订单都必须将`timeInForce`设置为`GTC`。
* 使用 `quantity` 的市价单 `MARKET` 明确的是用户想用市价单买入或卖出的数量。
* 使用 `quoteOrderQty` 的市价单`MARKET` 明确的是通过买入(或卖出)想要花费(或获取)的报价资产数量; 此时的正确报单数量将会以市场流动性和`quoteOrderQty`被计算出来。
* `MARKET` orders using `quoteOrderQty` will not break `LOT_SIZE` filter rules; the order will execute a `quantity` that will have the notional value as close as possible to `quoteOrderQty`.
* 使用 `quoteOrderQty` 的市价单`MARKET`不会突破`LOT_SIZE`的限制规则; 报单会按给定的`quoteOrderQty`尽可能接近地被执行。
* 除非之前的订单已经成交, 不然设置了相同的`newClientOrderId`订单会被拒绝。


MARKET版本和LIMIT版本针对市场价格触发订单价格规则：

* 价格高于市价：`止损``买入`，`获利``卖出`    
* 价格低于市价：`止损``卖出`，`获利``买入`


关于 newOrderRespType的三种选择

* **Response ACK:** 返回速度最快，不包含成交信息，信息量最少
* **Response RESULT:**返回速度居中，返回吃单成交的少量信息
* **Response FULL:** 返回速度最慢，返回吃单成交的详细信息




## 撤销订单 (TRADE)


> **响应**

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

取消有效订单。

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
orderId | LONG | NO |
origClientOrderId | STRING | NO |
newClientOrderId | STRING | NO |  用户自定义的本次撤销操作的ID(注意不是被撤销的订单的自定义ID)。如无指定会自动赋值。
recvWindow | LONG | NO | 赋值不得大于 ```60000```
timestamp | LONG | YES |

`orderId` 或 `origClientOrderId` 必须至少发送一个


## 撤销单一交易对的所有挂单 (TRADE)

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

撤销单一交易对下所有挂单, 包括OCO的挂单。

**Weight:**
1

**Parameters**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
recvWindow | LONG | NO | 不能大于 ```60000```
timestamp | LONG | YES |


## 查询订单 (USER_DATA)


> **响应**

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

查询订单状态。

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
orderId | LONG | NO |
origClientOrderId | STRING | NO |
recvWindow | LONG | NO | 赋值不得大于 ```60000``` 
timestamp | LONG | YES |

注意:

* 至少需要发送 `orderId` 与 `origClientOrderId`中的一个
* 某些订单中`cummulativeQuoteQty`<0，是由于这些订单是cummulativeQuoteQty功能上线之前的订单。




## 当前挂单 (USER_DATA)


> **响应**

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

获取交易对的所有当前挂单， 请小心使用不带交易对参数的调用。

**权重:**
1 单一交易对;   
2交易对参数缺失

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
symbol | STRING | NO |
recvWindow | LONG | NO | 赋值不得大于 ```60000```
timestamp | LONG | YES |

* 不带symbol参数，会返回所有交易对的挂单



## 查询所有订单 (USER_DATA)


> **响应**

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

获取所有帐户订单； 有效，已取消或已完成。

**权重:**
5 带有symbol

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
orderId | LONG | NO |
startTime | LONG | NO |
endTime | LONG | NO |
limit | INT | NO | 默认 500; 最大 1000.
recvWindow | LONG | NO | 赋值不得大于 ```60000``` 
timestamp | LONG | YES |

**注意:**

* 如设置 `orderId` , 订单量将 >=  `orderId`。否则将返回最新订单。
* 一些历史订单 `cummulativeQuoteQty`  < 0, 是指数据此时不存在。




## OCO下单(TRADE)



> **响应**

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

**权重**: 1

发送新 OCO 订单

**参数**:

名称 |类型| 是否必需 | 描述
-----|-----|----------| -----------
symbol|STRING| YES|
listClientOrderId|STRING|NO| 整个orderList的唯一ID
side|ENUM|YES|
quantity|DECIMAL|YES|
limitClientOrderId|STRING|NO| 限价单的唯一ID
price|DECIMAL|YES|
limitIcebergQty|DECIMAL|NO|
stopClientOrderId |STRING|NO| 止损/止损限价单的唯一ID
stopPrice |DECIMAL| YES
stopLimitPrice|DECIMAL|NO| 如果提供，须配合提交`stopLimitTimeInForce`
stopIcebergQty|DECIMAL|NO|
stopLimitTimeInForce|ENUM|NO| 有效值 `GTC`/`FOK`/`IOC`
newOrderRespType|ENUM|NO| Set the response JSON.
recvWindow|LONG|NO| 不能大于 `60000`
timestamp|LONG|YES|


其他信息:

* 价格限制:
    * `SELL`: 限价 > 最新成交价 >触发价
    * `BUY`: 限价 < 最新成交价 < 触发价
* 数量限制:
    * 两个 legs 必须具有同样的数量。
    * `ICEBERG`数量不必相同




## 取消 OCO 订单(TRADE)

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

**权重**: 1

取消整个订单列表。

**参数**

名称| 类型| 是否必需| 描述
----| ----|------|------
symbol| STRING| YES|
orderListId|LONG|NO| `orderListId` 或 `listClientOrderId` 必须被提供
listClientOrderId|STRING|NO| `orderListId` 或 `listClientOrderId` 必须被提供
newClientOrderId|STRING|NO| 用户自定义的本次撤销操作的ID(注意不是被撤销的订单的自定义ID)。如无指定会自动赋值。
recvWindow|LONG|NO|不能大于 `60000`
timestamp|LONG|YES|

其他注意点:

* 取消单个 leg 将取消整个 OCO 订单。


## 查询 OCO (USER_DATA)


> **响应**

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

**权重**: 1

根据提供的可选参数检索特定的OCO。

**参数**:

名称| 类型|是否必需| 描述
----|-----|----|----------
orderListId|LONG|NO|   ```orderListId``` 或 ```listClientOrderId``` 必须提供一个。
origClientOrderId|STRING|NO|  ```orderListId``` 或 ```listClientOrderId``` 必须提供一个。
recvWindow|LONG|NO| 赋值不得大于 ```60000```
timestamp|LONG|YES|




## 查询所有 OCO (USER_DATA)



> **响应**

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

**权重**: 10

根据提供的可选参数检索所有的OCO。

**参数**

名称|类型| 是否必需| 描述
----|----|----|---------
fromId|LONG|NO| 提供该项后, `startTime` 和 `endTime` 都不可提供
startTime|LONG|NO|
endTime|LONG|NO|
limit|INT|NO| 默认值: 500; 最大值: 1000
recvWindow|LONG|NO| 赋值不能超过 `60000`
timestamp|LONG|YES|



## 查询 OCO 挂单 (USER_DATA)



> **响应**

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

权重: 2

**参数**

名称| 类型|是否必需| 描述
----|-----|---|------------------
recvWindow|LONG|NO| 赋值不能大于 ```60000```
timestamp|LONG|YES|







## 账户信息 (USER_DATA)


> **响应**

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
  ],
  "permissions": [
    "SPOT"
  ]
}
```

``
GET /api/v3/account (HMAC SHA256)
``

获取当前账户信息。

**权重:**
5

**参数:**

名称 | 类型 | 是否必需| 描述
------------ | ------------ | ------------ | ------------
recvWindow | LONG | NO | 赋值不能大于 ```60000```
timestamp | LONG | YES |



## 账户成交历史 (USER_DATA)


> **响应**

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

获取账户指定交易对的成交历史

**权重:**
5 

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
startTime | LONG | NO |
endTime | LONG | NO |
fromId | LONG | NO | 起始Trade id。 默认获取最新交易。
limit | INT | NO | 默认 500; 最大 1000.
recvWindow | LONG | NO | 赋值不能超过 ```60000```
timestamp | LONG | YES |

**注意:**

* 如果设定 `fromId` , 获取订单 >= `fromId`.
否则返回最近订单。





# 杠杆账户和交易接口


## 杠杆账户划转 (MARGIN)

> **响应**

```javascript
{
    //transaction id
    "tranId": 100000001
}
```


``
POST /sapi/v1/margin/transfer (HMAC SHA256)
``
执行现货账户与杠杆账户之间的划转

**权重:**
1

**参数:**

名称 | 类型| 是否必需 | 描述
------------ | ------------ | ------------ | ------------
asset | STRING | YES | 被划转的资产, 比如, BTC
amount | DECIMAL | YES | 划转数量
type | INT | YES | 1: 主账户向杠杆账户划转 2: 杠杆账户向主账户划转
recvWindow | LONG | NO  | 赋值不能大于 ```60000```
timestamp | LONG | YES




## 杠杆账户借贷 (MARGIN)

> **响应**

```javascript
{
    //transaction id
    "tranId": 100000001
}
```


``
POST /sapi/v1/margin/loan (HMAC SHA256)
``
申请借贷。

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
asset | STRING | YES | 
amount | DECIMAL | YES | 
recvWindow | LONG | NO | 赋值不能超过 ```60000```
timestamp | LONG | YES



## 杠杆账户归还借贷 (MARGIN)

> **响应**

```javascript
{
    //transaction id
    "tranId": 100000001
}
```


``
POST /sapi/v1/margin/repay (HMAC SHA256)
``
获取杠杆账户归还借贷。

**权重:**
1

**参数:**

名称 | 类型| 是否必需 | 描述
------------ | ------------ | ------------ | ------------
asset | STRING | YES | 
amount | DECIMAL | YES | 
recvWindow | LONG | NO | 赋值不能超过 ```60000```
timestamp | LONG | YES






## 查询杠杆资产 (MARKET_DATA)

> **响应**

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

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
asset | STRING | YES |

* 需要提供`X-MBX-APIY` 


## 查询杠杆交易对 (MARKET_DATA)

> **响应**

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

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |

* 需要提供`X-MBX-APIY`


## 获取所有杠杆资产信息 (MARKET_DATA)


> **响应**

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

**权重:**
1

**参数:**    
None

* 必须提供`X-MBX-APIKEY` 


## 获取所有杠杆交易对(MARKET_DATA)

> **响应**

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

**权重:**
1

**参数:**

None


* 必须提供`X-MBX-APIKEY` 




## 查询杠杆价格指数 (MARKET_DATA)

> **响应**

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

**权重:**
1

**参数:**

名称 | 类型 | 是否必需| 描述
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |



* 需要提供`X-MBX-APIKEY` 。






## 杠杆账户下单 (TRADE)

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
  "price": "0.00000000",
  "origQty": "10.00000000",
  "executedQty": "10.00000000",
  "cummulativeQuoteQty": "10.00000000",
  "status": "FILLED",
  "timeInForce": "GTC",
  "type": "MARKET",
  "side": "SELL",
  "marginBuyBorrowAmount": 5,       // 下单后没有发生借款则不返回该字段
  "marginBuyBorrowAsset": "BTC",    // 下单后没有发生借款则不返回该字段
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

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
side |	ENUM | YES | BUY<br>SELL
type | ENUM | YES |
quantity | DECIMAL | YES |	
price |	DECIMAL | NO	|
stopPrice | DECIMAL | NO | 与`STOP_LOSS`, `STOP_LOSS_LIMIT`, `TAKE_PROFIT`, 和 `TAKE_PROFIT_LIMIT` 订单一起使用.
newClientOrderId | STRING | NO | 客户自定义的唯一订单ID。若未发送自动生成。
icebergQty | DECIMAL | NO | 与 `LIMIT`, `STOP_LOSS_LIMIT`, 和 `TAKE_PROFIT_LIMIT` 一起使用创建 iceberg 订单.
newOrderRespType | ENUM | NO | 设置响应: JSON. ACK, RESULT, 或 FULL; MARKET 和 LIMIT 订单类型默认为 FULL, 所有其他订单默认为 ACK.
sideEffectType | ENUM | NO | NO_SIDE_EFFECT, MARGIN_BUY, AUTO_REPAY;默认为 NO_SIDE_EFFECT.
timeInForce | ENUM | NO | GTC,IOC,FOK
recvWindow | LONG | NO | 赋值不能大于 ```60000```
timestamp | LONG | YES |



## 杠杆账户撤销订单 (TRADE)

> **响应**

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
杠杆账户撤销有效订单。

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
orderId | LONG | NO | 
origClientOrderId |	STRING | NO	
newClientOrderId |	STRING | NO | 用于唯一识别此撤销订单，默认自动生成。
recvWindow | LONG | NO | T赋值不能大于 ```60000```
timestamp | LONG | YES

* 必须发送 orderId 或 origClientOrderId 其中一个。




## 获取划转历史 (USER_DATA)

> **响应**

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

**权重:**
1

**参数:**

名称 |类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
asset |	STRING | NO
type | STRING | NO | 划转类型: ROLL_IN, ROLL_OUT
startTime |	LONG |	NO	
endTime | LONG | NO	
current | LONG | NO | 当前查询页。 从 1开始。 默认:1
size |	LONG | NO |	默认:10 最大:100
recvWindow | LONG | NO | 赋值不能大于 ```60000```
timestamp | LONG | YES

* 响应返回为降序排列。



## 查询借贷记录 (USER_DATA)

> **响应**

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


**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
asset |	STRING | YES	
txId | LONG | NO | `tranId` in POST /sapi/v1/margin/loan
startTime |	LONG |	NO	
endTime | LONG | NO	
current | LONG | NO | 当前查询页。 开始值 1。 默认:1
size |	LONG | NO |	默认:10 最大:100
recvWindow | LONG | NO | 赋值不能大于 ```60000```
timestamp | LONG | YES

* 必须发送`txId` 或 `startTime`，`txId` 优先。
* 响应返回为降序排列。


## 查询还贷记录 (USER_DATA)


> **响应**

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

**权重**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
asset | STRING |	YES	
txId | LONG | NO | 返回 /sapi/v1/margin/repay 
startTime | LONG | NO	
endTime | LONG | NO	
current | LONG | NO	| 当前查询页。开始值 1. 默认:1
size | LONG | NO | 默认:10 最大:100
recvWindow | LONG | NO | 赋值不能大于 ```60000```
timestamp | LONG | YES

* 必须发送`txId` 或 `startTime`，`txId` 优先。
* 响应返回为降序排列。




## 获取利息历史 (USER_DATA)

> **响应**

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


**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
asset |	STRING | No
startTime |	LONG |	NO	
endTime | LONG | NO	
current | LONG | NO | 当前查询页。 开始值 1. 默认:1
size |	LONG | NO |	默认:10 最大:100
recvWindow | LONG | NO | 赋值不能大于 ```60000```
timestamp | LONG | YES

* 响应返回为降序排列。



## 获取账户强制平仓记录(USER_DATA)


> **响应**

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


**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
startTime |	LONG |	NO	
endTime | LONG | NO	
current | LONG | NO | 当前查询页。 开始值 1. 默认:1
size |	LONG | NO |	默认:10 最大:100
recvWindow | LONG | NO |  赋值不能大于 ```60000```
timestamp | LONG | YES

* 响应返回为降序排列。






## 查询杠杆账户详情 (USER_DATA)


> **响应**

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


**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
recvWindow | LONG | NO | 赋值不能大于 ```60000```
timestamp | LONG | YES |




## 查询杠杆账户订单 (USER_DATA)


> **响应**

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
GET /sapi/v1/margin/order  (HMAC SHA256)
``

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
orderId | STRING | NO |	
origClientOrderId | STRING | NO	|
recvWindow | LONG | NO | 赋值不能大于 ```60000```
timestamp | LONG | YES

* 必须发送 orderId 或 origClientOrderId 其中一个。
* 一些历史订单的 cummulativeQuoteQty  < 0, 是指当前数据不存在。



## 查询杠杆账户挂单记录 (USER_DATA)


> **响应**

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


**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
symbol | STRING | NO |
recvWindow | LONG | NO | 赋值不能大于 ```60000```
timestamp | LONG | YES

* 如未发送symbol，返回所有 symbols 订单记录。
* 当返回所有symbols时，针对限速器计数的请求数量等于当前在交易所交易的symbols数量。




## 查询杠杆账户的所有订单 (USER_DATA)

> **响应**

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


**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
orderId | LONG | NO	
startTime |	LONG | NO	
endTime | LONG | NO	
limit |	INT | NO | 默认 500;最大 1000.
recvWindow | LONG | NO | 赋值不能大于 ```60000```
timestamp | LONG | YES

* 如果设置 orderId , 获取订单 >= orderId， 否则返回近期订单历史。
* 一些历史订单的 cummulativeQuoteQty  < 0, 是指当前数据不存在。



## 查询杠杆账户交易历史 (USER_DATA)


> **响应**

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
	},
	{
		"commission": "0.00002950",
		"commissionAsset": "BTC",
		"id": 32,
		"isBestMatch": true,
		"isBuyer": false,
		"isMaker": true,
		"orderId": 39319,
		"price": "0.00590000",
		"qty": "5.00000000",
		"symbol": "BNBBTC",
		"time": 1561964645345
	}
]
```

``
GET  /sapi/v1/margin/myTrades (HMAC SHA256)
``

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
startTime |	LONG | NO	
endTime | LONG | NO	
fromId | LONG | NO | 获取TradeId，默认获取近期交易历史。
limit |	INT | NO | 默认 500; 最大 1000.
recvWindow | LONG | NO | 默认值不能大于 ```60000```
timestamp | LONG | YES


* 如果设置 fromId , 获取订单 id >= fromId， 否则返回近期订单历史。



## 查询账户最大可借贷额(USER_DATA)


> **响应**

```javascript
{
    "amount": "1.69248805"
}
```

``
GET /sapi/v1/margin/maxBorrowable (HMAC SHA256)
``

**权重:**
5

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
asset | STRING | YES |
recvWindow | LONG | NO | 默认值不能大于 ```60000```
timestamp | LONG | YES



## 查询最大可转出额 (USER_DATA)


> **响应**

```javascript
 {
      "amount": "3.59498107"
 }
```

``
GET /sapi/v1/margin/maxTransferable (HMAC SHA256)
``


**权重:**
5

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
asset | STRING | YES |
recvWindow | LONG | NO | 默认值不能大于 ```60000```
timestamp | LONG | YES







# Websocket账户信息推送


* 本篇所列出API接口的base url : **https://api.binance.com**
* 用于订阅账户数据的 `listenKey` 从创建时刻起有效期为60分钟
* 可以通过 `PUT` 一个 `listenKey` 延长60分钟有效期
* 可以通过`DELETE`一个 `listenKey` 立即关闭当前数据流，并使该`listenKey` 无效
* 在具有有效`listenKey`的帐户上执行`POST`将返回当前有效的`listenKey`并将其有效期延长60分钟
* websocket接口的baseurl: **wss://stream.binance.com:9443**
* U订阅账户数据流的stream名称为 **/ws/\<listenKey\>** 或 **/stream?streams=\<listenKey\>**
* 每个链接有效期不超过24小时，请妥善处理断线重连。
* 账户数据流的消息不保证严格时间序; **请使用 E 字段进行排序**


## Listen Key（现货账户）

### 生成 Listen Key (USER_STREAM)

> **响应**

```javascript
{
  "listenKey": "pqia91ma19a5s61cv6a81va65sdf19v8a65a1a5s61cv6a81va65sdf19v8a65a1"
}
```


``
POST /api/v3/userDataStream
``

开始一个新的数据流。除非发送 keepalive，否则数据流于60分钟后关闭。如果该帐户具有有效的`listenKey`，则将返回该`listenKey`并将其有效期延长60分钟。
**权重:**
1

**参数:**

NONE




### 延长 Listen Key 有效期 (USER_STREAM)

> **响应**

```javascript
{}
```

``
PUT /api/v3/userDataStream
``

有效期延长至本次调用后60分钟,建议每30分钟发送一个 ping 。

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
listenKey | STRING | YES


### 关闭 Listen Key (USER_STREAM)

> **响应**

```javascript
{}
```

``
DELETE /api/v3/userDataStream
``

关闭用户数据流。

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
listenKey | STRING | YES




## Listen Key（杠杆账户）

### 生成 Listen Key (USER_STREAM)


> **响应**

```javascript
{"listenKey":  "T3ee22BIYuWqmvne0HNq2A2WsFlEtLhvWCtItw6ffhhdmjifQ2tRbuKkTHhr"}
```


``
POST  /sapi/v1/userDataStream
``


**权重:**
1

**参数:**

NONE



### 延长 Lisen Key 有效期 (USER_STREAM)

> **响应**

```javascript
{}
```

``
PUT  /sapi/v1/userDataStream
``

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
listenKey | STRING | YES |



### 关闭 ListenKey (USER_STREAM)

> **响应**

```javascript
{}
```

``
DELETE  /sapi/v1/userDataStream
``


**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
listenKey | STRING | YES |





## Payload: 账户更新
使用 `outboundAccountInfo` event进行账户更新。

> **Payload**

```javascript
{
  "e": "outboundAccountInfo",   // 事件类型
  "E": 1499405658849,           // 事件时间
  "m": 0,                       // 挂单费率 (基点)
  "t": 0,                       // 吃单费率 (基点)
  "b": 0,                       // 买单费率(基点)
  "s": 0,                       // 卖单费率(基点)
  "T": true,                    // 是否允许交易
  "W": true,                    // 是否允许提现
  "D": true,                    // 是否允许充值
  "u": 1499405658848,           // 账户末次更新时间戳
  "B": [                        // 余额
    {
      "a": "LTC",               // 资产名称
      "f": "17366.18538083",    // 可用余额
      "l": "0.00000000"         // 冻结余额
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
  ],
  "P": [                        // 权限
    "SPOT"
  ]
}
```


每当帐户余额发生更改时，都会发送一个附加事件`outboundAccountPosition`，其中包含可能由生成余额变动的事件而变动的资产。

> **Payload**

```javascript
{
  "e": "outboundAccountPosition", // 事件类型
  "E": 1564034571105,             // 事件时间
  "u": 1564034571073,             // 账户末次更新时间戳
  "B": [                          // 余额
    {
      "a": "ETH",                 // 资产名称
      "f": "10000.000000",        // 可用余额
      "l": "0.000000"             // 冻结余额
    }
  ]
}
```


## Payload: 余额更新


> **Payload**

```javascript
{
  "e": "balanceUpdate",         //Event Type
  "E": 1573200697110,           //Event Time
  "a": "ABC",                   //Asset
  "d": "100.00000000",          //Balance Delta
  "T": 1573200697068            //Clear Time
}
```

当下列情形发生时更新:

* 账户发生充值或提取
* 交易账户之间发生划转（例如 现货向杠杆账户划转）




## Payload: 订单更新
订单通过`executionReport`事件进行更新。 

请查阅文档[公开API参数](#public-api-definitions)以及以下文档，以获取相关的枚举定义。

通过将`Z`除以`z`可以找到平均价格。

> **Payload**

```javascript
{
  "e": "executionReport",        // 事件类型
  "E": 1499405658658,            // 事件时间
  "s": "ETHBTC",                 // 交易对
  "c": "mUvoqJxFIILMdfAW5iGSOW", // clientOrderId
  "S": "BUY",                    // 订单方向
  "o": "LIMIT",                  // 订单类型
  "f": "GTC",                    // 有效方式
  "q": "1.00000000",             // 订单原始数量
  "p": "0.10264410",             // 订单原始价格
  "P": "0.00000000",             // 止盈止损单触发价格
  "F": "0.00000000",             // 冰山订单数量
  "g": -1,                       // OCO订单 OrderListId
  "C": null,                     // 原始订单自定义ID(原始订单，指撤单操作的对象。撤单本身被视为另一个订单)
  "x": "NEW",                    // 本次事件的具体执行类型
  "X": "NEW",                    // 订单的当前状态
  "r": "NONE",                   // 订单被拒绝的原因
  "i": 4293153,                  // orderId
  "l": "0.00000000",             // 订单末次成交数量
  "z": "0.00000000",             // 订单累计已成交数量
  "L": "0.00000000",             // 订单末次成交价格
  "n": "0",                      // 手续费数量
  "N": null,                     // 手续费资产类别
  "T": 1499405658657,            // 成交时间
  "t": -1,                       // 成交ID
  "I": 8641984,                  // 请忽略
  "w": true,                     // 订单是否在订单簿上？
  "m": false,                    // 该成交是作为挂单成交吗？
  "M": false,                    // 请忽略
  "O": 1499405658657,            // 订单创建时间
  "Z": "0.00000000",             // 订单累计已成交金额
  "Y": "0.00000000",              // 订单末次成交金额
  "Q": "0.00000000"              // Quote Order Qty
}
```



**执行类型:**

* NEW 新订单
* CANCELED 订单被取消
* REPLACED (保留字段，当前未使用)
* REJECTED 新订单被拒绝
* TRADE 订单有新成交
* EXPIRED 订单失效（根据订单的Time In Force参数）


如果订单是OCO，则除了显示“ executionReport”事件外，还将显示一个名为“ ListStatus”的事件。

> **Payload**

```javascript
{
  "e": "listStatus",                // 事件类型
  "E": 1564035303637,               // 事件时间
  "s": "ETHBTC",                    // 交易对
  "g": 2,                           // OrderListId
  "c": "OCO",                       // Contingency Type
  "l": "EXEC_STARTED",              // List Status Type
  "L": "EXECUTING",                 // List Order Status
  "r": "NONE",                      // List 被拒绝的原因
  "C": "F4QN4G8DlFATFlIUQ0cjdD",    // List Client Order ID
  "T": 1564035303625,               // 成交时间
  "O": [                           
    {
      "s": "ETHBTC",                // 交易对
      "i": 17,                      // orderId
      "c": "AJYsMjErWJesZvqlJCTUgL" // clientOrderId
    },
    {
      "s": "ETHBTC",
      "i": 18,
      "c": "bfYPSQdLoqAJeNrOr9adzq"
    }
  ]
}
```





# 币安宝接口

* 这些接口用于币安宝产品。更多细节, 请参考[币安宝](https://www.binance.com/cn/lending)页面。

## 获取活期产品列表 (USER_DATA)

> **响应:**

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


**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
status | ENUM | NO | "ALL", "SUBSCRIBABLE", "UNSUBSCRIBABLE"; default "ALL"	
featured | STRING | NO | “ALL”, "true"; default "ALL"
recvWindow | LONG | NO | 不能大于 ```60000```
timestamp | LONG | YES



## 获取用户当日剩余活期可申购余额 (USER_DATA)

> **响应:**

```javascript
{
	"asset": "BUSD", 
	"leftQuota": "50000.00000000"
}
```

``
GET /sapi/v1/lending/daily/userLeftQuota (HMAC SHA256)
``


**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
productId | STRING | YES | 
recvWindow | LONG | NO | 不能大于 ```60000```
timestamp | LONG | YES



## 申购活期产品 (USER_DATA)

> **响应:**

```javascript
{
	"purchaseId": 40607
}
```

``
POST /sapi/v1/lending/daily/purchase (HMAC SHA256)
``


**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
productId | STRING | YES | 
amount | DECIMAL | YES
recvWindow | LONG | NO | 不能大于 ```60000```
timestamp | LONG | YES




## 获取用户当日活期可赎回余额 (USER_DATA)

> **响应:**

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

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
productId | STRING | YES | 
type | ENUM | YES | "FAST", “NORMAL“
recvWindow | LONG | NO | 不能大于 ```60000```
timestamp | LONG | YES



## 赎回活期产品 (USER_DATA)

> **响应:**

```javascript
{}
```

``
POST /sapi/v1/lending/daily/redeem (HMAC SHA256)
``

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
productId | STRING | YES | 
amount | DECIMAL | YES |
type | ENUM | YES | "FAST", “NORMAL“
recvWindow | LONG | NO | 不能大于 ```60000```
timestamp | LONG | YES




## 用户活期产品持仓 (USER_DATA)

> **响应:**

```javascript
[
	{
		"annualInterestRate": "0.02600000",
	  	"asset": "USDT",
	  	"avgAnnualInterestRate": "0.02599895",
	  	"canRedeem": true,
	  	"dailyInterestRate": "0.00007123",
	  	"freeAmount": "75.46000000",
	  	"freezeAmount": "0.00000000", // 弃用
	  	"lockedAmount": "0.00000000", // 弃用
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

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
asset | STRING | YES | 
recvWindow | LONG | NO | 不能大于 ```60000```
timestamp | LONG | YES



## 查询定期/灵活定期产品列表 (USER_DATA)

> **响应:**

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

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
asset | STRING | NO |
type | ENUM | YES | "REGULAR", "CUSTOMIZED_FIXED"
status | ENUM | NO | 	"ALL", "SUBSCRIBABLE", "UNSUBSCRIBABLE"; 默认 "ALL" 
isSortAsc | BOOLEAN | NO  | 默认 "true"
sortBy| ENUM | NO | "START_TIME", "LOT_SIZE", "INTEREST_RATE", "DURATION"; 默认 "START_TIME"
current | LONG | NO | 分页页码. 默认:1
size | LONG | NO | 单页显示条数，默认:10 最大:100
recvWindow | LONG | NO | 不能大于 ```60000```
timestamp | LONG | YES






## 申购灵活定期产品 (USER_DATA)

> **响应:**

```javascript
{
	purchaseId: "18356"
}
```

``
POST /sapi/v1/lending/customizedFixed/purchase (HMAC SHA256)
``

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
projectId | STRING | YES |
lot | LONG | YES | 申购手数
recvWindow | LONG | NO | 不能大于 ```60000```
timestamp | LONG | YES






## 用户定期/灵活定期持仓 (USER_DATA)

> **响应:**

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

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
asset | STRING | YES | 
projectId | STRING | NO |
status | ENUM | NO | "HOLDING", "REDEEMED"
recvWindow | LONG | NO | 不能大于 ```60000```
timestamp | LONG | YES







## 币安宝账户信息 (USER_DATA)

> **响应:**

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

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
recvWindow | LONG | NO | 不能大于 ```60000```
timestamp | LONG | YES





## 获取申购记录 (USER_DATA)

> **响应:**

> 活期产品

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
> 定期/灵活定期产品

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
  	},
  	{
  		"amount": "100.00000000",
  		"asset": "USDT",
  		"createTime": 1587010770000,
  		"lendingType": "CUSTOMIZED_FIXED",
  		"lot": 1,
  		"productName": "USDT",
  		"purchaseId": 55841,
  		"status": 'SUCCESS'
  	}
]
```

``
GET /sapi/v1/lending/union/purchaseRecord (HMAC SHA256)
``

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
lendingType | ENUM | YES | "DAILY" 表示活期, "REGULAR" 表示定期, "CUSTOMIZED_FIXED" 表示灵活定期
asset | STRING | NO | 
startTime | LONG | NO |
endTime | LONG | NO |
current | LONG | NO | Currently querying page. Start from 1. Default:1
size | LONG | NO | 	Default:10, Max:100
recvWindow | LONG | NO | 不能大于 ```60000```
timestamp | LONG | YES




## 获取赎回记录 (USER_DATA)

> **响应:**

> 活期产品

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

> 定期产品

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

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
lendingType | ENUM | YES | "DAILY" 表示活期, "REGULAR" 表示定期, "CUSTOMIZED_FIXED" 表示灵活定期
asset | STRING | NO | 
startTime | LONG | NO |
endTime | LONG | NO |
current | LONG | NO | Currently querying page. Start from 1. Default:1
size | LONG | NO | 	Default:10, Max:100
recvWindow | LONG | NO | 不能大于 ```60000```
timestamp | LONG | YES



## 获取利息历史 (USER_DATA)

> **响应:**


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

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
lendingType | ENUM | YES | "DAILY" 表示活期, "REGULAR" 表示定期, "CUSTOMIZED_FIXED" 表示灵活定期
asset | STRING | NO | 
startTime | LONG | NO |
endTime | LONG | NO |
current | LONG | NO | Currently querying page. Start from 1. Default:1
size | LONG | NO | 	Default:10, Max:100
recvWindow | LONG | NO | 不能大于 ```60000```
timestamp | LONG | YES







# 矿池接口

* 这些接口作用于币安矿池。更多细节, 参考[币安矿池](https://pool.binance.com/cn)页面。

## 获取算法（MARKET_DATA）

>**响应:**  

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

**权重:**  
1  
**参数:**
无

## 获取币种（MARKET_DATA）

>**响应:**  

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

`GET /sapi/v1/mining/pub/coinList`

**权重:**  
1  
**参数:**
无


## 请求矿工列表明细 (USER_DATA)



>**响应:**

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


**权重:**
5

**参数:**

Name | Type | Mandatory | Description |  例子
------------ | ------------ | ------------ | ------------ | ------------ 
algo | STRING | YES | 算法名称(sha256) | sha256
userName | STRING | YES | 挖矿用户名 | test
workerName | STRING | YES | 矿工用户名，必传 |bhdc1.16A10404B



## 请求矿工列表 (USER_DATA)


>**响应:**

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


**权重:**
5

**参数:**

Name | Type | Mandatory | Description |  例子
------------ | ------------ | ------------ | ------------ | ------------ 
algo | STRING | YES | 算法名称(sha256) | sha256
userName | STRING | YES | 挖矿用户名 | test
pageIndex | INTEGER | NO | 页码，为空默认第一页，从1开始 |
sort | INTEGER | NO | 排序方向(为空默认为0): 0 正序，1 倒序 |
sortColumn | INTEGER | NO | 排序方向(为空默认为0): 0 正序，1 倒序 |
workerStatus | INTEGER | NO | 矿机状态(默认为0)：0 全部，1 有效， 2 无效， 3 失效 |



## 收益列表 (USER_DATA)


>**响应:**

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


**权重:**
5

**参数:**

Name | Type | Mandatory | Description |  例子
------------ | ------------ | ------------ | ------------ | ------------ 
algo | STRING | YES | 算法名称(sha256) | sha256
userName | STRING | YES | 挖矿用户名 | test
coin | STRING | NO | 币种名称 |
startDate | Long | NO | 搜索日期 毫秒时间戳，同时为空查询所有 |
endDate | Long | NO | 搜索日期 毫秒时间戳，同时为空查询所有 |
pageIndex | INTEGER | NO | 页码，为空默认第一页，从1开始 |




## 统计列表 (USER_DATA)


>**响应:**

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


**权重:**
5

**参数:**

Name | Type | Mandatory | Description |  例子
------------ | ------------ | ------------ | ------------ | ------------ 
algo | STRING | YES | 算法名称(sha256) | sha256
userName | STRING | YES | 挖矿用户名 | test



## 账号列表 (USER_DATA)



>**响应:**

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


**权重:**
5

**参数:**

Name | Type | Mandatory | Description |  例子
------------ | ------------ | ------------ | ------------ | ------------ 
algo | STRING | YES | 算法名称(sha256) | sha256
userName | STRING | YES | 挖矿用户名 | test





# 错误代码

> 错误JSON格式:

```javascript
{
  "code":-1121,
  "msg":"Invalid symbol."
}
```

错误由两部分组成：错误代码和消息。 代码是通用的，但是消息可能会有所不同。 




## 10xx -常规服务器或网络问题
### -1000 UNKNOWN
 *处理请求时发生未知错误。

### -1001 DISCONNECTED
 * 内部错误; 无法处理您的请求。 请再试一次.

### -1002 UNAUTHORIZED
 * 您无权执行此请求。

### -1003 TOO_MANY_REQUESTS
 * 排队的请求过多。
 * 请求权重过多； 请使用websocket获取实时更新。
 * 请求权重过多； 当前限制为每分钟％s请求权重。 请使用websocket进行实时更新，以避免轮询API。
 * 请求权重过多； IP被禁止，直到％s。 请使用websocket进行实时更新，以免被禁。

### -1006 UNEXPECTED_RESP
 * 从消息总线收到意外的响应。 执行状态未知。

### -1007 TIMEOUT
 * 等待后端服务器响应超时。 发送状态未知； 执行状态未知。

### -1014 UNKNOWN_ORDER_COMPOSITION
 * 不支持的订单组合。

### -1015 TOO_MANY_ORDERS
 * 新订单太多。
 * 新订单太多； 当前限制为每％s ％s个订单。

### -1016 SERVICE_SHUTTING_DOWN
 * 该服务不可用。

### -1020 UNSUPPORTED_OPERATION
 * 不支持此操作。

### -1021 INVALID_TIMESTAMP
 * 此请求的时间戳在recvWindow之外。
 * 此请求的时间戳比服务器时间提前1000毫秒。

### -1022 INVALID_SIGNATURE
 * 此请求的签名无效。

### -1099 Not found, authenticated, or authorized
 * 替换错误代码-1999


## 11xx - 2xxx Request issues
### -1100 ILLEGAL_CHARS
 * 在参数中发现非法字符。
 * 在参数`％s`中发现非法字符； 合法范围是`％s`。

### -1101 TOO_MANY_PARAMETERS
 * 为此端点发送的参数太多。
 * 参数太多； 预期为`％s`并收到了`％s`。
 * 检测到的参数值重复。

### -1102 MANDATORY_PARAM_EMPTY_OR_MALFORMED
 * 未发送强制性参数，该参数为空/空或格式错误。
 * 强制参数`％s`未发送，为空/空或格式错误。
 * 必须发送参数`％s`或`％s`，但两者均为空！

### -1103 UNKNOWN_PARAM
 * 发送了未知参数。

### -1104 UNREAD_PARAMETERS
 * 并非所有发送的参数都被读取。
 * 并非所有发送的参数都被读取； 读取了`％s`参数，但被发送了`％s`。

### -1105 PARAM_EMPTY
 * 参数为空。
 * 参数`％s`为空。

### -1106 PARAM_NOT_REQUIRED
 * 不需要时已发送参数。
 * 不需要时发送参数`％s`。

### -1111 BAD_PRECISION
 * 精度超过为此资产定义的最大值。

### -1112 NO_DEPTH
 * 交易对没有挂单。

### -1114 TIF_NOT_REQUIRED
 * 不需要时发送了TimeInForce参数。

### -1115 INVALID_TIF
 * 无效 timeInForce.

### -1116 INVALID_ORDER_TYPE
 * 无效订单类型。

### -1117 INVALID_SIDE
 * 无效买卖方向。

### -1118 EMPTY_NEW_CL_ORD_ID
 * 新的客户订单ID为空。

### -1119 EMPTY_ORG_CL_ORD_ID
 * 客户自定义的订单ID为空。

### -1120 BAD_INTERVAL
 * 无效时间间隔。

### -1121 BAD_SYMBOL
 * 无效的交易对。

### -1125 INVALID_LISTEN_KEY
 * 该listenKey不存在。

### -1127 MORE_THAN_XX_HOURS
 * 查询间隔太大。
 * 从开始时间到结束时间之间超过％s小时。

### -1128 OPTIONAL_PARAMS_BAD_COMBO
 * 可选参数组合无效。

### -1130 INVALID_PARAMETER
 * 发送的参数为无效数据。
 * 发送参数`％s`的数据无效。

### -1131 BAD_RECV_WINDOW
 * `recvWindow` 必须小于 60000

### -2010 NEW_ORDER_REJECTED
 * 新订单被拒绝

### -2011 CANCEL_REJECTED
 * 取消订单被拒绝

### -2013 NO_SUCH_ORDER
 * 订单不存在。

### -2014 BAD_API_KEY_FMT
 * API-key 格式无效。

### -2015 REJECTED_MBX_KEY
 * 无效的API密钥，IP或操作权限。

### -2016 NO_TRADING_WINDOW
 * 找不到该交易对的交易窗口。 尝试改为24小时自动报价。


## 3xxx-4xxx SAPI 具体问题

### -3021 PAIR_ADMIN_BAN_TRADE
 * 杠杆账户无法交易此交易对。

### -3022 ACCOUNT_BAN_TRADE
 * 账号被禁止交易。

### -3023 WARNING_MARGIN_LEVEL
 * 无法在当前杠杆倍数下转出资金或者下单

### -3024 FEW_LIABILITY_LEFT
 *  付款之后未付款的债务太小

### -3025 INVALID_EFFECTIVE_TIME
 * 输入时间有误。

### -3026 VALIDATION_FAILED
 * 输入参数有误。

### -3027 NOT_VALID_MARGIN_ASSET
 * 无效的杠杆资产。

### -3028 NOT_VALID_MARGIN_PAIR
 * 无效的杠杆交易对。

### -3029 TRANSFER_FAILED
 * 转账失败。

### -3036 ACCOUNT_BAN_REPAY
 * 此账号无法还款。

### -3037 PNL_CLEARING
 *  `PNL`正在清帐，请稍等。

### -3038 LISTEN_KEY_NOT_FOUND
 * 找不到`Listen key`

### -3042 PRICE_INDEX_NOT_FOUND
 * 该杠杆交易对无可用价格指数。

### -3999 NOT_WHITELIST_USER
 * 此功能只面向邀请的用户。

### -4001 CAPITAL_INVALID
 * 非法操作

### -4002 CAPITAL_IG
 * 非法获取

### -4003 CAPITAL_IEV
 * 非法邮箱验证

### -4004 CAPITAL_UA
 * 未登录或者认证。

### -4005 CAPAITAL_TOO_MANY_REQUEST
 * 请求太频繁。

### -4006 CAPITAL_ONLY_SUPPORT_PRIMARY_ACCOUNT
 * 只支持主账号。

### -4007 CAPITAL_ADDRESS_VERIFICATION_NOT_PASS
 * 地址的没有通过校验。

### -4008 CAPITAL_ADDRESS_TAG_VERIFICATION_NOT_PASS
 * 地址的标记信息(`tag`)没有通过校验。


## 6XXX - 币安宝相关

### -6001 DAILY_PRODUCT_NOT_EXIST
 * 理财产品不存在.

### -6003 DAILY_PRODUCT_NOT_ACCESSIBLE
 * 产品不存在或者没有权限。

### -6004 DAILY_PRODUCT_NOT_PURCHASABLE
 * 产品无法购买。

### -6005 DAILY_LOWER_THAN_MIN_PURCHASE_LIMIT
 * 低于可以购买的最小限额。

### -6006 DAILY_REDEEM_AMOUNT_ERROR
 * 赎回额度有误。

### -6007 DAILY_REDEEM_TIME_ERROR
 * 不在赎回的时间内。

### -6008 DAILY_PRODUCT_NOT_REDEEMABLE
 * 产品暂时无法赎回。

### -6009 REQUEST_FREQUENCY_TOO_HIGH
 * 发送请求太频繁。

### -6011 EXCEEDED_USER_PURCHASE_LIMIT
 * 超购每个月用户可以申购的最大次数。

### -6012 BALANCE_NOT_ENOUGH
 * 余额不足。

### -6013 PURCHASING_FAILED
 * 申购失败。

### -6014 UPDATE_FAILED
 * 超过可以申购的最大上限。

### -6015 EMPTY_REQUEST_BODY
 * 请求的`body`为空。

### -6016 PARAMS_ERR
 * 请求的参数有误。

### -6017 NOT_IN_WHITELIST
 * 不在白名单里面。

### -6018 ASSET_NOT_ENOUGH
 * 资产不足。

### -6019 PENDING
 * 需要进一步确认。



## -9xxx 过滤器故障
报错信息 | 描述
------------ | ------------
"Filter failure: PRICE_FILTER" | “价格”过高，过低和/或不遵循交易对的最小价格规则。
"Filter failure: PERCENT_PRICE" | “价格”比最近Y分钟的平均加权价格高X％或X％太低。
"Filter failure: LOT_SIZE" | “数量”太高，太低和/或不遵循该交易对的步长规则。
"Filter failure: MIN_NOTIONAL" | 价格*数量太低，无法成为该交易对的有效订单。
"Filter failure: ICEBERG_PARTS" | `ICEBERG` 订单会分成太多部分； icebergQty太小。
"Filter failure: MARKET_LOT_SIZE" | “ MARKET”订单的“数量”过高，过低和/或未遵循交易对的步长规则。
"Filter failure: MAX_NUM_ORDERS" | 客户在交易对上有太多挂单。
"Filter failure: MAX_ALGO_ORDERS" | 账户有太多未平仓止损和/或在交易对上执行获利指令。
"Filter failure: MAX_NUM_ICEBERG_ORDERS" | 客户在交易对上有太多 iceberg 挂单。
"Filter failure: EXCHANGE_MAX_NUM_ORDERS" | 帐户上的交易所有太多挂单。
"Filter failure: EXCHANGE_MAX_ALGO_ORDERS" | 帐户有太多止损挂单和/或在交易所收取获利指令。



## 订单拒绝错误

以下错误代码表示撮合引擎返回的订单相关错误:

* `1010_ERROR_MSG_RECEIVED`
* `-2010 NEW_ORDER_REJECTED`
* `2011 CANCEL_REJECTED`

结合以下消息将指示特定的错误：


Error message | Description
------------ | ------------
"Unknown order sent." | 找不到订单（通过“ orderId”，“ clientOrderId”，“ origClientOrderId”）
"Duplicate order sent." | `clientOrderId`已经被使用
"Market is closed." | 该交易对不在交易范围
"Account has insufficient balance for requested action." | 没有足够的资金来完成行动
"Market orders are not supported for this symbol." | 交易对上未启用“ MARKET”
"Iceberg orders are not supported for this symbol." | 交易对上未启用`icebergQty` 
"Stop loss orders are not supported for this symbol." | 交易对上未启用 `STOP_LOSS` 
"Stop loss limit orders are not supported for this symbol." | 交易对上未启`STOP_LOSS_LIMIT`
"Take profit orders are not supported for this symbol." | 交易对上未启用`TAKE_PROFIT` 
"Take profit limit orders are not supported for this symbol." | 交易对上未启用`TAKE_PROFIT_LIMIT`
"Price * QTY is zero or less." | `price` * `quantity`太小
"IcebergQty exceeds QTY." | `icebergQty` 必须少于订单数量
"This action disabled is on this account." | 联系客户支持； 该帐户已禁用了某些操作。
"Unsupported order combination" | 不允许组合`orderType`, `timeInForce`, `stopPrice`, 和/或 `icebergQty` 。
"Order would trigger immediately." | 与最后交易价格相比，订单的止损价无效。
"Cancel order is invalid. Check origClientOrderId and orderId." | 未发送`origClientOrderId` 或`orderId` 。
"Order would immediately match and take." | `LIMIT_MAKER` 订单类型将立即匹配并进行交易，而不是纯粹的生成订单。
"The relationship of the prices for the orders is not correct." | `OCO`订单中设置的价格不符合报价规则：<br> The rules are: <br> `SELL Orders`: Limit Price > Last Price > Stop Price <br>`BUY Orders`: Limit Price < Last Price < Stop Price
"OCO orders are not supported for this symbol" | `OCO`订单不支持该交易对


# 备注说明
## 请求参数

### <h3 id="request-email-address">Email地址</h3>
Email地址作为请求参数，需要转译(encode)。比如 `alice@test.com` 转换成 `alice%40test.com`
