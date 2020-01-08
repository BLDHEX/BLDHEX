# API 对接文档 

## 准备工作
**通过API可以获取市场行情数据、进行交易、管理订单、查询账户信息**
### 创建API Key
用户注册账号后，可以创建API Key秘钥，创建完成后得到一组随机生成的API Key与Secret Key,利用这一组数据可以进行程序化交易，单个账号建议最多创建5个密钥
> **请不要泄露API Key 与 Secret Key信息，以免造成资产损失,建议API可以绑定多个IP地址**

### 接口调用方式说明

- REST API

提供行情查询、余额查询、币币交易、订单管理功能

## REST API
### 接入 URL

- https://api.bldh.cc
### 请求交互
#### 介绍
REST API 提供行情查询、余额查询、币币交易、订单管理功能
所有请求基于Https协议。

对于公共接口，请求头信息为:

- **content-type:application/x-www-form-urlencoded**

对于私有接口，请求头信息为:

- **User-Agent:Broker-P V2.0.6**
- **X-BH-APIKEY:申请的API Key**
#### 错误码
| 状态码 | 说明 |
| --- | --- |
| -1000 | 未知错误 |
| -1001 | 请求中断，内部错误，请重试 |
| -1002 | 未授权请求 |
| -1003 | 请求次数过多 |
| -1006 | 服务器执行异常 |
| -1007 | 超时 |
| -1014 | 不支持的订单组合 |
| -1015 | 达到速率限制，请放慢请求速度 |
| -1016 | 服务器不可用 |
| -1020 | 不支持的操作 |
| -1021 | 请求时间戳失效 |
| -1022 | 请求签名无效 |
| -1100 | 存在非法参数 |
| -1101 | 参数过多 |
| -1102 | 强制参数不合法 |
| -1103 | 未知参数 |
| -1104 | 存在不可读的参数成分 |
| -1105 | 参数为空 |
| -1106 | 发送了不需要的参数 |
| -1111 | 精度不正确 |
| -1112 | 订单簿不存在 |
| -1114 | 发送了不必要的TimeInForce参数 |
| -1115 | 无效的TimeInForce |
| -1116 | 无效的orderType |
| -1117 | 无效的side |
| -1118 | 新订单id为空 |
| -1119 | 原始订单id为空 |
| -1120 | 无效的时间间隔 |
| -1121 | 无效的符号 |
| -1125 | listenKey不存在 |
| -1127 | 查询间隔太大 |
| -1128 | 可选参数组合无效 |
| -1130 | 发送的参数无效 |
| -1132 | 订单价格过高 | 
| -1133 | 订单价格过低 |
| -1134 | 订单价格精度过小 |
| -1135 | 订单数量过大 |
| -1136 | 订单数量过小 |
| -1137 | 订单数量精度国小 |
| -1138 | 订单价格超出允许范围 |
| -1139 | 订单已经成交 |
| -1140 | 订单金额低于最低金额 |
| -1141 | 重复的clientOrderId |
| -1142 | 订单已取消 |
| -1143 | 订单不存在订单簿上 |
| -1144 | 订单已经被锁定 |
| -1145 | 订单类型不支持取消 |
| -1146 | 订单创建超时 |
| -1147 | 订单取消超时 |
| -2010 | 不可创建订单 |
| -2011 | 不可取消订单 |
| -2013 | 订单不存在 |
| -2014 | API密钥格式错误 |
| -2015 | 无效的API密钥、IP或操作权限 |
| -2016 | 找不到该交易品种 |

### 签名认证
#### 签名说明
API 请求在通过网络传输的过程中极有可能被篡改，为了确保请求未被更改，除公共接口（基础信息，行情数据）外的私有接口均必须使用您的 API Key 做签名认证，以校验参数或参数值在传输途中是否发生了更改。
#### 签名步骤
**以获取当前订单为例**

- 接口
  - GET /openapi/v1/openOrders
- 示例API秘钥
  - apiKey = tAQfOrPIZAhym0qHISRt8EFvxPemdBm5j5WMlkm3Ke9aFp0EGWC2CGM8GHV4kCYW
  - secretKey = lH3ELTNiFxCQTmi9pPcWWikhsjO04Yoqw3euoHUuOLC3GYBW64ZqzQsiOEHXQS76

