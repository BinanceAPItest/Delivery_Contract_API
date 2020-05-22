---
title: Binance API 使用文档

language_tabs: # must be one of https://git.io/vQNgJ
  #- shell
  #- javascript
  #- jason


includes:

search: true

---




# 基本信息


## Rest 基本信息

* 本篇列出REST接口的baseurl **https://fapi.binance.com**
* 所有接口的响应都是JSON格式
* 响应中如有数组，数组元素以时间升序排列，越早的数据越提前。
* 所有时间、时间戳均为UNIX时间，单位为毫秒
* HTTP `4XX` 错误码用于指示错误的请求内容、行为、格式。
* HTTP `403` 错误码表示违反WAF限制(Web应用程序防火墙)。
* HTTP `429` 错误码表示警告访问频次超限，即将被封IP
* HTTP `418` 表示收到429后继续访问，于是被封了。
* HTTP `5XX` 错误码用于指示Binance服务侧的问题。 
* HTTP `504` 表示API服务端已经向业务核心提交了请求但未能获取响应，特别需要注意的是`504`代码不代表请求失败，而是未知。很可能已经得到了执行，也有可能执行失败，需要做进一步确认。
* 每个接口都有可能抛出异常

> 异常响应格式如下：

```javascript
{
  "code": -1121,
  "msg": "Invalid symbol."
}
```

* 具体的错误码及其解释在[错误代码](#cf68bca02a)
* `GET`方法的接口, 参数必须在`query string`中发送.
* `POST`, `PUT`, 和 `DELETE` 方法的接口, 参数可以在 `query string`中发送，也可以在 `request body`中发送(content type `application/x-www-form-urlencoded`)。允许混合这两种方式发送参数。但如果同一个参数名在query string和request body中都有，query string中的会被优先采用。
* 对参数的顺序不做要求。

## 访问限制
* 在 `/dapi/v1/exchangeInfo`接口中`rateLimits`数组里包含有REST接口(不限于本篇的REST接口)的访问限制。包括带权重的访问频次限制、下单速率限制。本篇`枚举定义`章节有限制类型的进一步说明。
* 违反上述任何一个访问限制都会收到HTTP 429，这是一个警告.

### IP 访问限制
* 每个请求将包含一个`X-MBX-USED-WEIGHT-(intervalNum)(intervalLetter)`的头，其中包含当前IP所有请求的已使用权重。
* 每个路由都有一个“权重”，该权重确定每个接口计数的请求数。较重的接口和对多个交易对进行操作的接口将具有较重的“权重”。
* 收到429时，您有责任作为API退回而不向其发送更多的请求。
* **如果屡次违反速率限制和/或在收到429后未能退回，将导致API的IP被禁（http状态418）。**
* 频繁违反限制，封禁时间会逐渐延长 ，**对于重复违反者，将会被封从2分钟到3天**。
* **访问限制是基于IP的，而不是API Key**

<aside class="notice">
建议您尽可能多地使用websocket消息获取相应数据，以减少请求带来的访问限制压力。
</aside>


###下单频率限制
* 每个下单请求回报将包含一个`X-MBX-ORDER-COUNT-(intervalNum)(intervalLetter)`的头，其中包含当前账户已用的下单限制数量。
* 被拒绝或不成功的下单并不保证回报中包含以上头内容。
* **下单频率限制是基于每个账户计数的。**

## 接口鉴权类型
* 每个接口都有自己的鉴权类型，鉴权类型决定了访问时应当进行何种鉴权
* 如果需要 API-key，应当在HTTP头中以`X-MBX-APIKEY`字段传递
* API-key 与 API-secret 是大小写敏感的
* 可以在网页用户中心修改API-key 所具有的权限，例如读取账户信息、发送交易指令、发送提现指令

鉴权类型 | 描述
------------ | ------------
NONE | 不需要鉴权的接口
TRADE | 需要有效的API-KEY和签名
USER_DATA | 需要有效的API-KEY和签名
USER_STREAM | 需要有效的API-KEY
MARKET_DATA | 需要有效的API-KEY


## 需要签名的接口 (TRADE 与 USER_DATA)
* 调用这些接口时，除了接口本身所需的参数外，还需要传递`signature`即签名参数。
* 签名使用`HMAC SHA256`算法. API-KEY所对应的API-Secret作为 `HMAC SHA256` 的密钥，其他所有参数作为`HMAC SHA256`的操作对象，得到的输出即为签名。
* 签名大小写不敏感。
* 当同时使用query string和request body时，`HMAC SHA256`的输入query string在前，request body在后

### 时间同步安全
* 签名接口均需要传递`timestamp`参数, 其值应当是请求发送时刻的unix时间戳（毫秒）
* 服务器收到请求时会判断请求中的时间戳，如果是5000毫秒之前发出的，则请求会被认为无效。这个时间窗口值可以通过发送可选参数`recvWindow`来自定义。
* 另外，如果服务器计算得出客户端时间戳在服务器时间的‘未来’一秒以上，也会拒绝请求。

> 逻辑伪代码：
  
  ```javascript
  if (timestamp < (serverTime + 1000) && (serverTime - timestamp) <= recvWindow) {
    // process request
  } else {
    // reject request
  }
  ```

**关于交易时效性** 
互联网状况并不100%可靠，不可完全依赖,因此你的程序本地到币安服务器的时延会有抖动.
这是我们设置`recvWindow`的目的所在，如果你从事高频交易，对交易时效性有较高的要求，可以灵活设置recvWindow以达到你的要求。

<aside class="notice">
不推荐使用5秒以上的recvWindow
</aside>

### POST /dapi/v1/order 的示例

以下是在linux bash环境下使用 echo openssl 和curl工具实现的一个调用接口下单的示例
apikey、secret仅供示范

Key | Value
------------ | ------------
apiKey | vmPUZE6mv9SD5VNHk4HlWFsOr6aKE2zvsw0MuIgwCIPy6utIco14y7Ju91duEh8A
secretKey | NhqPtmdSJYdKjVHjA7PZj4Mge3R5YNiP1e3UZjInClVN65XAbvqqM6A7H5fATj0j


参数 | 取值
------------ | ------------
symbol | BTCUSD_200930
side | BUY
type | LIMIT
timeInForce | GTC
quantity | 1
price | 0.1
recvWindow | 5000
timestamp | 1499827319559


### 示例 1: 所有参数通过 query string 发送
> **示例1:**
> **HMAC SHA256 签名:**

```shell
    $ echo -n "symbol=BTCUSD_200930&side=BUY&type=LIMIT&timeInForce=GTC&quantity=1&price=6000&recvWindow=5000&timestamp=1499827319559" | openssl dgst -sha256 -hmac "NhqPtmdSJYdKjVHjA7PZj4Mge3R5YNiP1e3UZjInClVN65XAbvqqM6A7H5fATj0j"
    (stdin)= c8db56825ae71d6d79447849e617115f4a920fa2acdcab2b053c4b2838bd6b71
```


> **curl 调用:**

```shell
    (HMAC SHA256)
    $ curl -H "X-MBX-APIKEY: vmPUZE6mv9SD5VNHk4HlWFsOr6aKE2zvsw0MuIgwCIPy6utIco14y7Ju91duEh8A" -X POST 'https://fapi.binance.com/dapi/v1/order?symbol=BTCUSD_200930&side=BUY&type=LIMIT&timeInForce=GTC&quantity=1&price=6000&recvWindow=5000&timestamp=1499827319559&signature=c8db56825ae71d6d79447849e617115f4a920fa2acdcab2b053c4b2838bd6b71'
```

* **queryString:** 

symbol=BTCUSD_200930   
&side=BUY   
&type=LIMIT   
&timeInForce=GTC   
&quantity=1   
&price=6000  
&recvWindow=5000   
&timestamp=1499827319559


### 示例 2: 所有参数通过 request body 发送
> **示例2:**
> **HMAC SHA256 签名:**

```shell
    $ echo -n "symbol=BTCUSD_200930&side=BUY&type=LIMIT&timeInForce=GTC&quantity=1&price=6000&recvWindow=5000&timestamp=1499827319559" | openssl dgst -sha256 -hmac "NhqPtmdSJYdKjVHjA7PZj4Mge3R5YNiP1e3UZjInClVN65XAbvqqM6A7H5fATj0j"
    (stdin)= c8db56825ae71d6d79447849e617115f4a920fa2acdcab2b053c4b2838bd6b71
```


> **curl 调用:**

```shell
    (HMAC SHA256)
    $ curl -H "X-MBX-APIKEY: vmPUZE6mv9SD5VNHk4HlWFsOr6aKE2zvsw0MuIgwCIPy6utIco14y7Ju91duEh8A" -X POST 'https://fapi.binance.com/dapi/v1/order' -d 'symbol=BTCUSD_200930&side=BUY&type=LIMIT&timeInForce=GTC&quantity=1&price=6000&recvWindow=5000&timestamp=1499827319559&signature=c8db56825ae71d6d79447849e617115f4a920fa2acdcab2b053c4b2838bd6b71'
```

* **requestBody:** 

symbol=BTCUSD_200930  
&side=BUY   
&type=LIMIT   
&timeInForce=GTC   
&quantity=1   
&price=6000  
&recvWindow=5000   
&timestamp=1499827319559


### 示例 3: 混合使用 query string 与 request body
> **示例3:**
> **HMAC SHA256 签名:**

```shell
    $ echo -n "symbol=BTCUSD_200930&side=BUY&type=LIMIT&timeInForce=GTCquantity=1&price=6000&recvWindow=5000&timestamp=1499827319559" | openssl dgst -sha256 -hmac "NhqPtmdSJYdKjVHjA7PZj4Mge3R5YNiP1e3UZjInClVN65XAbvqqM6A7H5fATj0j"
    (stdin)= 0fd168b8ddb4876a0358a8d14d0c9f3da0e9b20c5d52b2a00fcf7d1c602f9a77
```


> **curl 调用:**

```shell
    (HMAC SHA256)
    $ curl -H "X-MBX-APIKEY: vmPUZE6mv9SD5VNHk4HlWFsOr6aKE2zvsw0MuIgwCIPy6utIco14y7Ju91duEh8A" -X POST 'https://fapi.binance.com/dapi/v1/order?symbol=BTCUSD_200930&side=BUY&type=LIMIT&timeInForce=GTC' -d 'quantity=1&price=6000&recvWindow=5000&timestamp=1499827319559&signature=0fd168b8ddb4876a0358a8d14d0c9f3da0e9b20c5d52b2a00fcf7d1c602f9a77'
```

* **queryString:** 

symbol=BTCUSD_200930&side=BUY&type=LIMIT&timeInForce=GTC

* **requestBody:** 

quantity=1&price=6000&recvWindow=5000&timestamp=1499827319559


Note that the signature is different in example 3.
There is no & between "GTC" and "quantity=1".



## 公开API参数
### 术语解释
* `base asset` 指一个交易对的交易对象，即写在靠前部分的资产名
* `quote asset` 指一个交易对的定价资产，即写在靠后部分资产名


### 枚举定义

**交易对类型:**

* FUTURE 期货
* DELIVERY_CONTRACT 交割合约

**合约类型:**

* CURRENT_QUARTER 当季合约
* NEXT_QUARTER 次季合约


**合约状态:**

* PENDING_TRADING   待上市
* TRADING          	交易中
* PRE_DELIVERING		结算中
* DELIVERING			交割中
* DELIVERED			已交割


**订单状态:**

* NEW 新建订单
* PARTIALLY_FILLED  部分成交
* FILLED  全部成交
* CANCELED  已撤销
* REJECTED 订单被拒绝
* EXPIRED 订单过期(根据timeInForce参数规则)

**订单种类:**

* LIMIT 限价单
* MARKET 市价单
* STOP 止损限价单
* STOP_MARKET 止损市价单
* TAKE_PROFIT 止盈限价单
* TAKE_PROFIT_MARKET 止盈市价单
* TRAILING_STOP_MARKET 跟踪止损单

**订单方向:**

* BUY 买入
* SELL 卖出

**持仓方向:**

* BOTH 单一持仓方向
* LONG 多头（双向持仓下）
* SHORT 空头（双向持仓下）

**有效方式:**

* GTC - Good Till Cancel 成交为止
* IOC - Immediate or Cancel 无法立即成交(吃单)的部分就撤销
* FOK - Fill or Kill 无法全部立即成交就撤销
* GTX - Good Till Crossing 无法成为挂单方就撤销

**条件价格触发类型 (workingType)**

* MARK_PRICE
* CONTRACT_PRICE 

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

* REQUESTS_WEIGHT  单位时间请求权重之和上限

```json
    {
      "rateLimitType": "REQUEST_WEIGHT",
      "interval": "MINUTE",
      "intervalNum": 1,
      "limit": 2400
    }
```
* ORDERS    单位时间下单(撤单)次数上限

```json
    {
      "rateLimitType": "ORDERS",
      "interval": "MINUTE",
      "intervalNum": 1,
      "limit": 1200
    }
```

**限制间隔**
* MINUTE



## 过滤器
过滤器，即Filter，定义了一系列交易规则。
共有两类，分别是针对交易对的过滤器`symbol filters`，和针对整个交易所的过滤器`exchange filters`(暂不支持)

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

价格过滤器用于检测order订单中price参数的合法性
* `minPrice` 定义了 `price`/`stopPrice` 允许的最小值
* `maxPrice` 定义了 `price`/`stopPrice` 允许的最大值。
* `tickSize` 定义了 `price`/`stopPrice` 的步进间隔，即price必须等于minPrice+(tickSize的整数倍)
以上每一项均可为0，为0时代表这一项不再做限制。

逻辑伪代码如下：

* `price` >= `minPrice`
* `price` <= `maxPrice`
* (`price`-`minPrice`) % `tickSize` == 0



#### LOT_SIZE 订单尺寸

> */exchangeInfo 响应中的格式:**

```javascript
  {
    "filterType": "LOT_SIZE",
    "minQty": "0.00100000",
    "maxQty": "100000.00000000",
    "stepSize": "0.00100000"
  }
```

lots是拍卖术语，这个过滤器对订单中的`quantity`也就是数量参数进行合法性检查。包含三个部分：

* `minQty` 表示 `quantity` 允许的最小值.
* `maxQty` 表示 `quantity` 允许的最大值
* `stepSize` 表示 `quantity`允许的步进值。

逻辑伪代码如下：

* `quantity` >= `minQty`
* `quantity` <= `maxQty`
* (`quantity`-`minQty`) % `stepSize` == 0


#### MARKET_LOT_SIZE 市价订单尺寸
参考LOT_SIZE，区别仅在于对市价单还是限价单生效

#### MAX_NUM_ORDERS 最多订单数


> **/exchangeInfo 响应中的格式:**

```javascript
  {
    "filterType": "MAX_NUM_ORDERS",
    "limit": 25
  }
```

定义了某个交易对最多允许的挂单数量（不包括已关闭的订单）
普通订单与条件订单均计算在内


#### PERCENT_PRICE 价格振幅过滤器

> **/exchangeInfo 响应中的格式:**

```javascript
  {
    "filterType": "PERCENT_PRICE",
    "multiplierUp": "1.1500",
    "multiplierDown": "0.8500",
    "multiplierDecimal": 4
  }
```

`PERCENT_PRICE` 定义了基于标记价格计算的挂单价格的可接受区间.

挂单价格必须同时满足以下条件：

* 买单: `price` <= `markPrice` * `multiplierUp`
* 卖单: `price` >= `markPrice` * `multiplierDown`



# 行情接口
## 测试服务器连通性 PING
``
GET /dapi/v1/ping
``

> **响应:**

```javascript
{}
```

测试能否联通

**权重:**
1

**参数:**
NONE



## 获取服务器时间

> **响应:**

```javascript
{
  "serverTime": 1499827319559 // 当前的系统时间
}
```

``
GET /dapi/v1/time
``

获取服务器时间

**权重:**
1

**参数:**
NONE


## 获取交易规则和交易对

> **响应:**

```javascript
{
	"exchangeFilters": [],
 	"rateLimits": [ // API访问的限制
 		{
 			"interval": "MINUTE", // 按照分钟计算
   			"intervalNum": 1, // 按照1分钟计算
   			"limit": 6000, // 上限次数
   			"rateLimitType": "REQUEST_WEIGHT" // 按照访问权重来计算
   		},
  		{
  			"interval": "MINUTE",
   			"intervalNum": 1,
   			"limit": 6000,
   			"rateLimitType": "ORDERS" // 按照订单数量来计算
   		}
   	],
 	"serverTime": 1565613908500, // 系统时间
 	"symbols": [ // 交易对信息
 		{
 			"filters": [
 				{
 					"filterType": "PRICE_FILTER", // 价格限制
     				"maxPrice": "10000000", // 价格上限, 最大价格
     				"minPrice": "0.00000100", // 价格下限, 最小价格
     				"tickSize": "0.00000100" // 步进间隔
     			},
    			{
    				"filterType": "LOT_SIZE", // 数量限制
     				"maxQty": "10000000", // 数量上限, 最大数量
     				"minQty": "0.00100000", // 数量下限, 最小数量
     				"stepSize": "0.00100000" // 允许的步进值
     			},
    			{
    				"filterType": "MARKET_LOT_SIZE", // 市价订单数量限制
     				"maxQty": "10000000", // 数量上限, 最大数量
     				"minQty": "0.00100000", // 数量下限, 最小数量
     				"stepSize": "0.00100000" // 允许的步进值
     			},
     			{
    				"filterType": "MAX_NUM_ORDERS", // 最多挂单数限制
    				"limit": 100
  				}，
  				{
    				"filterType": "PERCENT_PRICE", // 价格比限制
    				"multiplierUp": "1.1500", // 价格上限百分比
    				"multiplierDown": "0.8500", // 价格下限百分比
    				"multiplierDecimal": 4
  				}
    		],
   			"maintMarginPercent": "2.5000", // 维持保证金比例
   			"pricePrecision": 2, // 价格小数点位数
   			"quantityPrecision": 3, // 数量小数点位数
   			"requiredMarginPercent": "5.0000", // 所需保证金比例
   			"OrderType": [ // 订单类型
   				"LIMIT",  // 限价单
   				"MARKET",  // 市价单
   				"STOP", // 止损单
   				"TAKE_PROFIT", // 止盈单
   				"TRAILING_STOP_MARKET" // 跟踪止损单
   			],
   			"timeInForce": [ // 有效方式
   				"GTC", // 成交为止, 一直有效
   				"IOC", // 无法立即成交(吃单)的部分就撤销
   				"FOK", // 无法全部立即成交就撤销
   				"GTX" // 无法成为挂单方就撤销
   			],
   			"symbol": "BTCUSD_200930", // 交易对
   			"pair": "BTCUSD",	// 标的交易对
   			"contractType": "CURRENT_QUARTER",   // 合约类型
   			"deliveryDate": "20200930",
   			"onboardDate": "20200515",
   			"contractStatus": "LISTING", // 交易对状态
   			"contractSize": 100,     //
   			"quoteAsset": "USD", // 报价币种
   			"baseAsset": "BTC",   
   			"marginAsset": "BTC"	// 保证金币种 
   		}
   	],
	"timezone": "UTC" // 服务器所用的时间区域
}

```

``
GET /dapi/v1/exchangeInfo
``

获取交易规则和交易对

**权重:**
1

**参数:**
NONE



## 深度信息

> **响应:**

```javascript
{
  "lastUpdateId": 1027024,
  "bids": [				// 买单
    [
      "4.00000000",     // 价格
      "431.00000000"    // 数量
    ]
  ],
  "asks": [				// 卖单
    [
      "4.00000200",		// 价格
      "12.00000000"		// 数量
    ]
  ]
}
```

``
GET /dapi/v1/depth
``

**权重:**

limit         | 权重
------------  | ------------
5, 10, 20, 50 | 2
100           | 5
500           | 10
1000          | 20

**参数:**

 名称  |  类型  | 是否必需 |                            描述
------ | ------ | -------- | -----------------------------------------------------------
symbol | STRING | YES      | 交易对
limit  | INT    | NO       | 默认 100; 最大 1000. 可选值:[5, 10, 20, 50, 100, 500, 1000]


## 近期成交

> **响应:**

```javascript
[
  {
    "id": 28457,				// 成交ID
    "price": "4.00000100",		// 成交价格
    "qty": "12.00000000",		// 成交数量
    "quoteQty": "48.00",		// 成交额
    "time": 1499865549590,		// 时间
    "isBuyerMaker": true		// 买方是否为挂单方
  }
]
```

``
GET /dapi/v1/trades
``

获取近期成交（最多至最近24h）

**权重:**
1

**参数:**

 名称  |  类型  | 是否必需 |          描述
------ | ------ | -------- | ----------------------
symbol | STRING | YES      | 交易对
limit  | INT    | NO       | 默认值:500 最大值:1000.


## 查询历史成交 (MARKET_DATA)

> **响应:**

```javascript
[
  {
    "id": 28457,				// 成交ID
    "price": "4.00000100",		// 成交价格
    "qty": "12.00000000",		// 成交数量
    "quoteQty": "48.00",		// 成交额
    "time": 1499865549590,		// 时间
    "isBuyerMaker": true		// 买方是否为挂单方
  }
]
```

``
GET /dapi/v1/historicalTrades
``

**权重:**
5

**参数:**

 名称  |  类型  | 是否必需 |                      描述
------ | ------ | -------- | ----------------------------------------------
symbol | STRING | YES      | 交易对
limit  | INT    | NO       | 默认值:500 最大值:1000.
fromId | LONG   | NO       | 从哪一条成交id开始返回. 缺省返回最近的成交记录




## 近期成交(归集) 

> **响应:**

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
  }
]
```

``
GET /dapi/v1/aggTrades
``

归集交易与逐笔交易的区别在于，同一价格、同一方向、同一时间（按秒计算）的trade会被聚合为一条

**权重:**
1

**参数:**

  名称    |  类型  | 是否必需 |                描述
--------- | ------ | -------- | ----------------------------------
symbol    | STRING | YES      | 交易对
fromId    | LONG   | NO       | 从包含fromID的成交开始返回结果
startTime | LONG   | NO       | 从该时刻之后的成交记录开始返回结果
endTime   | LONG   | NO       | 返回该时刻为止的成交记录
limit     | INT    | NO       | 默认 500; 最大 1000.


* 如果没有发送任何筛选参数(`fromId`, `startTime`, `endTime`)，默认返回最近的成交记录




## 最新现货指数价格,基差,和Mark Price

> **响应:**


```javascript
[
	{
	   	"symbol": "BTCUSD_200930",		// 交易对
	   	"pair": "BTCUSD",					// 标的
	   	"indexPrice": "11180.12345678",	// 指数价格
    	"markPrice": "11186.12345678",	// mark price
    	"estimatedSettlePrice": "0",    // 预估结算价，0 表示暂无
    	"time": 1562566020000				// 更新时间
	}
]
```

``
GET /dapi/v1/premiumIndex
``


**权重:**
1

**参数:**

 名称  |  类型  | 是否必需 |  描述
------ | ------ | -------- | ------
symbol | STRING | NO       | 交易对
pair   | STRING | NO 		| 标的交易对






## K线数据

> **响应:**

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
GET /dapi/v1/klines
``
每根K线的开盘时间可视为唯一ID

**权重:**
1

**参数:**

  名称    |  类型  | 是否必需 |          描述
--------- | ------ | -------- | ----------------------
symbol    | STRING | YES      | 交易对
interval  | ENUM   | YES      | 时间间隔
startTime | LONG   | NO       | 起始时间
endTime   | LONG   | NO       | 结束时间
limit     | INT    | NO       | 默认值:500 最大值:1500

* 缺省返回最近的数据


## 连续合约K线数据

> **响应:**

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
GET /dapi/v1/continuousKlines
``
每根K线的开盘时间可视为唯一ID

**权重:**
1

**参数:**

  名称    |  类型  | 是否必需 |          描述
--------- | ------ | -------- | ----------------------
pair    	| STRING | YES      | 标的交易对
contractType | ENUM | YES		| 合约类型
interval  | ENUM   | YES      | 时间间隔
startTime | LONG   | NO       | 起始时间
endTime   | LONG   | NO       | 结束时间
limit     | INT    | NO       | 默认值:500 最大值:1500

* 缺省返回最近的数据





## 价格指数K线数据

> **响应:**

```javascript
[
  [
    1587910140000,      // 开盘时间
    "0.01634790",       // 开盘价
    "0.80000000",       // 最高价
    "0.01575800",       // 最低价
    "0.01577100",       // 收盘价(当前K线未结束的即为最新价)
    "0	", 					 // 请忽略 （成交量）
    1587910199999,      // 收盘时间
    "0",    				 // 请忽略 （成交额）
    60,                	 // 构成记录数
    "0",    				 // 请忽略 （主动买入成交量）
    "0",      			 // 请忽略 （主动买入成交额）
    "0" 					 // 请忽略该参数
  ]
]
```

``
GET /dapi/v1/indexPriceKlines
``
每根K线的开盘时间可视为唯一ID

**权重:**
1

**参数:**

  名称    |  类型  | 是否必需 |          描述
--------- | ------ | -------- | ----------------------
pair    	| STRING | YES      | 标的交易对
interval  | ENUM   | YES      | 时间间隔
startTime | LONG   | NO       | 起始时间
endTime   | LONG   | NO       | 结束时间
limit     | INT    | NO       | 默认值:500 最大值:1500

* 最多只能查询最近30天数据




## 标记价格K线数据

> **响应:**

```javascript
[
  [
    1587910140000,      // 开盘时间
    "0.01634790",       // 开盘价
    "0.80000000",       // 最高价
    "0.01575800",       // 最低价
    "0.01577100",       // 收盘价(当前K线未结束的即为最新价)
    "0	", 					 // 请忽略 （成交量）
    1587910199999,      // 收盘时间
    "0",    				 // 请忽略 （成交额）
    60,                	 // 构成记录数
    "0",    				 // 请忽略 （主动买入成交量）
    "0",      			 // 请忽略 （主动买入成交额）
    "0" 					 // 请忽略该参数
  ]
]
```

``
GET /dapi/v1/markPriceKlines
``
每根K线的开盘时间可视为唯一ID

**权重:**
1

**参数:**

  名称    |  类型  | 是否必需 |          描述
--------- | ------ | -------- | ----------------------
symbol   	| STRING | YES      | 交易对
interval  | ENUM   | YES      | 时间间隔
startTime | LONG   | NO       | 起始时间
endTime   | LONG   | NO       | 结束时间
limit     | INT    | NO       | 默认值:500 最大值:1500

* 最多只能查询最近30天数据




## 24hr价格变动情况

> **响应:**


```javascript
[
	{
  		"pair": "BTCUSD",
  		"contractType": "CURRENT_QUARTER",
  		"symbol": "BTCUSD_200930",
  		"priceChange": "-94.99999800",    //24小时价格变动
  		"priceChangePercent": "-95.960",  //24小时价格变动百分比
  		"weightedAvgPrice": "0.29628482", //加权平均价
  		"lastPrice": "4.00000200",        //最近一次成交价
  		"lastQty": "200.00000000",        //最近一次成交额
  		"openPrice": "99.00000000",       //24小时内第一次成交的价格
  		"highPrice": "100.00000000",      //24小时最高价
  		"lowPrice": "0.10000000",         //24小时成交量
  		"volume": "8913.30000000",        //24小时成交额
  		"quoteVolume": "15.30000000",     //24小时成交额
  		"openTime": 1499783499040,        //24小时内，第一笔交易的发生时间
  		"closeTime": 1499869899040,       //24小时内，最后一笔交易的发生时间
  		"firstId": 28385,   // 首笔成交id
  		"lastId": 28460,    // 末笔成交id
  		"count": 76         // 成交笔数
    }
]
```

``
GET /dapi/v1/ticker/24hr
``

请注意，不携带symbol参数会返回全部交易对数据，不仅数据庞大，而且权重极高

**权重:**
* 带symbol为`1`
* 不带为`40`

**参数:**

 名称  |  类型  | 是否必需 |  描述
------ | ------ | -------- | ------
symbol | STRING | NO       | 交易对
pair   | STRING | NO       | 标的交易对

* symbol 和 pair 不接受同时发送
* 发送 pair的，返回pair对应所有正在交易的symbol数据
* symbol,pair 都没有发送的，返回所有symbol数据


## 最新价格

> **响应:**



```javascript
[
	{
  		"symbol": "BTCUSD_200930",	// 交易对
  		"price": "6000.01"		// 价格
	}
]
```

``
GET /dapi/v1/ticker/price
``

返回最近价格

**权重:**

* 单交易对`1`
* 多交易对`2`

**参数:**

 名称  |  类型  | 是否必需 |  描述
------ | ------ | -------- | ------
symbol | STRING | NO       | 交易对
pair   | STRING | NO       | 标的交易对

* symbol 和 pair 不接受同时发送
* 发送 pair的，返回pair对应所有正在交易的symbol数据
* symbol,pair 都没有发送的，返回所有symbol数据



## 当前最优挂单

> **响应:**


```javascript
[
	{
  		"symbol": "BTCUSD_200930",
  		"bidPrice": "9994.00000000",//最优买单价
  		"bidQty": "431.00000000",//挂单量
  		"askPrice": "9994.00000200",//最优卖单价
  		"askQty": "9.00000000"//挂单量
	}
]
```

``
GET /dapi/v1/ticker/bookTicker
``

返回当前最优的挂单(最高买单，最低卖单)

**权重:**
单交易对1   
多交易对2

**参数:**

 名称  |  类型  | 是否必需 |  描述
------ | ------ | -------- | ------
symbol | STRING | NO       | 交易对
pair   | STRING | NO       | 标的交易对

* symbol 和 pair 不接受同时发送
* 发送 pair的，返回pair对应所有正在交易的symbol数据
* symbol,pair 都没有发送的，返回所有symbol数据




##获取市场强平订单

> **响应:**

```javascript
[

    {
          "symbol": "BTCUSD_200930",          // 交易对
          "price": "7918.33",                 // 订单价格
          "origQty": "0.014",                 // 订单数量
          "executedQty": "0.014",             // 成交数量
          "avragePrice": "7918.33",           // 成交均价
          "status": "FILLED",                 // 订单状态
          "timeInForce": "IOC",               // 有效方式
          "type": "LIMIT",					  // 订单类型
          "side": "SELL",                     // 订单方向
          "time": 1568014460893 
    },
]
```


``GET /dapi/v1/allForceOrders``

**权重:** 5

**参数:**

  名称    |  类型  | 是否必需 |         描述
--------- | ------ | -------- | --------------------
symbol    | STRING | NO       | 交易对
pair      | STRING | NO       | 标的交易对
startTime | LONG   | NO       | 起始时间
endTime   | LONG   | NO       | 结束时间
limit     | LONG   | NO       | 默认值:100 最大值:1000


* symbol 和 pair 不接受同时发送
* 发送 pair的，返回pair对应所有正在交易的symbol数据
* symbol,pair 都没有发送的，返回所有symbol数据


## 获取未平仓合约数

> **响应:**

```javascript
{
	"openInterest": "10659.509", // 未平仓合约数量
	"symbol": "BTCUSD_200930" // 交易对
	"pair": "BTCUSD", // 标的交易对
	"contractType": "CURRENT_QUARTER"  // 合约类型
}