**1. 所有参数按"参数名=参数值"格式并用"&"拼接在一起组成要签名计算的字符串**

- 原始参数有:
  - timestamp = 1578389791149
  - orderId = 123456
- orderId=123456&recvWindow=120000&timestamp=1578389791149

**2. 用秘钥(secretKey)对字符串进行SHA256签名计算**

- SHA256(orderId=123456&recvWindow=120000&timestamp=1578389791149)
- 最终得到签名结果:
  - signature = a833bdf2e3a3a3198282d2d126a48ec1a297228776adf35e80cd440d1ff9eec9

**4. 将生成的数字签名加入到请求的路径参数里**

- 最终发送到服务器的请求地址为:
  - https://api.bldh.cc/openapi/v1/order?orderId=123456&recvWindow=120000&timestamp=1578389791149&signature=a833bdf2e3a3a3198282d2d126a48ec1a297228776adf35e80cd440d1ff9eec9
### 时效安全

- 一个`SIGNED`(有签名)的端点还需要发送一个参数，`timestamp`，这是当请求发起时的毫秒级时间戳。

- 一个额外的参数（非强制性）, `recvWindow`, 可以说明这个请求在多少毫秒内是有效的。如果`recvWindow`没有被发送，**默认值是5000**。

- 在当前，只有创建订单的时候才会用到`recvWindow`。

- 该参数的逻辑如下：
```json
if (timestamp < (serverTime + 1000) && (serverTime - timestamp) <= recvWindow) {
  // process request
} else {
  // reject request
}
```
**严谨的交易和时效紧紧相关** 网络有时会不稳定或者不可靠，这会导致请求发送服务器的时间不一致。 有了`recvWindow`，你可以说明在多少毫秒内请求是有效的，否则就会被服务器拒绝。
**建议使用一个相对小的recvWindow（5000或以下）！**
**
### REST API列表
| API | 接口类型 | 签名 | 说明 |
| --- | --- | --- | --- |
| GET /openapi/v1/time | 公共接口 | X | 获取服务器时间 |
| GET /openapi/v1/brokerInfo | 公共接口 | X | 获取当前broker交易规则和交易对的信息 |
| GET /openapi/quote/v1/depth | 公共接口 | X | 获取订单簿 |
| GET /openapi/quote/v1/trades | 公共接口 | X | 获取当前最新成交（最多500） |
| GET /openapi/quote/v1/klines | 公共接口 | X | symbol的k线/烛线图数据 K线会根据开盘时间而辨别。 |
| GET /openapi/quote/v1/ticker/24hr | 公共接口 | X | 24小时ticker价格变化数据 |
| GET /openapi/quote/v1/ticker/price | 公共接口 | X | 获取交易对最新价格 |
| GET /openapi/quote/v1/ticker/bookTicker | 公共接口 | X | 获取最佳订单簿价格 |
| POST /openapi/v1/order | 私有接口 | V | 创建订单 |
| GET /openapi/v1/order | 私有接口 | V | 查新订单 |
| DELETE /openapi/v1/order | 私有接口 | V | 取消委托单 |
| GET /openapi/v1/openOrders | 私有接口 | V | 获取单个或者多个交易对的当前订单 |
| GET /openapi/v1/historyOrders | 私有接口 | V | 获取当前账户的所有订单。亦或是取消的，完全成交的，拒绝的 |
| GET /openapi/v1/account | 私有接口 | V | 获取当前账户信息 |
| GET /openapi/v1/myTrades | 私有接口 | V | 获取当前账户历史成交记录 |
| GET /openapi/v1/depositOrders | 私有接口 | V | 获取当前账户的存款记录 |


### 获取服务器时间
#### GET /openapi/v1/time
#### 输入参数:无
#### 返回示例:
```json
{
  "serverTime": 1538323200000
}
```

### 获取交易对信息
#### GET /openapi/v1/brokerInfo
#### 输入参数:无
#### 返回示例:
```json
{
  "timezone": "UTC",
  "serverTime": 1538323200000,
  "rateLimits": [{
      "rateLimitType": "REQUESTS_WEIGHT",
      "interval": "MINUTE",
      "limit": 1500
    },
    {
      "rateLimitType": "ORDERS",
      "interval": "SECOND",
      "limit": 20
    },
    {
      "rateLimitType": "ORDERS",
      "interval": "DAY",
      "limit": 350000
    }
  ],
  "brokerFilters":[],
  "symbols": [{
    "symbol": "ETHBTC",
    "status": "TRADING",
    "baseAsset": "ETH",
    "baseAssetPrecision": "0.001",
    "quoteAsset": "BTC",
    "quotePrecision": "0.01",
    "icebergAllowed": false,
    "filters": [{
      "filterType": "PRICE_FILTER",
      "minPrice": "0.00000100",
      "maxPrice": "100000.00000000",
      "tickSize": "0.00000100"
    }, {
      "filterType": "LOT_SIZE",
      "minQty": "0.00100000",
      "maxQty": "100000.00000000",
      "stepSize": "0.00100000"
    }, {
      "filterType": "MIN_NOTIONAL",
      "minNotional": "0.00100000"
    }]
  }]
}
```

### 获取订单簿
#### GET /openapi/quote/v1/depth
#### 输入参数:
| 名称 | 类型 | 是否强制 | 描述 |
| --- | --- | --- | --- |
| symbol | STRING | YES |  |
| limit | INT | NO | 默认 100; 最大 100. |

注意: 如果设置limit=0会返回很多数据。
#### 返回示例:
```json
{
  "bids": [
    [
      "3.90000000",   // 价格
      "431.00000000"  // 数量
    ],
    [
      "4.00000000",
      "431.00000000"
    ]
  ],
  "asks": [
    [
      "4.00000200",  // 价格
      "12.00000000"  // 数量
    ],
    [
      "5.10000000",
      "28.00000000"
    ]
  ]
}
```

### 获取当前最新成交
#### GET /openapi/quote/v1/trades
#### 输入参数: 
| 名称 | 类型 | 是否强制 | 描述 |
| --- | --- | --- | --- |
| symbol | STRING | YES |  |
| limit | INT | NO | 默认 500; 最大 1000. |

#### 返回示例:
```json
[
  {
    "price": "4.00000100",
    "qty": "12.00000000",
    "time": 1499865549590,
    "isBuyerMaker": true
  }
]
```

### 获取K线
#### GET /openapi/quote/v1/klines
#### 输入参数:
| 名称 | 类型 | 是否强制 | 描述 |
| --- | --- | --- | --- |
| symbol | STRING | YES |  |
| interval | ENUM | YES |  |
| startTime | LONG | NO |  |
| endTime | LONG | NO |  |
| limit | INT | NO | 默认500; 最大1000. |

#### 返回实例：
```json
[
  [
    1499040000000,      // 开盘时间
    "0.01634790",       // 开盘价
    "0.80000000",       // 最高价
    "0.01575800",       // 最低价
    "0.01577100",       // 收盘价
    "148976.11427815",  // 交易量
    1499644799999,      // 收盘时间
    "2434.19055334",    // Quote asset数量
    308,                // 交易次数
    "1756.87402397",    // Taker buy base asset数量
    "28.46694368"       // Taker buy quote asset数量
  ]
]
```

### 获取24小时ticker价格变化数据
#### GET /openapi/quote/v1/ticker/24hr
#### 输入参数:
| 名称 | 类型 | 是否强制 | 描述 |
| --- | --- | --- | --- |
| symbol | STRING | NO |  |

注意 如果没有发送symbol，会返回很多数据。
#### 返回示例:
```json
{
  "time": 1538725500422,
  "symbol": "ETHBTC",
  "bestBidPrice": "4.00000200",
  "bestAskPrice": "4.00000200",
  "lastPrice": "4.00000200",
  "openPrice": "99.00000000",
  "highPrice": "100.00000000",
  "lowPrice": "0.10000000",
  "volume": "8913.30000000"
}

OR

[
  {
    "time": 1538725500422,
    "symbol": "ETHBTC",
    "lastPrice": "4.00000200",
    "openPrice": "99.00000000",
    "highPrice": "100.00000000",
    "lowPrice": "0.10000000",
    "volume": "8913.30000000"
 }
]
```