```


``
GET /dapi/v1/openInterest
``

**权重:** 1

**参数:**

 名称  |  类型  | 是否必需 |  描述
------ | ------ | -------- | ------
symbol | STRING | YES     | 交易对





## 杠杆分层标准 (USER_DATA)


> **响应:**

```javascript
[
    {
        "pair": "BTCUSD",
        "brackets": [
            {
                "bracket": 1,   // 层级
                "initialLeverage": 75,  // 该层允许的最高初始杠杆倍数
                "qtyCap": 100,  // 该层对应的数量上限
                "qtylFloor": 0,  // 该层对应的数量下限 
                "maintMarginRatio": 0.0065 // 该层对应的维持保证金率
            },
        ]
    }
[
```



``
GET /dapi/v1/leverageBracket
``


**权重:** 1

**参数:**

 名称  |  类型  | 是否必需 |  描述
------ | ------ | -------- | ------
pair	| STRING | NO






# Websocket 行情推送

* 本篇所列出的所有wss接口的baseurl为: **wss://dstream.binance.com**
* 订阅单一stream格式为 **/ws/\<streamName\>**
* 组合streams的URL格式为 **/stream?streams=\<streamName1\>/\<streamName2\>/\<streamName3\>**
* stream名称中所有交易对均为**小写**
* 每个到**dstream.binancefuture.com**的链接有效期不超过24小时，请妥善处理断线重连。
* 每3分钟，服务端会发送ping帧，客户端应当在10分钟内回复pong帧，否则服务端会主动断开链接。允许客户端发送不成对的pong帧(即客户端可以以高于10分钟每次的频率发送pong帧保持链接)。

## 实时订阅/取消数据流

* 以下数据可以通过websocket发送以实现订阅或取消订阅数据流。示例如下。
* 响应内容中的`id`是无符号整数，作为往来信息的唯一标识。

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
      	"btcusd_200930@aggTrade",    
      	"btcusd_200930@depth"     
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
      "btcusd_200930@depth"   
    ],    
    "id": 312   
  }



### 已订阅信息流

> **响应**
  
  ```javascript
  {
    "result": [
      "btcusd_200930@aggTrade"
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
    "result": null
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




## 最新合约价格
aggTrade中的价格'p'或ticker/miniTicker中的价格'c'均可以作为最新成交价。

## 归集交易

> **Payload:**

```javascript
{
  "e": "aggTrade",  // 事件类型
  "E": 123456789,   // 事件时间
  "s": "BTCUSD_200930",    // 交易对
  "a": 5933014,		// 归集成交ID
  "p": "0.001",     // 成交价格
  "q": "100",       // 成交数量
  "f": 100,         // 被归集的首个交易ID
  "l": 105,         // 被归集的末次交易ID
  "T": 123456785,   // 成交时间
  "m": true         // 买方是否是做市方。如true，则此次成交是一个主动卖出单，否则是一个主动买入单。
}
```

同一价格、同一方向、同一时间（100ms计算）的trade会被聚合为一条.推送间隔100毫秒。

**Stream Name:**       
``<symbol>@aggTrade``

**Update Speed:** 100ms




## 最新现货指数价格

> **Payload:**

```javascript
  {
    "e": "indexPriceUpdate",  // 事件类型
    "E": 1562305380000,      // 事件时间
    "i": "BTCUSD",          // 标的交易对
    "p": "11185.87786614",   // 指数价格
  }
```


**Stream Name:**    
``<pair>@indexPrice`` 或 ``<pair>@indexPrice@1s``

**Update Speed:** 3000ms 或 1000ms



## 最新MarkPrice

> **Payload:**

```javascript
  {
    "e": "markPriceUpdate",  // 事件类型
    "E": 1562305380000,      // 事件时间
    "s": "BTCUSD_200630",    // 交易对
    "p": "11185.87786614",   // 标记价格
    "P": "0"    // 预估结算价，0 表示暂无
  }
```


**Stream Name:**    
``<symbol>@markPrice`` 或 ``<symbol>@markPrice@1s``

**Update Speed:** 3000ms 或 1000ms



## pair所有symbol最新MarkPrice

> **Payload:**

```javascript
 [ 
	 {
	    "e": "markPriceUpdate",  // 事件类型
	    "E": 1562305380000,      // 事件时间
	    "s": "BTCUSD_200630",          // 交易对
	    "p": "11185.87786614",   // 标记价格
	    "P": "0"    // 预估结算价，0 表示暂无
  	}
 ]
```


**Stream Name:**    
``<pair>@markPrice`` 或 ``<pair>@markPrice@1s``

**Update Speed:** 3000ms 或 1000ms






## K线

> **Payload:**

```javascript
{
  "e": "kline",     // 事件类型
  "E": 123456789,   // 事件时间
  "s": "BTCUSD_200930",    // 交易对
  "k": {
    "t": 123400000, // 这根K线的起始时间
    "T": 123460000, // 这根K线的结束时间
    "s": "BTCUSD_200930",  // 交易对
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

**订阅Kline需要提供间隔参数，最短为分钟线，最长为月线。支持以下间隔:**

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

**Stream Name:**    
``<symbol>@kline_<interval>``

**Update Speed:** 250ms




## 连续合约K线

> **Payload:**

```javascript
{
  "e": "continuous_kline",     // 事件类型
  "E": 123456789,   // 事件时间
  "ps": "BTCUSD",    // 标的交易对
  "ct": "CURRENT_QUARTER",  // 合约类型
  "k": {
    "t": 123400000, // 这根K线的起始时间
    "T": 123460000, // 这根K线的结束时间
    "s": "BTCUSD_200930",  // 交易对
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

**订阅Kline需要提供间隔参数，最短为分钟线，最长为月线。支持以下间隔:**

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

**Stream Name:**    
``<pair>_<contractType>@continuousKline_<interval>``

**Update Speed:** 250ms




## 价格指数K线

> **Payload:**

```javascript
{
  "e": "indexPrice_kline",     // 事件类型
  "E": 123456789,   // 事件时间
  "ps": "BTCUSD",    // 标的交易对
  "k": {
    "t": 123400000, // 这根K线的起始时间
    "T": 123460000, // 这根K线的结束时间
    "s": "0",  // 无意义字段
    "i": "1m",      // K线间隔
    "f": 100,       // 这根K线期间第一笔数据Id
    "L": 200,       // 这根K线期间末一笔数据Id
    "o": "0.0010",  // 这根K线期间第一笔成交价
    "c": "0.0020",  // 这根K线期间末一笔成交价
    "h": "0.0025",  // 这根K线期间最高成交价
    "l": "0.0015",  // 这根K线期间最低成交价
    "v": "0",    	// 无意义字段
    "n": 8,       	// 这根K线更新期间数据数量
    "x": false,     // 这根K线是否完结（是否已经开始下一根K线）
    "q": "0",  		// 无意义字段
    "V": "0",     	// 无意义字段
    "Q": "0",   		// 无意义字段
    "B": "0"   		// 无意义字段
  }
}
```

K线stream逐秒推送所请求的K线种类(最新一根K线)的更新。

**订阅Kline需要提供间隔参数，最短为分钟线，最长为月线。支持以下间隔:**

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

**Stream Name:**    
``<pair>@indexPriceKline_<interval>``

**Update Speed:** 250ms




## 标记价格K线

> **Payload:**

```javascript
{
  "e": "markPrice_kline",     // 事件类型
  "E": 123456789,   // 事件时间
  "ps": "BTCUSD",    // 标的交易对
  "k": {
    "t": 123400000, // 这根K线的起始时间
    "T": 123460000, // 这根K线的结束时间
    "s": "BTCUSD_200930",  // 交易对
    "i": "1m",      // K线间隔
    "f": 100,       // 这根K线期间第一笔数据Id
    "L": 200,       // 这根K线期间末一笔数据Id
    "o": "0.0010",  // 这根K线期间第一笔成交价
    "c": "0.0020",  // 这根K线期间末一笔成交价
    "h": "0.0025",  // 这根K线期间最高成交价
    "l": "0.0015",  // 这根K线期间最低成交价
    "v": "0",    	// 无意义字段
    "n": 8,       	// 这根K线更新期间数据数量
    "x": false,     // 这根K线是否完结（是否已经开始下一根K线）
    "q": "0",  		// 无意义字段
    "V": "0",     	// 无意义字段
    "Q": "0",   		// 无意义字段
    "B": "0"   		// 无意义字段
  }
}
```

K线stream逐秒推送所请求的K线种类(最新一根K线)的更新。

**订阅Kline需要提供间隔参数，最短为分钟线，最长为月线。支持以下间隔:**

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

**Stream Name:**    
``<symbol>@markPriceKline_<interval>``

**Update Speed:** 250ms





## 按Symbol的精简Ticker

> **Payload:**

```javascript
  {
    "e": "24hrMiniTicker",  // 事件类型
    "E": 123456789,         // 事件时间（毫秒）
    "s": "BTCUSD_200930",  // 交易对
    "ps": "BTCUSD",  		 // 标的交易对
    "c": "0.0025",          // 最新成交价格
    "o": "0.0010",          // 24小时前开始第一笔成交价格
    "h": "0.0025",          // 24小时内最高成交价
    "l": "0.0010",          // 24小时内最低成交加
    "v": "10000",           // 成交量
    "q": "18"               // 成交额
  }
```

按Symbol刷新的24小时精简ticker信息.

**Stream Name:**     
``<symbol>@miniTicker`

**Update Speed:** 500ms


## 全市场的精简Ticker

> **Payload:**

```javascript
[  
  {
    "e": "24hrMiniTicker",  // 事件类型
    "E": 123456789,         // 事件时间（毫秒）
    "s": "BTCUSD_200930",   // 交易对
    "ps": "BTCUSD",  		 // 标的交易对
    "c": "0.0025",          // 最新成交价格
    "o": "0.0010",          // 24小时前开始第一笔成交价格
    "h": "0.0025",          // 24小时内最高成交价
    "l": "0.0010",          // 24小时内最低成交加
    "v": "10000",           // 成交量
    "q": "18"               // 成交额
  }
]
```

所有symbol24小时精简ticker信息.需要注意的是，只有发生变化的tiker更新才会被推送。

**Stream Name:**     
`!miniTicker@arr`

**Update Speed:** 1000ms



## 按Symbol的完整Ticker


> **Payload:**

```javascript
{
  "e": "24hrTicker",  // 事件类型
  "E": 123456789,     // 事件时间
  "s": "BTCUSD_200930",      // 交易对
  "ps": "BTCUSD",  		 // 标的交易对
  "p": "0.0015",      // 24小时价格变化
  "P": "250.00",      // 24小时价格变化（百分比）
  "w": "0.0018",      // 平均价格
  "c": "0.0025",      // 最新成交价格
  "Q": "10",          // 最新成交价格上的成交量
  "o": "0.0010",      // 24小时内第一比成交的价格
  "h": "0.0025",      // 24小时内最高成交价
  "l": "0.0010",      // 24小时内最低成交加
  "v": "10000",       // 24小时内成交量
  "q": "18",          // 24小时内成交额
  "O": 0,             // 统计开始时间
  "C": 86400000,      // 统计关闭时间
  "F": 0,             // 24小时内第一笔成交交易ID
  "L": 18150,         // 24小时内最后一笔成交交易ID
  "n": 18151          // 24小时内成交数
}
```

按Symbol刷新的24小时完整ticker信息.

**Stream Name:**     
``<symbol>@ticker``

**Update Speed:** 500ms


## 全市场的完整Ticker


> **Payload:**

```javascript
[
	{
	  "e": "24hrTicker",  // 事件类型
	  "E": 123456789,     // 事件时间
	  "s": "BTCUSD_200930",      // 交易对
	  "ps": "BTCUSD",  		 // 标的交易对
	  "p": "0.0015",      // 24小时价格变化
	  "P": "250.00",      // 24小时价格变化（百分比）
	  "w": "0.0018",      // 平均价格
	  "c": "0.0025",      // 最新成交价格
	  "Q": "10",          // 最新成交价格上的成交量
	  "o": "0.0010",      // 24小时内第一比成交的价格
	  "h": "0.0025",      // 24小时内最高成交价
	  "l": "0.0010",      // 24小时内最低成交加
	  "v": "10000",       // 24小时内成交量
	  "q": "18",          // 24小时内成交额
	  "O": 0,             // 统计开始时间
	  "C": 86400000,      // 统计关闭时间
	  "F": 0,             // 24小时内第一笔成交交易ID
	  "L": 18150,         // 24小时内最后一笔成交交易ID
	  "n": 18151          // 24小时内成交数
	}
]	
```

所有symbol 24小时完整ticker信息.需要注意的是，只有发生变化的tiker更新才会被推送。

**Stream 名称:**     
``!ticker@arr``

**Update Speed:** 1000ms


## 按Symbol的最优挂单信息

> **Payload:**

```javascript
{
  "u":400900217,     // 更新ID
  "s":"BTCUSD_200930",     // 交易对
  "ps": "BTCUSD",  		 // 标的交易对
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



##强平订单

> **Payload:**

```javascript
{

	"e":"forceOrder",                   // 事件类型
	"E":1568014460893,                  // 事件时间
	"o":{
	
		"s":"BTCUSD_200930",            	// 交易对
		"ps": "BTCUSD",  		 			// 标的交易对
		"S":"SELL",                      // 订单方向
		"o":"LIMIT",                     // 订单类型
		"f":"IOC",                       // 有效方式
		"q":"0.014",                     // 订单数量
		"p":"9910",                      // 订单价格
		"ap":"9910",                     // 平均价格
		"X":"FILLED",                    // 订单状态
		"l":"0.014",                     // 订单最近成交数量
		"z":"0.014",                     // 订单累计成交数量
		"T":1568014460893,          	 // 交易时间
	
	}

}
```

推送特定`symbol`的强平订单信息

**Stream Name:**  ``<symbol>@forceOrder``

**Update Speed:** 实时


##全市场强平订单

> **Payload:**

```javascript
{

	"e":"forceOrder",                   // 事件类型
	"E":1568014460893,                  // 事件时间
	"o":{
	
		"s":"BTCUSD_200930",            	// 交易对
		"ps": "BTCUSD",  		 			// 标的交易对
		"S":"SELL",                      // 订单方向
		"o":"LIMIT",                     // 订单类型
		"f":"IOC",                       // 有效方式
		"q":"0.014",                     // 订单数量
		"p":"9910",                      // 订单价格
		"ap":"9910",                     // 平均价格
		"X":"FILLED",                    // 订单状态
		"l":"0.014",                     // 订单最近成交数量
		"z":"0.014",                     // 订单累计成交数量
		"T":1568014460893,          	 // 交易时间
	
	}

}
```

推送全市场强平订单信息

**Stream Name:** ``!forceOrder@arr``

**Update Speed:** 实时






## 有限档深度信息

> **Payload:**

```javascript
{
  "e": "depthUpdate", 			// 事件类型
  "E": 1571889248277, 			// 事件时间
  "T": 1571889248276, 			// 交易时间
  "s": "BTCUSD_200930",			// 交易对
  "ps": "BTCUSD",  		 		// 标的交易对
  "U": 390497796,
  "u": 390497878,
  "pu": 390497794,
  "b": [          				// 买方
    [
      "7403.89",  				// 价格
      "0.002"     				// 数量
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
  "a": [          				// 卖方
    [
      "7405.96",  				// 价格
      "3.340"     				// 数量
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

推送有限档深度信息。levels表示几档买卖单信息, 可选 5/10/20档

**Stream Names:** `<symbol>@depth<levels>` 或 `<symbol>@depth<levels>@500ms` 或 `<symbol>@depth<levels>@100ms`.  

**Update Speed:** 250ms 或 500ms 或 100ms




## 增量深度信息stream

> **Payload:**

```javascript
{
  "e": "depthUpdate", // 事件类型
  "E": 123456789,     // 事件时间
  "T": 123456788,     // 撮合时间
  "s": "BTCUSD_200930",      // 交易对
  "ps": "BTCUSD",  		 // 标的交易对
  "U": 157,           // 从上次推送至今新增的第一个 update Id
  "u": 160,           // 从上次推送至今新增的最后一个 update Id
  "pu": 149,          // 上次推送的最后一个update Id（即上条消息的‘u’）
  "b": [              // 变动的买单深度
    [
      "0.0024",       // 价格
      "10"           // 数量
    ]
  ],
  "a": [              // 变动的卖单深度
    [
      "0.0026",       // 价格
      "100"          // 数量
    ]
  ]
}
```

orderbook的变化部分，推送间隔250毫秒,500毫秒，100毫秒或实时更新（如有刷新）

**Stream 名称:**     
``<symbol>@depth`` OR ``<symbol>@depth@500ms`` OR ``<symbol>@depth@100ms`` OR ``<symbol>@depth@0ms``

**Update Speed:** 250ms 或 500ms 或 100ms 或 实时




## 如何正确在本地维护一个orderbook副本
1. 订阅 **wss://dstream.binance.com/stream?streams=btcusd_200930@depth**
2. 开始缓存收到的更新。同一个价位，后收到的更新覆盖前面的。
3. 访问Rest接口 **https://fapi.binance.com/dapi/v1/depth?symbol=BTCUSD_200930&limit=1000**获得一个1000档的深度快照
4. 将目前缓存到的信息中`u`< 步骤3中获取到的快照中的`lastUpdateId`的部分丢弃(丢弃更早的信息，已经过期)。
5. 将深度快照中的内容更新到本地orderbook副本中，并从websocket接收到的第一个`U` <= `lastUpdateId` **且** `u` >= `lastUpdateId` 的event开始继续更新本地副本。
6. 每一个新event的`pu`应该等于上一个event的`u`，否则可能出现了丢包，请从step3重新进行初始化。
7. 每一个event中的挂单量代表这个价格目前的挂单量**绝对值**，而不是相对变化。
8. 如果某个价格对应的挂单量为0，表示该价位的挂单已经撤单或者被吃，应该移除这个价位。






# 账户和交易接口


## 更改持仓模式（TRADE）

> **响应:**

```javascript
{
	"code": 200,
	"msg": "success"
}
```

``
POST /dapi/v1/positionSide/dual (HMAC SHA256)
``

变换用户在 ***所有symbol*** 合约上的持仓模式：双向持仓或单向持仓。   

**权重:**
1

**参数:**

   名称    |  类型  | 是否必需 |       描述
---------- | ------ | -------- | -----------------
dualSidePosition | STRING   | YES      | "true": 双向持仓模式；"false": 单向持仓模式
recvWindow | LONG   | NO       |
timestamp  | LONG   | YES      |



## 下单 (TRADE)


> **响应:**

```javascript
{
 	"clientOrderId": "testOrder", // 用户自定义的订单号
 	"cumQuote": "0", // 成交金额
 	"executedQty": "0", // 成交数量
 	"orderId": 22542179, // 系统订单号
 	"avgPrice": "0.00000",		// 平均成交价
 	"origQty": "10", // 原始委托数量
 	"price": "0", // 委托价格
 	"reduceOnly": false, // 仅减仓
 	"closePosition": false,   // 是否条件全平仓
 	"side": "SELL", // 买卖方向
 	"positionSide": "SHORT", // 持仓方向
 	"status": "NEW", // 订单状态
 	"stopPrice": "0", // 触发价，对`TRAILING_STOP_MARKET`无效
 	"symbol": "BTCUSD_200930", // 交易对
 	"pair": "BTCUSD",	// 标的交易对
 	"timeInForce": "GTC", // 有效方法
 	"type": "TRAILING_STOP_MARKET", // 订单类型
 	"activatePrice": "9020", // 跟踪止损激活价格, 仅`TRAILING_STOP_MARKET` 订单返回此字段
  	"priceRate": "0.3",	// 跟踪止损回调比例, 仅`TRAILING_STOP_MARKET` 订单返回此字段
 	"updateTime": 1566818724722, // 更新时间
 	"workingType": "CONTRACT_PRICE" // 条件价格触发类型
 }
```

``
POST /dapi/v1/order  (HMAC SHA256)
``

**权重:**
1

**参数:**

名称              |  类型   | 是否必需   |                                              描述
---------------- | ------- | -------- | -----------------------------------------------------------------------------------------------
symbol           | STRING  | YES      | 交易对
side             | ENUM    | YES      | 买卖方向 `SELL`, `BUY`
positionSide     | ENUM	    | NO       | 持仓方向，单向持仓模式下非必填，默认且仅可填`BOTH`;在双向持仓模式下必填,且仅可选择 `LONG` 或 `SHORT`   
type             | ENUM    | YES      | 订单类型 `LIMIT`, `MARKET`, `STOP`, `TAKE_PROFIT`, `STOP_MARKET`, `TAKE_PROFIT_MARKET`, `TRAILING_STOP_MARKET`
reduceOnly       | STRING  | NO       | `true`, `false`; 非双开模式下默认`false`；双开模式下不接受此参数； 使用`closePosition`不支持此参数。
quantity         | DECIMAL | NO      | 下单数量,使用`closePosition`不支持此参数。
price            | DECIMAL | NO       | 委托价格
newClientOrderId | STRING  | NO       | 用户自定义的订单号，不可以重复出现在挂单中。如空缺系统会自动赋值
stopPrice        | DECIMAL | NO       | 触发价, 仅 `STOP`, `STOP_MARKET`, `TAKE_PROFIT`, `TAKE_PROFIT_MARKET` 需要此参数
closePosition    | STRING  | NO       | `true`, `false`；触发后全部平仓，仅支持`STOP_MARKET`和`TAKE_PROFIT_MARKET`；不与`quantity`合用；自带只平仓效果，不与`reduceOnly` 合用
activationPrice  | DECIMAL | NO       | 追踪止损激活价格，仅`TRAILING_STOP_MARKET` 需要此参数, 默认为下单当前市场价格（支持不同`workingType`)
callbackRate     | DECIMAL | NO       | 追踪止损回调比例，可取值范围[0.1, 4],其中 1代表1% ,仅`TRAILING_STOP_MARKET` 需要此参数
timeInForce      | ENUM    | NO       | 有效方法
workingType      | ENUM    | NO       | stopPrice 触发类型: `MARK_PRICE`(标记价格), `CONTRACT_PRICE`(合约最新价). 默认 `CONTRACT_PRICE`
newOrderRespType | ENUM    | NO       | "ACK", "RESULT", 默认 "ACK"
recvWindow       | LONG    | NO       |
timestamp        | LONG    | YES      |

根据 order `type`的不同，某些参数强制要求，具体如下:

Type                 |           强制要求的参数
----------------------------------- | ----------------------------------
`LIMIT`                             | `timeInForce`, `quantity`, `price`
`MARKET`                            | `quantity`
`STOP`, `TAKE_PROFIT`               | `quantity`,  `price`, `stopPrice`
`STOP_MARKET`, `TAKE_PROFIT_MARKET` | `stopPrice`
`TRAILING_STOP_MARKET`              | `callbackRate`


* 条件单的触发必须:

	* `STOP`, `STOP_MARKET` 止损单:
		* 买入: 最新合约价格/标记价格高于等于触发价`stopPrice`
		* 卖出: 最新合约价格/标记价格低于等于触发价`stopPrice`
	* `TAKE_PROFIT`, `TAKE_PROFIT_MARKET` 止盈单:
		* 买入: 最新合约价格/标记价格低于等于触发价`stopPrice`
		* 卖出: 最新合约价格/标记价格高于等于触发价`stopPrice`

	* `TRAILING_STOP_MARKET` 跟踪止损单:
		* 买入: 当合约价格/标记价格区间最低价格低于激活价格`activationPrice`,且最新合约价格/标记价高于等于最低价设定回调幅度。
		* 卖出: 当合约价格/标记价格区间最高价格高于激活价格`activationPrice`,且最新合约价格/标记价低于等于最高价设定回调幅度。


* `TRAILING_STOP_MARKET` 跟踪止损单如果遇到报错 ``{"code": -2021, "msg": "Order would immediately trigger."}``    
表示订单不满足以下条件:
	* 买入: 指定的`activationPrice` 必须小于 latest price
	* 卖出: 指定的`activationPrice` 必须大于 latest price

* `newOrderRespType` 如果传 `RESULT`:
	* `MARKET` 订单将直接返回成交结果；
	* 配合使用特殊 `timeInForce` 的 `LIMIT` 订单将直接返回成交或过期拒绝结果。

* `STOP_MARKET`, `TAKE_PROFIT_MARKET` 配合 `closePosition`=`true`:
	* 条件单触发依照上述条件单触发逻辑
	* 条件触发后，平掉当时持有所有多头仓位（若为卖单）或当时持有所有空头仓位（若为买单）
	* 不支持 `quantity` 参数
	* 自带只平仓属性，不支持`reduceOnly`参数
	* 双开模式下,`LONG`方向上不支持`BUY`; `SHORT` 方向上不支持`SELL`


## 测试下单接口 (TRADE)


> **响应:**

```javascript
{}
```

``
POST /dapi/v1/order/test (HMAC SHA256)
``

用于测试订单请求，但不会提交到撮合引擎

**权重:**
1

**参数:**

参考 `POST /dapi/v1/order`



## 查询订单 (USER_DATA)


> **响应:**

```javascript
{
  	"avgPrice": "0.00000",				// 平均成交价
  	"clientOrderId": "abc",				// 用户自定义的订单号
  	"cumQuote": "0",					// 成交金额
  	"executedQty": "0",					// 成交数量
  	"orderId": 1573346959,				// 系统订单号
  	"origQty": "0.40",					// 原始委托数量
  	"origType": "TRAILING_STOP_MARKET",	// 触发前订单类型
  	"price": "0",						// 委托价格
  	"reduceOnly": false,				// 是否仅减仓
  	"side": "BUY",						// 买卖方向
  	"positionSide": "SHORT", 			// 持仓方向
  	"status": "NEW",					// 订单状态
  	"stopPrice": "9300",					// 触发价，对`TRAILING_STOP_MARKET`无效
  	"closePosition": false,   // 是否条件全平仓
  	"symbol": "BTCUSD_200930",				// 交易对
  	"pair": "BTCUSD",	// 标的交易对
  	"time": 1579276756075,				// 订单时间
  	"timeInForce": "GTC",				// 有效方法
  	"type": "TRAILING_STOP_MARKET",		// 订单类型
  	"activatePrice": "9020",			// 跟踪止损激活价格, 仅`TRAILING_STOP_MARKET` 订单返回此字段
  	"priceRate": "0.3",					// 跟踪止损回调比例, 仅`TRAILING_STOP_MARKET` 订单返回此字段
  	"updateTime": 1579276756075,		// 更新时间
  	"workingType": "CONTRACT_PRICE"		// 条件价格触发类型
}
```

``
GET /dapi/v1/order (HMAC SHA256)
``

查询订单状态

**权重:**
1

**参数:**

名称        |  类型  | 是否必需 | 描述
----------------- | ------ | -------- | ----
symbol            | STRING | YES      | 交易对
orderId           | LONG   | NO       | 系统订单号
origClientOrderId | STRING | NO       | 用户自定义的订单号
recvWindow        | LONG   | NO       |
timestamp         | LONG   | YES      |

注意:

* 至少需要发送 `orderId` 与 `origClientOrderId`中的一个


## 撤销订单 (TRADE)

> **响应:**

```javascript
{
 	"clientOrderId": "myOrder1", // 用户自定义的订单号
 	"cumQty": "0",
 	"cumQuote": "0", // 成交金额
 	"executedQty": "0", // 成交数量
 	"orderId": 283194212, // 系统订单号
 	"origQty": "11", // 原始委托数量
 	"price": "0", // 委托价格
	"reduceOnly": false, // 仅减仓
	"side": "BUY", // 买卖方向
	"positionSide": "SHORT", // 持仓方向
 	"status": "CANCELED", // 订单状态
 	"stopPrice": "9300", // 触发价，对`TRAILING_STOP_MARKET`无效
 	"closePosition": false,   // 是否条件全平仓
 	"symbol": "BTCUSD_200930", // 交易对
 	"pair": "BTCUSD",	// 标的交易对
 	"timeInForce": "GTC", // 有效方法
 	"origType": "TRAILING_STOP_MARKET",	// 触发前订单类型
 	"type": "TRAILING_STOP_MARKET", // 订单类型
 	"activatePrice": "9020", // 跟踪止损激活价格, 仅`TRAILING_STOP_MARKET` 订单返回此字段
  	"priceRate": "0.3",	// 跟踪止损回调比例, 仅`TRAILING_STOP_MARKET` 订单返回此字段
 	"updateTime": 1571110484038, // 更新时间
 	"workingType": "CONTRACT_PRICE" // 条件价格触发类型
}
```

``
DELETE /dapi/v1/order  (HMAC SHA256)
``

**权重:**
1

**Parameters:**

名称               |  类型   | 是否必需  |        描述
----------------- | ------ | -------- | ------------------
symbol            | STRING | YES      | 交易对
orderId           | LONG   | NO       | 系统订单号
origClientOrderId | STRING | NO       | 用户自定义的订单号
recvWindow        | LONG   | NO       |
timestamp         | LONG   | YES      |

`orderId` 与 `origClientOrderId` 必须至少发送一个


## 撤销全部订单 (TRADE)

> **响应:**

```javascript
{
	"code": "200", 
	"msg": "The operation of cancel all open order is done."
}
```

``
DELETE /dapi/v1/allOpenOrders  (HMAC SHA256)
``

**权重:**
1

**Parameters:**

   名称    |  类型  | 是否必需 |  描述
---------- | ------ | -------- | ------
symbol     | STRING | YES      | 交易对
recvWindow | LONG   | NO       |
timestamp  | LONG   | YES      |




## 批量撤销订单 (TRADE)

> **响应:**

```javascript
[
	{
	 	"clientOrderId": "myOrder1", // 用户自定义的订单号
	 	"cumQty": "0",
	 	"cumQuote": "0", // 成交金额
	 	"executedQty": "0", // 成交数量
	 	"orderId": 283194212, // 系统订单号
	 	"origQty": "11", // 原始委托数量
	 	"price": "0", // 委托价格
		"reduceOnly": false, // 仅减仓
		"side": "BUY", // 买卖方向
		"positionSide": "SHORT", // 持仓方向
	 	"status": "CANCELED", // 订单状态
	 	"stopPrice": "9300", // 触发价，对`TRAILING_STOP_MARKET`无效
	 	"closePosition": false,   // 是否条件全平仓
	 	"symbol": "BTCUSD_200930", // 交易对
	 	"pair": "BTCUSD",	// 标的交易对
	 	"timeInForce": "GTC", // 有效方法
	 	"origType": "TRAILING_STOP_MARKET", // 触发前订单类型
 		"type": "TRAILING_STOP_MARKET", // 订单类型
	 	"activatePrice": "9020", // 跟踪止损激活价格, 仅`TRAILING_STOP_MARKET` 订单返回此字段
  		"priceRate": "0.3",	// 跟踪止损回调比例, 仅`TRAILING_STOP_MARKET` 订单返回此字段
	 	"updateTime": 1571110484038 // 更新时间
	},
	{
		"code": -2011,
		"msg": "Unknown order sent."
	}
]
```

``
DELETE /dapi/v1/batchOrders  (HMAC SHA256)
``

**权重:**
1

**Parameters:**

  名称          |      类型      | 是否必需 |       描述
--------------------- | -------------- | -------- | ----------------
symbol                | STRING         | YES      | 交易对
orderIdList           | LIST\<LONG\>   | NO       | 系统订单号, 最多支持10个订单 <br/> 比如`[1234567,2345678]`
origClientOrderIdList | LIST\<STRING\> | NO       | 用户自定义的订单号, 最多支持10个订单 <br/> 比如`["my_id_1","my_id_2"]` 需要encode双引号。逗号后面没有空格。
recvWindow            | LONG           | NO       |
timestamp             | LONG           | YES      |

`orderIdList` 与 `origClientOrderIdList` 必须至少发送一个，不可同时发送



## 查询当前挂单 (USER_DATA)

> **响应:**

```javascript

{
  	"avgPrice": "0.00000",				// 平均成交价
  	"clientOrderId": "abc",				// 用户自定义的订单号
  	"cumQuote": "0",						// 成交金额
  	"executedQty": "0",					// 成交数量
  	"orderId": 1917641,					// 系统订单号
  	"origQty": "0.40",					// 原始委托数量
  	"origType": "TRAILING_STOP_MARKET",	// 触发前订单类型
  	"price": "0",					// 委托价格
  	"reduceOnly": false,				// 是否仅减仓
  	"side": "BUY",						// 买卖方向
  	"status": "NEW",					// 订单状态
  	"stopPrice": "9300",					// 触发价，对`TRAILING_STOP_MARKET`无效
  	"closePosition": false,   // 是否条件全平仓
  	"symbol": "BTCUSD_200930",				// 交易对
  	"pair": "BTCUSD",	// 标的交易对
  	"time": 1579276756075,				// 订单时间
  	"timeInForce": "GTC",				// 有效方法
  	"type": "TRAILING_STOP_MARKET",		// 订单类型
  	"activatePrice": "9020", // 跟踪止损激活价格, 仅`TRAILING_STOP_MARKET` 订单返回此字段
  	"priceRate": "0.3",	// 跟踪止损回调比例, 仅`TRAILING_STOP_MARKET` 订单返回此字段
  	"updateTime": 1579276756075,		// 更新时间
  	"workingType": "CONTRACT_PRICE"		// 条件价格触发类型
}
```

``
GET /dapi/v1/openOrder  (HMAC SHA256)
``

请小心使用不带symbol参数的调用

**权重: 1***


**参数:**

   名称    |  类型  | 是否必需 |  描述
---------- | ------ | -------- | ------
symbol | STRING | YES | 交易对
orderId | LONG | NO | 系统订单号
origClientOrderId | STRING | NO | 用户自定义的订单号
recvWindow | LONG   | NO       |
timestamp  | LONG   | YES      |

* `orderId` 与 `origClientOrderId` 中的一个为必填参数
* 查询的订单如果已经成交或取消，将返回报错"Order does not exist."


## 查看当前全部挂单 (USER_DATA)

> **响应:**

```javascript
[
  {
  	"avgPrice": "0.00000",				// 平均成交价
  	"clientOrderId": "abc",				// 用户自定义的订单号
  	"cumQuote": "0",						// 成交金额
  	"executedQty": "0",					// 成交数量
  	"orderId": 1917641,					// 系统订单号
  	"origQty": "0.40",					// 原始委托数量
  	"origType": "TRAILING_STOP_MARKET",	// 触发前订单类型
  	"price": "0",					// 委托价格
  	"reduceOnly": false,				// 是否仅减仓
  	"side": "BUY",						// 买卖方向
  	"positionSide": "SHORT", // 持仓方向
  	"status": "NEW",					// 订单状态
  	"stopPrice": "9300",					// 触发价，对`TRAILING_STOP_MARKET`无效
  	"closePosition": false,   // 是否条件全平仓
  	"symbol": "BTCUSD_200930",				// 交易对
  	"pair": "BTCUSD",	// 标的交易对
  	"time": 1579276756075,				// 订单时间
  	"timeInForce": "GTC",				// 有效方法
  	"type": "TRAILING_STOP_MARKET",		// 订单类型
  	"activatePrice": "9020", // 跟踪止损激活价格, 仅`TRAILING_STOP_MARKET` 订单返回此字段
  	"priceRate": "0.3",	// 跟踪止损回调比例, 仅`TRAILING_STOP_MARKET` 订单返回此字段
  	"updateTime": 1579276756075,		// 更新时间
  	"workingType": "CONTRACT_PRICE"		// 条件价格触发类型
  }
]
```

``
GET /dapi/v1/openOrders  (HMAC SHA256)
``

请小心使用不带symbol参数的调用

**权重:**

- 带symbol **1**
- 带pair **5**


**参数:**

   名称    |  类型  | 是否必需 |  描述
---------- | ------ | -------- | ------
symbol     | STRING | NO       | 交易对
pair 		 | STRING | NO		 | 标的交易对
recvWindow | LONG   | NO       |
timestamp  | LONG   | YES      |

* symbol或pair参数，必须传一个



## 查询所有订单（包括历史订单） (USER_DATA)


> **响应:**

```javascript
[
  {
   	"avgPrice": "0.00000",				// 平均成交价
  	"clientOrderId": "abc",				// 用户自定义的订单号
  	"cumQuote": "0",						// 成交金额
  	"executedQty": "0",					// 成交数量
  	"orderId": 1917641,					// 系统订单号
  	"origQty": "0.40",					// 原始委托数量
  	"origType": "TRAILING_STOP_MARKET",	// 触发前订单类型
  	"price": "0",					// 委托价格
  	"reduceOnly": false,				// 是否仅减仓
  	"side": "BUY",						// 买卖方向
  	"positionSide": "SHORT", 			// 持仓方向
  	"status": "NEW",					// 订单状态
  	"stopPrice": "9300",					// 触发价，对`TRAILING_STOP_MARKET`无效
  	"closePosition": false,   // 是否条件全平仓
  	"symbol": "BTCUSD_200930",				// 交易对
  	"pair": "BTCUSD",	// 标的交易对
  	"time": 1579276756075,				// 订单时间
  	"timeInForce": "GTC",				// 有效方法
  	"type": "TRAILING_STOP_MARKET",		// 订单类型
  	"activatePrice": "9020", // 跟踪止损激活价格, 仅`TRAILING_STOP_MARKET` 订单返回此字段
  	"priceRate": "0.3",	// 跟踪止损回调比例, 仅`TRAILING_STOP_MARKET` 订单返回此字段
  	"updateTime": 1579276756075,		// 更新时间
  	"workingType": "CONTRACT_PRICE"		// 条件价格触发类型
  }
]
```

``
GET /dapi/v1/allOrders (HMAC SHA256)
``

**权重:**    
传symbol **20**    
传pairs **40**

**Parameters:**

   名称    |  类型  | 是否必需 |                      描述
---------- | ------ | -------- | -----------------------------------------------
symbol     | STRING | NO       | 交易对
pair		 | STRING | NO 		 | 标的交易对
orderId    | LONG   | NO       | 只返回此orderID及之后的订单，缺省返回最近的订单, 仅支持配合symbol使用
startTime  | LONG   | NO       | 起始时间
endTime    | LONG   | NO       | 结束时间
limit      | INT    | NO       | 返回的结果集数量 默认值:50 最大值:100
recvWindow | LONG   | NO       |
timestamp  | LONG   | YES      |

* symbol 或 pair 必须传一个，不能同时传



## 账户信息 (USER_DATA)

> **响应:**

```javascript

{
	"assets": [	// 资产内容
		{
			"asset": "BTC", // 资产名
		   	"initialMargin": "0.33683000", // 起始保证金
		   	"maintMargin": "0.02695000", // 维持保证金
		   	"marginBalance": "8.74947592", // 保证金余额
		   	"maxWithdrawAmount": "8.41264592", // 最大可提款金额
		   	"openOrderInitialMargin": "0.00000000",	// 挂单起始保证金
		   	"positionInitialMargin": "0.33683000", // 持仓起始保证金
		   	"unrealizedProfit": "-0.44537584", // 持仓未实现盈亏
		   	"walletBalance": "9.19485176" // 账户余额
		}
	 ],
	 "canDeposit": true, // 是否可以入金
	 "canTrade": true, // 是否可以交易
	 "canWithdraw": true, // 是否可以出金
	 "feeTier": 2, // 手续费等级
	 "positions": [ // 头寸
		 {
		 	"isolated": false, // 是否是逐仓模式
		 	"leverage": "20", // 杠杆倍率
		 	"initialMargin": "0.33683", // 起始保证金
		  	"maintMargin": "0.02695", // 维持保证金
		   	"openOrderInitialMargin": "0.00000", // 挂单起始保证金
		   	"positionInitialMargin": "0.33683", // 持仓起始保证金
		   	"symbol": "BTCUSD_200930", // 交易对 
		   	"unrealizedProfit": "-0.44537584", // 持仓未实现盈亏
		   	"positionSide": "BOTH", // 持仓方向
		 },
		 {
		 	"isolated": false, // 是否是逐仓模式
		 	"leverage": "20", // 杠杆倍率
		 	"initialMargin": "0.00000", // 起始保证金
		  	"maintMargin": "0.00000", // 维持保证金
		   	"openOrderInitialMargin": "0.00000", // 挂单起始保证金
		   	"positionInitialMargin": "0.00000", // 持仓起始保证金
		   	"symbol": "BTCUSD_200930", // 交易对 
		   	"unrealizedProfit": "0.00000000", // 持仓未实现盈亏
		   	"positionSide": "LONG", // 持仓方向
		 },
		 {
		 	"isolated": false, // 是否是逐仓模式
		 	"leverage": "20", // 杠杆倍率
		 	"initialMargin": "0.00000", // 起始保证金
		  	"maintMargin": "0.00000", // 维持保证金
		   	"openOrderInitialMargin": "0.00000", // 挂单起始保证金
		   	"positionInitialMargin": "0.00000", // 持仓起始保证金
		   	"symbol": "BTCUSD_200930", // 交易对 
		   	"unrealizedProfit": "0.00000000", // 持仓未实现盈亏
		   	"positionSide": "SHORT", // 持仓方向
		 }
	 ],
	 // 以下跨资产总额不需要了
	 //"totalInitialMargin": "0.33683000", // 起始保证金总额（存在逐仓请忽略）
	 //"totalMaintMargin": "0.02695000", // 维持保证金总额
	 //"totalMarginBalance": "8.74947592", // 保证金总余额
	 //"totalOpenOrderInitialMargin": "0.00000000", // 挂单起始保证金总额
	 //"totalPositionInitialMargin": "0.33683000", // 持仓起始保证金总额
	 //"totalUnrealizedProfit": "-0.44537584", // 持仓未实现盈亏总额
	 //"totalWalletBalance": "9.19485176", // 账户总余额
	 // 以上跨资产总额不需要了
	 "updateTime": 0
 }

```

``
GET /dapi/v1/account (HMAC SHA256)
``

**权重:**
5

**参数:**

   名称    | 类型 | 是否必需 | 描述
---------- | ---- | -------- | ----
recvWindow | LONG | NO       |
timestamp  | LONG | YES      |




## 调整开仓杠杆 (TRADE)

> **响应:**

```javascript
{
 	"leverage": 21,	// 杠杆倍数
 	"maxNotionalValue": "1000000", // 当前杠杆倍数下允许的最大名义价值
 	"symbol": "BTCUSD_200930"	// 交易对
}
```

``
POST /dapi/v1/leverage (HMAC SHA256)
``

调整用户在指定symbol合约的开仓杠杆。不同持仓方向上使用相同杠杆倍数，共享允许的最大名义价值。

**权重:**
1

**参数:**

   名称    |  类型  | 是否必需 |     描述
---------- | ------ | -------- | ------------
symbol     | STRING | YES      | 交易对
leverage   | INT    | YES      | 目标杠杆倍数
recvWindow | LONG   | NO       |
timestamp  | LONG   | YES      |


## 变换逐全仓模式 (TRADE)

> **响应:**

```javascript
{
	"code": 200,
	"msg": "success"
}
```

``
POST /dapi/v1/marginType (HMAC SHA256)
``

变换用户在指定symbol合约上的保证金模式：逐仓或全仓。   
不同持仓方向上使用相同的保证金模式。双向持仓模式下的逐仓，`LONG` 与 `SHORT`使用独立的逐仓仓位。

**权重:**
1

**参数:**

   名称    |  类型  | 是否必需 |       描述
---------- | ------ | -------- | -----------------
symbol     | STRING | YES      | 交易对
marginType | ENUM   | YES      | 保证金模式 ISOLATED(逐仓), CROSSED(全仓)
recvWindow | LONG   | NO       |
timestamp  | LONG   | YES      |


## 调整逐仓保证金 (TRADE)

> **响应:**

```javascript
{
	"amount": 100.0,
  	"code": 200,
  	"msg": "Successfully modify position margin.",
  	"type": 1
}
```

``
POST /dapi/v1/positionMargin (HMAC SHA256)
``

针对逐仓模式下的仓位，调整其逐仓保证金资金。

**权重:**
1

**参数:**

   名称    |  类型   | 是否必需 |                 描述
---------- | ------- | -------- | ------------------------------------
symbol     | STRING  | YES      | 交易对
positionSide| ENUM   | NO		  | 持仓方向，单向持仓模式下非必填，默认且仅可填`BOTH`;在双向持仓模式下必填,且仅可选择 `LONG` 或 `SHORT` 
amount     | DECIMAL | YES      | 保证金资金
type       | INT     | YES      | 调整方向 1: 增加逐仓保证金，2: 减少逐仓保证金
recvWindow | LONG    | NO       |
timestamp  | LONG    | YES      |

* 只针对逐仓symbol 与 positionSide(如有)


## 逐仓保证金变动历史 (TRADE)

> **响应:**

```javascript
[
	{
		"amount": "23.36332311", // 数量
	  	"asset": "BTC", // 资产
	  	"symbol": "BTCUSD_200930", // 交易对
	  	"time": 1578047897183, // 时间
	  	"type": 1,  // 调整方向
	  	"positionSide": "BOTH"  // 持仓方向
	},
	{
		"amount": "100",
	  	"asset": "BTC",
	  	"symbol": "BTCUSD_200930",
	  	"time": 1578047900425,
	  	"type": 1，
	  	"positionSide": "LONG" 
	}
]
```

``
GET /dapi/v1/positionMargin/history (HMAC SHA256)
``

**权重:**
1

**参数:**

   名称    |  类型  | 是否必需 |                 描述
---------- | ------ | -------- | ------------------------------------
symbol     | STRING | YES      | 交易对
type       | INT    | NO       | 调整方向 1: 增加逐仓保证金，2: 减少逐仓保证金
startTime  | LONG   | NO       | 起始时间
endTime    | LONG   | NO       | 结束时间
limit      | INT    | NO       | 返回的结果集数量 默认值: 50
recvWindow | LONG   | NO       |
timestamp  | LONG   | YES      |


## 用户持仓风险

> **响应:**

```javascript
[
  	{
  		"entryPrice": "0.00000", // 开仓均价
  		"marginType": "isolated", // 逐仓模式或全仓模式
  		"isAutoAddMargin": "false",
  		"isolatedMargin": "0.00000000",	// 逐仓保证金
  		"leverage": "10", // 当前杠杆倍数
  		"liquidationPrice": "0", // 参考强平价格
  		"markPrice": "6679.50671178",	// 当前标记价格
  		"maxQty": "20000000", // 当前杠杆倍数允许的数量上限
  		"positionAmt": "0.000", // 头寸数量，符号代表多空方向, 正数为多，负数为空
  		"symbol": "BTCUSD_200930", // 交易对
  		"unRealizedProfit": "0.00000000", // 持仓未实现盈亏
  		"positionSide": "BOTH", // 持仓方向
  	},
  	{
  		"entryPrice": "6563.66500", // 开仓均价
  		"marginType": "isolated", // 逐仓模式或全仓模式
  		"isAutoAddMargin": "false",
  		"isolatedMargin": "15517.54150468", // 逐仓保证金
  		"leverage": "10", // 当前杠杆倍数
  		"liquidationPrice": "5930.78", // 参考强平价格
  		"markPrice": "6679.50671178",	// 当前标记价格
  		"maxQty": "20000000", // 当前杠杆倍数允许的名义价值上限
  		"positionAmt": "20.000", // 头寸数量，符号代表多空方向, 正数为多，负数为空
  		"symbol": "BTCUSD_200930", // 交易对
  		"unRealizedProfit": "2316.83423560" // 持仓未实现盈亏
  		"positionSide": "LONG", // 持仓方向
  	},
  	{
  		"entryPrice": "0.00000", // 开仓均价
  		"marginType": "isolated", // 逐仓模式或全仓模式
  		"isAutoAddMargin": "false",
  		"isolatedMargin": "5413.95799991", // 逐仓保证金
  		"leverage": "10", // 当前杠杆倍数
  		"liquidationPrice": "7189.95", // 参考强平价格
  		"markPrice": "6679.50671178",	// 当前标记价格
  		"maxQty": "20000000", // 当前杠杆倍数允许的名义价值上限
  		"positionAmt": "-10.000", // 头寸数量，符号代表多空方向, 正数为多，负数为空
  		"symbol": "BTCUSD_200930", // 交易对
  		"unRealizedProfit": "-1156.46711780" // 持仓未实现盈亏
  		"positionSide": "SHORT", // 持仓方向
  	}
  	
]
```

``
GET /dapi/v1/positionRisk (HMAC SHA256)
``

   名称    | 类型 | 是否必需 | 描述
---------- | ---- | -------- | ----
marginAsset | STRING | NO	  | 
pair     | STRING | NO     |
recvWindow | LONG | NO       |
timestamp  | LONG | YES      |

**注意**  

* marginAsset 和 pair 不要同时提供
* marginAsset 和 pair 均不提供则返回所有上市状态和结算中的symbol   
* 请与账户推送信息`ACCOUNT_UPDATE`配合使用，以满足您的及时性和准确性需求。



## 账户成交历史 (USER_DATA)

> **响应:**

```javascript
[
  {
  	"buyer": false,	// 是否是买方
  	"commission": "-0.07819010", // 手续费
  	"commissionAsset": "BTC", // 手续费计价单位
  	"id": 698759, // 交易ID
  	"maker": false,	// 是否是挂单方
  	"orderId": 25851813, // 订单编号
  	"price": "7819.01",	// 成交价
  	"qty": "0.002",	// 成交数量
  	"quoteQty": "15.63802",	// 成交额
  	"realizedPnl": "-0.91539999",	// 实现盈亏
  	"side": "SELL",	// 买卖方向
  	"positionSide": "SHORT",  // 持仓方向
  	"symbol": "BTCUSD_200930", // 交易对
  	"pair": "BTCUSD",	// 标的交易对
  	"time": 1569514978020 // 时间
  }
]
```

``
GET /dapi/v1/userTrades  (HMAC SHA256)
``

获取成交历史

**权重:**
传symbol **20**    
传pairs **40**

**参数:**

   名称    |  类型  | 是否必需 |                     描述
---------- | ------ | -------- | --------------------------------------------
symbol     | STRING | NO  		 | 交易对
pair		 | STRING | NO 		 | 标的交易对
startTime  | LONG   | NO       | 起始时间
endTime    | LONG   | NO       | 结束时间
fromId     | LONG   | NO       | 返回该fromId及之后的成交，缺省返回最近的成交, 仅支持配合symbol使用
limit      | INT    | NO       | 返回的结果集数量 默认值:50 最大值:100.
recvWindow | LONG   | NO       |
timestamp  | LONG   | YES      |

* symbol 或 pair 其中一个必传


## 获取账户损益资金流水(USER_DATA)

> **响应:**

```javascript
[
	{
    	"symbol": "BTCUSD_200930", // 交易对，仅针对涉及交易对的资金流
    	"incomeType": "TRANSFER",	// 资金流类型
    	"income": "-0.37500000", // 资金流数量，正数代表流入，负数代表流出
    	"asset": "BTC", // 资产内容
    	"info":"", // 备注信息，取决于流水类型
    	"time": 1570608000000, // 时间
	},
	{
   		"symbol": "BTCUSD_200930",
    	"incomeType": "COMMISSION", 
    	"income": "-0.01000000",
    	"asset": "BTC",
    	"info":"",
    	"time": 1570636800000,
	}
]
```

``
GET /dapi/v1/income (HMAC SHA256)
``

**权重:**
1

**参数:**

   名称    |  类型  | 是否必需 |                                              描述
---------- | ------ | -------- | -----------------------------------------------------------------------------------------------
symbol     | STRING | NO       | 交易对
incomeType | STRING | NO       | 收益类型 "TRANSFER"，"WELCOME_BONUS", "REALIZED_PNL", "COMMISSION", "INSURANCE_CLEAR", **"DELIVERED_SETTELMENT"**
startTime  | LONG   | NO       | 起始时间
endTime    | LONG   | NO       | 结束时间
limit      | INT    | NO       | 返回的结果集数量 默认值:100 最大值:1000
recvWindow | LONG   | NO       |
timestamp  | LONG   | YES      |

* 如果`incomeType`没有发送，返回所有类型账户损益资金流水。



# Websocket 账户信息推送


* 本篇所列出REST接口的baseurl **https://fapi.binance.com**
* 用于订阅账户数据的 `listenKey` 从创建时刻起有效期为60分钟
* 可以通过`PUT`一个`listenKey`延长60分钟有效期
* 可以通过`DELETE`一个 `listenKey` 立即关闭当前数据流，并使该`listenKey` 无效
* 在具有有效`listenKey`的帐户上执行`POST`将返回当前有效的`listenKey`并将其有效期延长60分钟
* 本篇所列出的websocket接口baseurl: **wss://dstream.binance.com**
* 订阅账户数据流的stream名称为 **/ws/\<listenKey\>**
* 每个链接有效期不超过24小时，请妥善处理断线重连。
* 账户数据流的消息**不保证**严格时间序; **请使用 E 字段进行排序**


## 生成listenKey (USER_STREAM)


> **响应:**

```javascript
{
  "listenKey": "pqia91ma19a5s61cv6a81va65sdf19v8a65a1a5s61cv6a81va65sdf19v8a65a1"
}
```

``
POST /dapi/v1/listenKey (HMAC SHA256)
``

创建一个新的user data stream，返回值为一个listenKey，即websocket订阅的stream名称。如果该帐户具有有效的`listenKey`，则将返回该`listenKey`并将其有效期延长60分钟。

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
recvWindow | LONG | NO |
timestamp | LONG | YES |


## 延长listenKey有效期 (USER_STREAM)


> **响应:**

```javascript
{}
```

``
PUT /dapi/v1/listenKey (HMAC SHA256)
``

有效期延长至本次调用后60分钟

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
recvWindow | LONG | NO |
timestamp | LONG | YES |



## 关闭listenKey (USER_STREAM)

> **响应:**

```javascript
{}
```

``
DELETE /dapi/v1/listenKey (HMAC SHA256)
``

关闭某账户数据流

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
recvWindow | LONG | NO |
timestamp | LONG | YES |




## 追加保证金通知

> **Payload:**

```javascript
{
    "e":"MARGIN_CALL",    	// 事件类型
    "E":1587727187525,		// 事件时间
    "i": "SfsR",							// 账户唯一识别码 accountAlias
    "cw":"3.16812045",		// 除去逐仓仓位保证金的钱包余额, 仅在全仓 margin call 情况下推送此字段
    "p":[					// 涉及持仓
      {
        "s":"BTCUSD",		// symbol
        "ps":"LONG",		// 持仓方向
        "pa":"1.327",		// 仓位
        "mt":"CROSSED",		// 保证金模式
        "iw":"0",			// 若为逐仓，仓位保证金
        "mp":"187.17127",	// 标记价格
        "up":"-1.166074",	// 为实现盈亏
        "mm":"1.614445"		// 持仓需要的维持保证金
      }
    ]
}  
 
```


* 当用户持仓风险过高，会推送此消息。
* 此消息仅作为风险指导信息，不建议用于投资策略。
* 在大波动市场行情下,不排除此消息发出的同时用户仓位已被强平的可能。




## Balance和Position更新推送

> **Payload:**

```javascript
{
  "e": "ACCOUNT_UPDATE",				// 事件类型
  "E": 1564745798939,            		// 事件时间
  "T": 1564745798938,           		// 撮合时间
  "i": "SfsR",							// 账户唯一识别码 accountAlias
  "a":                          		// 账户更新事件
    {
      "B":[                     		// 余额信息
        {
          "a":"BTC",           		// 资产名称
          "wb":"122624.12345678",    	// 钱包余额
          "cw":"100.12345678"			// 除去逐仓保证金的钱包余额
        },
        {
          "a":"ETH",           
          "wb":"1.00000000",
          "cw":"0.00000000"         
        }
      ],
      "P":[
       {
          "s":"BTCUSD_200930",          	// 交易对
          "pa":"0",               	// 仓位
          "ep":"0.00000",            // 入仓价格
          "cr":"200",             	// (费前)累计实现损益
          "up":"0",						// 持仓未实现盈亏
          "mt":"isolated",				// 保证金模式
          "iw":"0.00000000",			// 若为逐仓，仓位保证金
          "ps":"BOTH"					// 持仓方向
       }，
       {
        	"s":"BTCUSD_200930",
        	"pa":"20",
        	"ep":"6563.66500",
        	"cr":"0",
        	"up":"2850.21200",
        	"mt":"isolated",
        	"iw":"13200.70726908",
        	"ps":"LONG"
      	 },
       {
        	"s":"BTCUSD_200930",
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

账户更新事件的 event type 固定为 `ACCOUNT_UPDATE`

* 仅当账户信息有变动时，才会推送此事件。
* 订单状态变化没有引起账户和持仓变化的，不会推送此事件。
* 每次推送的position 信息，仅包含当前持仓不为0或逐仓仓位保证金不为0的symbol position。


## 订单/交易 更新推送

> **Payload:**

```javascript
{
  
  "e":"ORDER_TRADE_UPDATE",			// 事件类型
  "E":1568879465651,				// 事件时间
  "T":1568879465650,				// 撮合时间
  "i": "SfsR",							// 账户唯一识别码 accountAlias
  "o":{								
    "s":"BTCUSD_200930",					// 交易对
    "c":"TEST",						// 客户端自定订单ID
      // 特殊的自定义订单ID:
      // "autoclose-"开头的字符串: 系统强平订单
      // "delivery-"开头的字符串: 系统交割平仓单
    "S":"SELL",						// 订单方向
    "o":"LIMIT",					// 订单类型
    "f":"GTC",						// 有效方式
    "q":"0.001",					// 订单原始数量
    "p":"9910",						// 订单原始价格
    "ap":"0",						// 订单平均价格
    "sp":"0",						// 订单停止价格
    "x":"NEW",						// 本次事件的具体执行类型
    "X":"NEW",						// 订单的当前状态
    "i":8886774,					// 订单ID
    "l":"0",						// 订单末次成交数量
    "z":"0",						// 订单累计已成交数量
    "L":"0",						// 订单末次成交价格
    "ma": "BTC", 					// 保证金资产类型
    "N": "BTC",                 	// 手续费资产类型
    "n": "0",                    	// 手续费数量
    "T":1568879465651,				// 成交时间
    "t":0,							// 成交ID
    "ur": "0",						// 交易已实现盈亏
    "b":"0",						// 买单净值
    "a":"9.91",						// 卖单净值
    "m": false,					    // 该成交是作为挂单成交吗？
    "R":false	,				    // 是否是只减仓单
    "wt": "CONTRACT_PRICE",	        // 触发价类型
    "ot": "LIMIT",					// 原始订单类型
    "ps":"LONG",						// 持仓方向
    "cp":false,						// 是否为触发平仓单
    "AP":"7476.89",					// 追踪止损激活价格
    "cr":"5.0"						// 追踪止损回调比例
    
  }
  
}
```

当有新订单创建、订单有新成交或者新的状态变化时会推送此类事件
event type统一为 `ORDER_TRADE_UPDATE`

**订单方向**

* BUY 买入
* SELL 卖出

**持仓方向:**

* BOTH 单一持仓方向
* LONG 多头（双向持仓下）
* SHORT 空头（双向持仓下）

**订单类型**

* MARKET  市价单
* LIMIT	限价单
* STOP		止损单
* TAKE_PROFIT 止盈单

**本次事件的具体执行类型**

* NEW
* PARTIAL_FILL	部分成交
* FILL				成交
* CANCELED		已撤
* REJECTED		拒绝订单
* CALCULATED		强平单
* EXPIRED			订单失效
* TRADE			交易
* RESTATED 		

**订单状态**

* NEW
* PARTIALLY_FILLED    
* FILLED
* CANCELED
* REPLACED
* STOPPED
* REJECTED
* EXPIRED
* NEW_INSURANCE		风险保障基金（强平）
* NEW_ADL				自动减仓序列（强平）

**有效方式:**

* GTC 
* IOC
* FOK
* GTX



#错误代码

> error JSON payload:
 
```javascript
{
  "code":-1121,
  "msg":"Invalid symbol."
}
```

错误由两部分组成：错误代码和消息。 代码是通用的，但是消息可能会有所不同。


## 10xx - 常规服务器或网络问题
### -1000 UNKNOWN
 * An unknown error occured while processing the request.
 * 处理请求时发生未知错误。

### -1001 DISCONNECTED
 * Internal error; unable to process your request. Please try again.
 * 内部错误; 无法处理您的请求。 请再试一次.

### -1002 UNAUTHORIZED
 * You are not authorized to execute this request.
 * 您无权执行此请求。

### -1003 TOO_MANY_REQUESTS
 * Too many requests queued.
 * 排队的请求过多。
 * Too many requests; please use the websocket for live updates.
 * 请求权重过多； 请使用websocket获取最新更新。
 * Too many requests; current limit is %s requests per minute. Please use the websocket for live updates to avoid polling the API.
 * 请求权重过多； 当前限制为每分钟％s请求权重。 请使用websocket进行实时更新，以避免轮询API。
 * Way too many requests; IP banned until %s. Please use the websocket for live updates to avoid bans.
 * 请求权重过多； IP被禁止，直到％s。 请使用websocket进行实时更新，以免被禁。
 
### -1004 DUPLICATE_IP
 * This IP is already on the white list
 * IP地址已经在白名单

### -1005 NO_SUCH_IP
 * No such IP has been white listed
 * 白名单上没有此IP地址
 
### -1006 UNEXPECTED_RESP
 * An unexpected response was received from the message bus. Execution status unknown.
 * 从消息总线收到意外的响应。执行状态未知。

### -1007 TIMEOUT
 * Timeout waiting for response from backend server. Send status unknown; execution status unknown.
 * 等待后端服务器响应超时。 发送状态未知； 执行状态未知。

### -1014 UNKNOWN_ORDER_COMPOSITION
 * Unsupported order combination.
 * 不支持当前的下单参数组合

### -1015 TOO_MANY_ORDERS
 * Too many new orders.
 * 新订单太多。
 * Too many new orders; current limit is %s orders per %s.
 * 新订单太多； 当前限制为每％s ％s个订单。

### -1016 SERVICE_SHUTTING_DOWN
 * This service is no longer available.
 * 该服务不可用。

### -1020 UNSUPPORTED_OPERATION
 * This operation is not supported.
 * 不支持此操作。

### -1021 INVALID_TIMESTAMP
 * Timestamp for this request is outside of the recvWindow.
  * 此请求的时间戳在recvWindow之外。
 * Timestamp for this request was 1000ms ahead of the server's time.
 * 此请求的时间戳比服务器时间提前1000毫秒。

### -1022 INVALID_SIGNATURE
 * Signature for this request is not valid.
 * 此请求的签名无效。

### -1023 START_TIME_GREATER_THAN_END_TIME
 * Start time is greater than end time.
 * 参数里面的开始时间在结束时间之后


## 11xx - Request issues
### -1100 ILLEGAL_CHARS
 * Illegal characters found in a parameter.
 * 在参数中发现非法字符。
 * Illegal characters found in parameter '%s'; legal range is '%s'.
 * 在参数`％s`中发现非法字符； 合法范围是`％s`。

### -1101 TOO_MANY_PARAMETERS
 * Too many parameters sent for this endpoint.
 * 为此端点发送的参数太多。
 * Too many parameters; expected '%s' and received '%s'.
 * 参数太多；预期为`％s`并收到了`％s`。
 * Duplicate values for a parameter detected.
 * 检测到的参数值重复。

### -1102 MANDATORY_PARAM_EMPTY_OR_MALFORMED
 * A mandatory parameter was not sent, was empty/null, or malformed.
 * 未发送强制性参数，该参数为空/空或格式错误。
 * Mandatory parameter '%s' was not sent, was empty/null, or malformed.
 * 强制参数`％s`未发送，为空/空或格式错误。
 * Param '%s' or '%s' must be sent, but both were empty/null!
 * 必须发送参数`％s`或`％s`，但两者均为空！

### -1103 UNKNOWN_PARAM
 * An unknown parameter was sent.
 * 发送了未知参数。

### -1104 UNREAD_PARAMETERS
 * Not all sent parameters were read.
 * 并非所有发送的参数都被读取。
 * Not all sent parameters were read; read '%s' parameter(s) but was sent '%s'.
 * 并非所有发送的参数都被读取； 读取了`％s`参数，但被发送了`％s`。

### -1105 PARAM_EMPTY
 * A parameter was empty.
 * 参数为空。
 * Parameter '%s' was empty.
 * 参数`％s`为空。

### -1106 PARAM_NOT_REQUIRED
 * A parameter was sent when not required.
 * 发送了不需要的参数。
 * Parameter '%s' sent when not required.
 * 发送了不需要参数`％s`。

### -1111 BAD_PRECISION
 * Precision is over the maximum defined for this asset.
 * 精度超过为此资产定义的最大值。

### -1112 NO_DEPTH
 * No orders on book for symbol.
 * 交易对没有挂单。
 
### -1114 TIF_NOT_REQUIRED
 * TimeInForce parameter sent when not required.
 * 发送的`TimeInForce`参数不需要。

### -1115 INVALID_TIF
 * Invalid timeInForce.
 * 无效的`timeInForce`

### -1116 INVALID_ORDER_TYPE
 * Invalid orderType.
 * 无效订单类型。

### -1117 INVALID_SIDE
 * Invalid side.
 * 无效买卖方向。

### -1118 EMPTY_NEW_CL_ORD_ID
 * New client order ID was empty.
 * 新的客户订单ID为空。

### -1119 EMPTY_ORG_CL_ORD_ID
 * Original client order ID was empty.
 * 客户自定义的订单ID为空。

### -1120 BAD_INTERVAL
 * Invalid interval.
 * 无效时间间隔。

### -1121 BAD_SYMBOL
 * Invalid symbol.
 * 无效的交易对。

### -1125 INVALID_LISTEN_KEY
 * This listenKey does not exist.
 * 此`listenKey`不存在。

### -1127 MORE_THAN_XX_HOURS
 * Lookup interval is too big.
 * 查询间隔太大。
 * More than %s hours between startTime and endTime.
 * 从开始时间到结束时间之间超过`％s`小时。

### -1128 OPTIONAL_PARAMS_BAD_COMBO
 * Combination of optional parameters invalid.
 * 可选参数组合无效。

### -1130 INVALID_PARAMETER
 * Invalid data sent for a parameter.
 * 发送的参数为无效数据。
 * Data sent for paramter '%s' is not valid.
 * 发送参数`％s`的数据无效。


## 20xx - Processing Issues
### -2010 NEW_ORDER_REJECTED
 * NEW_ORDER_REJECTED
 * 新订单被拒绝

### -2011 CANCEL_REJECTED
 * CANCEL_REJECTED
 * 取消订单被拒绝

### -2013 NO_SUCH_ORDER
 * Order does not exist.
 * 订单不存在。

### -2014 BAD_API_KEY_FMT
 * API-key format invalid.
 * API-key 格式无效。

### -2015 REJECTED_MBX_KEY
 * Invalid API-key, IP, or permissions for action.
 * 无效的API密钥，IP或操作权限。

### -2016 NO_TRADING_WINDOW
 * No trading window could be found for the symbol. Try ticker/24hrs instead.
 * 找不到该交易对的交易窗口。 尝试改为24小时自动报价。

### -2018 BALANCE_NOT_SUFFICIENT
 * Balance is insufficient.
 * 余额不足

### -2019 MARGIN_NOT_SUFFICIEN
 * Margin is insufficient.
 * 杠杆账户余额不足

### -2020 UNABLE_TO_FILL
 * Unable to fill.
 * 无法成交

### -2021 ORDER_WOULD_IMMEDIATELY_TRIGGER
 * Order would immediately trigger.
 * 订单可能被立刻触发

### -2022 REDUCE_ONLY_REJECT
 * ReduceOnly Order is rejected.
 * `ReduceOnly`订单被拒绝

### -2023 USER_IN_LIQUIDATION
 * User in liquidation mode now.
 * 用户正处于被强平模式

### -2024 POSITION_NOT_SUFFICIENT
 * Position is not sufficient.
 * 持仓不足

### -2025 MAX_OPEN_ORDER_EXCEEDED
 * Reach max open order limit.
 * 挂单量达到上限

### -2026 REDUCE_ONLY_ORDER_TYPE_NOT_SUPPORTED
 * This OrderType is not supported when reduceOnly.
 * 当前订单类型不支持`reduceOnly`
 

## 40xx - Filters and other Issues
### -4000 INVALID_ORDER_STATUS
 * Invalid order status.
 * 订单状态不正确

### -4001 PRICE_LESS_THAN_ZERO
 * Price less than 0.
 * 价格小于0

### -4002 PRICE_GREATER_THAN_MAX_PRICE
 * Price greater than max price.
 * 价格超过最大值
 
### -4003 QTY_LESS_THAN_ZERO
 * Quantity less than zero.
 * 数量小于0

### -4004 QTY_LESS_THAN_MIN_QTY
 * Quantity less than min quantity.
 * 数量小于最小值
 
### -4005 QTY_GREATER_THAN_MAX_QTY
 * Quantity greater than max quantity.
 * 数量大于最大值

### -4006 STOP_PRICE_LESS_THAN_ZERO
 * Stop price less than zero. 
 * 触发价小于最小值
 
### -4007 STOP_PRICE_GREATER_THAN_MAX_PRICE
 * Stop price greater than max price.
 * 触发价大于最大值

### -4008 TICK_SIZE_LESS_THAN_ZERO
 * Tick size less than zero.
 * 价格精度小于0

### -4009 MAX_PRICE_LESS_THAN_MIN_PRICE
 * Max price less than min price.
 * 最大价格小于最小价格

### -4010 MAX_QTY_LESS_THAN_MIN_QTY
 * Max qty less than min qty.
 * 最大数量小于最小数量

### -4011 STEP_SIZE_LESS_THAN_ZERO
 * Step size less than zero.
 * 步进值小于0

### -4012 MAX_NUM_ORDERS_LESS_THAN_ZERO
 * Max num orders less than zero.
 * 最大订单量小于0

### -4013 PRICE_LESS_THAN_MIN_PRICE
 * Price less than min price.
 * 价格小于最小价格

### -4014 PRICE_NOT_INCREASED_BY_TICK_SIZE
 * Price not increased by tick size.
 * 价格增量不是价格精度的倍数。
 
### -4015 INVALID_CL_ORD_ID_LEN
 * Client order id is not valid.
 * 客户订单ID有误。
 * Client order id length should be less than 32 chars
 * 客户订单ID长度应该少于32字符

### -4016 PRICE_HIGHTER_THAN_MULTIPLIER_UP
 * Price is higher than mark price multiplier cap.

### -4017 MULTIPLIER_UP_LESS_THAN_ZERO
 * Multiplier up less than zero.
 * 价格上限小于0

### -4018 MULTIPLIER_DOWN_LESS_THAN_ZERO
 * Multiplier down less than zero.
 * 价格下限小于0

### -4019 COMPOSITE_SCALE_OVERFLOW
 * Composite scale too large.

### -4020 TARGET_STRATEGY_INVALID
 * Target strategy invalid for orderType '%s',reduceOnly '%b'.
 * 目标策略值不适合`%s`订单状态, 只减仓`%b`。

### -4021 INVALID_DEPTH_LIMIT
 * Invalid depth limit.
 * 深度信息的`limit`值不正确。
 * '%s' is not valid depth limit.
 * `%s`不是合理的深度信息的`limit`值。

### -4022 WRONG_MARKET_STATUS
 * market status sent is not valid.
 * 发送的市场状态不正确。
 
### -4023 QTY_NOT_INCREASED_BY_STEP_SIZE
 * Qty not increased by step size.
 * 数量的递增值不是步进值的倍数。

### -4024 PRICE_LOWER_THAN_MULTIPLIER_DOWN
 * Price is lower than mark price multiplier floor.

### -4025 MULTIPLIER_DECIMAL_LESS_THAN_ZERO
 * Multiplier decimal less than zero.

### -4026 COMMISSION_INVALID
 * Commission invalid.
 * 收益值不正确
 * `%s` less than zero.
 * `%s`少于0
 * `%s` absolute value greater than `%s`
 * `%s`绝对值大于`%s`

### -4027 INVALID_ACCOUNT_TYPE
 * Invalid account type.
 * 账户类型不正确。

### -4028 INVALID_LEVERAGE
 * Invalid leverage
 * 杠杆倍数不正确
 * Leverage `%s` is not valid
 * 杠杆`%s`不正确
 * Leverage `%s` already exist with `%s`
 * 杠杆`%s`已经存在于`%s`

### -4029 INVALID_TICK_SIZE_PRECISION
 * Tick size precision is invalid.
 * 价格精度小数点位数不正确。

### -4030 INVALID_STEP_SIZE_PRECISION
 * Step size precision is invalid.
 * 步进值小数点位数不正确。

### -4031 INVALID_WORKING_TYPE
 * Invalid parameter working type
 * 不正确的参数类型
 * Invalid parameter working type: `%s`
 * 不正确的参数类型: `%s`

### -4032 EXCEED_MAX_CANCEL_ORDER_SIZE
 * Exceed maximum cancel order size.
 * 超过可以取消的最大订单量。
 * Invalid parameter working type: `%s`
 * 不正确的参数类型: `%s`

### -4033 INSURANCE_ACCOUNT_NOT_FOUND
 * Insurance account not found.
 * 风险保障基金账号没找到。

### -4044 INVALID_BALANCE_TYPE
 * Balance Type is invalid.
 * 余额类型不正确。

### -4045 MAX_STOP_ORDER_EXCEEDED
 * Reach max stop order limit.
 * 达到止损单的上限。

### -4046 NO_NEED_TO_CHANGE_MARGIN_TYPE
 * No need to change margin type.
 * 不需要切换仓位模式。

### -4047 THERE_EXISTS_OPEN_ORDERS
 * Margin type cannot be changed if there exists open orders.
 * 如果有挂单，仓位模式不能切换。

### -4048 THERE_EXISTS_QUANTITY
 * Margin type cannot be changed if there exists position.
 * 如果有仓位，仓位模式不能切换。

### -4049 ADD_ISOLATED_MARGIN_REJECT
 * Add margin only support for isolated position.

### -4050 CROSS_BALANCE_INSUFFICIENT
 * Cross balance insufficient.
 * 全仓余额不足。

### -4051 ISOLATED_BALANCE_INSUFFICIENT
 * Isolated balance insufficient.
 * 逐仓余额不足。

### -4052 NO_NEED_TO_CHANGE_AUTO_ADD_MARGIN
 * No need to change auto add margin.

### -4053 AUTO_ADD_CROSSED_MARGIN_REJECT
 * Auto add margin only support for isolated position.
 * 自动增加保证金只适用于逐仓。

### -4054 ADD_ISOLATED_MARGIN_NO_POSITION_REJECT
 * Cannot add position margin: position is 0.
 * 不能增加逐仓保证金: 持仓为0

### -4055 AMOUNT_MUST_BE_POSITIVE
 * Amount must be positive.
 * 数量必须是正整数


## Messages for -1010 ERROR_MSG_RECEIVED, -2010 NEW_ORDER_REJECTED, and -2011 CANCEL_REJECTED
交易系统返回错误后，将发送此代码。 以下消息将指示特定的错误：

错误信息                                                          |                                       解释
--------------------------------------------------------------- | ------------------------------------------------------------------------------------------
"Unknown order sent."                                           | 通过`orderId`, `clientOrderId`, `origClientOrderId`都无法找到订单。
"Duplicate order sent."                                         | `clientOrderId`已经被使用。用户自定义的ID不可以重复。
"Market is closed."                                             | 该交易对不在交易范围。
"Account has insufficient balance for requested action."        | 资金不足，无法执行相关操作。
"Market orders are not supported for this symbol."              | 此交易对不支持`MARKET`订单。
"Stop loss orders are not supported for this symbol."           | 此交易对不支持`STOP_LOSS`。
"Stop loss limit orders are not supported for this symbol."     | 此交易对不支持`STOP_LOSS_LIMIT`。
"Take profit orders are not supported for this symbol."         | 此交易对不支持`TAKE_PROFIT`。
"Take profit limit orders are not supported for this symbol."   | 此交易对不支持`TAKE_PROFIT_LIMIT`。
"Price * QTY is zero or less."                                  | `price` * `quantity`太小。
"This action disabled is on this account."                      | 联系客户支持； 该帐户已禁用了某些操作。
"Unsupported order combination"                                 | 无法一起设置`orderType`, `timeInForce`, `stopPrice`来下单。
"Order would trigger immediately."                              | 和最新的交易价格比起来, `stop price`不合理，有可能立刻被成交。
"Cancel order is invalid. Check origClientOrderId and orderId." | `origClientOrderId`或者`orderId`没有设置。
"Order would immediately match and take."                       | `GTX`的订单可能会立刻成交，这样就成为吃单方，这是不允许的。

## -9xxx 过滤器故障
错误信息                            |                                                 解释
--------------------------------- | ---------------------------------------------------------------------------------------------------------
"Filter failure: PRICE_FILTER"    | `price`过高，过低和/或不遵循交易对的最小价格规则。
"Filter failure: LOT_SIZE"        | `quantity`太高，太低和/或不遵循该交易对的步长规则。
"Filter failure: MARKET_LOT_SIZE" | `MARKET`市价订单里`quantity`太高或者太低。通过`/dapi/v1/exchangeInfo`接口可以拿到相关信息。
"Filter failure: MAX_NUM_ORDERS"  | 账户里面此交易对的挂单(`open orders`)太多。
"Filter failure: PERCENT_PRICE"   | `price`比最近Y分钟的平均加权价格高X％或X％太低。