### 获取交易对最新价格
#### GET /openapi/quote/v1/ticker/price
#### 输入参数:
| 名称 | 类型 | 是否强制 | 描述 |
| --- | --- | --- | --- |
| symbol | STRING | NO |  |

注意：如果symbol没有发送，所有symbol的最新价都会被返回。
#### 返回示例:
```json
{
  "price": "4.00000200"
}

OR

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

### 获取最佳订单簿价格
#### GET /openapi/quote/v1/ticker/bookTicker
#### 输入参数:
| 名称 | 类型 | 是否强制 | 描述 |
| --- | --- | --- | --- |
| symbol | STRING | NO |  |

#### 返回示例：
```json
{
  "symbol": "LTCBTC",
  "bidPrice": "4.00000000",
  "bidQty": "431.00000000",
  "askPrice": "4.00000200",
  "askQty": "9.00000000"
}

OR

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

### 创建订单
#### POST /openapi/v1/order
#### 输入参数:
| 名称 | 类型 | 是否强制 | 描述 |
| --- | --- | --- | --- |
| symbol | STRING | YES |  |
| assetType | STRING | NO |  |
| side | ENUM | YES |  |
| type | ENUM | YES |  |
| timeInForce | ENUM | NO |  |
| quantity | DECIMAL | YES |  |
| price | DECIMAL | NO |  |
| newClientOrderId | STRING | NO | 一个自己给订单定义的ID，如果没有发送会自动生成。 |
| stopPrice | DECIMAL | NO | 与 `STOP_LOSS`, `STOP_LOSS_LIMIT`, `TAKE_PROFIT`, 和`TAKE_PROFIT_LIMIT` 订单一起使用. **当前不可用** |
| icebergQty | DECIMAL | NO | 与 `LIMIT`, `STOP_LOSS_LIMIT`, 和 `TAKE_PROFIT_LIMIT` 来创建冰山订单. **当前不可用** |
| recvWindow | LONG | NO |  |
| timestamp | LONG | YES |  |

在`type`上的额外强制参数:

| 类型 | 额外强制参数 |
| --- | --- |
| `LIMIT` | `timeInForce`, `quantity`, `price` |
| `MARKET` | `quantity` |
| `STOP_LOSS` | `quantity`, `stopPrice` **当前不可用** |
| `STOP_LOSS_LIMIT` | `timeInForce`, `quantity`, `price`, `stopPrice` **当前不可用** |
| `TAKE_PROFIT` | `quantity`, `stopPrice` **当前不可用** |
| `TAKE_PROFIT_LIMIT` | `timeInForce`, `quantity`, `price`, `stopPrice` **当前不可用** |
| `LIMIT_MAKER` | `quantity`, `price` |

#### 返回示例：
```json
{
  "orderId": 28,
  "clientOrderId": "ABC"
}
```

### 查询订单
#### GET /openapi/v1/order
#### 输入参数:
| 名称 | 类型 | 是否强制 | 描述 |
| --- | --- | --- | --- |
| orderId | LONG | NO |  |
| origClientOrderId | STRING | NO |  |
| recvWindow | LONG | NO |  |
| timestamp | LONG | YES |  |

注意：

- 单一 orderId 或者 origClientOrderId 必须被发送。
- 对于某些历史数据 cummulativeQuoteQty 可能会 < 0, 这说明数据当前不可用。
#### 返回示例:

```json
{
  "symbol": "LTCBTC",
  "orderId": 1,
  "clientOrderId": "ABC",
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
  "isWorking": true
}
```

### 取消当前委托
#### DELETE /openapi/v1/order
#### 输入参数:
| 名称 | 类型 | 是否强制 | 描述 |
| --- | --- | --- | --- |
| orderId | LONG | NO |  |
| clientOrderId | STRING | NO |  |
| recvWindow | LONG | NO |  |
| timestamp | LONG | YES |  |

注意：单一 orderId 或者 clientOrderId必须被发送。
#### 返回示例:
```json
{
  "symbol": "LTCBTC",
  "clientOrderId": "ABC",
  "orderId": 1,
  "status": "CANCELED"
}
```

### 获取当前订单
#### GET /openapi/v1/openOrders
#### 输入参数:

| 名称 | 类型 | 是否强制 | 描述 |
| --- | --- | --- | --- |
| symbol | String | NO |  |
| orderId | LONG | NO |  |
| limit | INT | NO | 默认 500; 最多 1000. |
| recvWindow | LONG | NO |  |
| timestamp | LONG | YES |  |

注意：如果没有发送symbol，会返回很多数据。
#### 返回示例:
```json
[
  {
    "symbol": "LTCBTC",
    "orderId": 1,
    "clientOrderId": "ABC",
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
    "isWorking": true
  }
]
```

### 获取历史订单
#### GET /openapi/v1/historyOrders
#### 输入参数:

| 名称 | 类型 | 是否强制 | 描述 |
| --- | --- | --- | --- |
| symbol | String | NO |  |
| orderId | LONG | NO |  |
| startTime | LONG | NO |  |
| endTime | LONG | NO |  |
| limit | INT | NO | 默认 500; 最大 1000. |
| recvWindow | LONG | NO |  |
| timestamp | LONG | YES |  |

注意：如果orderId设定好了，会筛选订单小于orderId的。否则会返回最近的订单信息。
#### 返回示例:
```json
[
  {
    "symbol": "LTCBTC",
    "orderId": 1,
    "clientOrderId": "987yjj2Ym",
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
    "isWorking": true
  }
]
```

### 获取账户信息
#### GET /openapi/v1/account
#### 输入参数:

| 名称 | 类型 | 是否强制 | 描述 |
| --- | --- | --- | --- |
| recvWindow | LONG | NO |  |
| timestamp | LONG | YES |  |

#### 返回示例:
```json
{
  "canTrade": true,
  "canWithdraw": true,
  "canDeposit": true,
  "updateTime": 123456789,
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
}
```

### 账户交易记录
#### GET /openapi/v1/myTrades
#### 输入参数:

| 名称 | 类型 | 是否强制 | 描述 |
| --- | --- | --- | --- |
| startTime | LONG | NO |  |
| endTime | LONG | NO |  |
| fromId | LONG | NO | TradeId to fetch from. |
| toId | LONG | NO | TradeId to fetch to. |
| limit | INT | NO | Default 500; max 1000. |
| recvWindow | LONG | NO |  |
| timestamp | LONG | YES |  |

注意：

- 如果只有fromId，会返回订单号小于fromId的，倒序排列。
- 如果只有toId，会返回订单号小于toId的，升序排列。
- 如果同时有fromId和toId, 会返回订单号在fromId和toId的，倒序排列。
- 如果fromId和toId都没有，会返回最新的成交记录，倒序排列。
#### 返回示例:
```json
[
  {
    "symbol": "ETHBTC",
    "id": 28457,
    "orderId": 100234,
    "matchOrderId": 109834,
    "price": "4.00000100",
    "qty": "12.00000000",
    "commission": "10.10000000",
    "commissionAsset": "ETH",
    "time": 1499865549590,
    "isBuyer": true,
    "isMaker": false
  }
]
```

### 账户存款记录
#### GET /openapi/v1/depositOrders
#### 输入参数:
| 名称 | 类型 | 是否强制 | 描述 |
| --- | --- | --- | --- |
| startTime | LONG | NO |  |
| endTime | LONG | NO |  |
| fromId | LONG | NO | 从哪个OrderId起开始抓取。默认抓取最新的存款记录。 |
| limit | INT | NO | 默认 500; 最大 1000. |
| recvWindow | LONG | NO |  |
| timestamp | LONG | YES |  |

注意：如果orderId设定好了，会筛选订单小于orderId的。否则会返回最近的订单信息。
#### 返回示例:
```json
[
  {
	"orderId": 100234,
	"token": "EOS",
	"address": "deposit2bh",
	"addressTag": "19012584",
	"fromAddress": "clarkkent",
	"fromAddressTag": "19029901",
	"time": 1499865549590,
	"quantity": "1.01"
  }
]
```
