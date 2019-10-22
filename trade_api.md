[TOC]

### 接入信息

#### 第三方验证信息
- App ID
- App Secret
#### API地址
- https://wtrade.ytangdata.com/exchange-thirdparty/v1


### 请求格式

#### 请求方法
涉及到的请求方法有
- POST
- GET
- DELETE

使用POST的时候，内容为JSON, header里需要指定
`Content-Type: application/json`

#### 验证
REST HTTP header 包含以下信息
- `ACCESS_APP_ID`, 内容是App ID
- `ACCESS_SIGN`, 生成的签名
- `ACCESS_TIMESTAMP`, 当前timestamp, 格式为ISO8601

`ACCESS_SIGN`签名由如下规则生成:

1. 取到当前 timestamp 格式为ISO8601
2. 对请求参数按字典序排序并通过`&`符号拼接。比如`key1=value1&key2=value2`。
  如果是POST请求, 也按同样方法拼接字符窜，
  如json `{"key1":"value1","Key2":"value2"}`也得到同样的字符窜`key1=value1&key2=value2`
3. 1里的timestamp和2里的请求参数字符窜拼接后，通过HMAC SHA256加密(秘钥使用APP Secret), 以及BASE64编码得到签名.

```javascript
//取到当前 timestamp 格式为ISO
var timestamp="2018-12-28T05:26:57.532Z"
//对请求参数按字母排序用‘&’连接参数对(对post JSON body也类似)
var sortedRequestParameterKeyValue="a=1&b=2"
var secret = "secret"
//签名由timestamp + 请求参数字符窜 拼接后通过HMAC SHA256加密, 以及BASE64编码得到。
console.log(
    CryptoJS.enc.Base64.stringify(
        CryptoJS.HmacSHA256(timestamp + sortedRequestParameterKeyValue, secret)))
```

请求例子:
```
curl -H "ACCESS_APP_ID:app1" \
     -H "ACCESS_SIGN:nicGokrXshEOxftcXLV90KrUoCsuPv2NimKLjUFhIzI=" \
     -H "ACCESS_TIMESTAMP:2018-12-28T05:26:57.532Z" \
     'localhost:8080/hello?a=1&b=2'
```

### 响应

#### 状态码
|状态码|简介|详细描述|
|--- |--- |--- |
|200|OK|请求成功完成|
|201|Created|成功创建了一个新的资源。资源的URI可从响应中获得|
|204|No Content|已成功应用到现有资源的更新|
|400|Bad Request|错误请求是。响应体将提供详细信息|
|404|Not Found|请求的资源不存在|
|5XX|Server Error|业务系统发生错误|

#### 错误消息:
每当返回一个错误响应（状态代码>＝5XX）时，主体将包含一个JSON对象，这对象说明了问题所在。错误对象具有以下结构

|字段名|类型|描述|
|--- |--- |--- |
|code|String|错误代码|
|message|String|错误原因的描述|

例如:
```
{code: "EX_ACCOUNT_NOT_ENOUGH"}
```

通用错误代码, 其他代码见各API
##### 错误代码
| code | Description |
|--- |--- |
|EX_SYSTEM_ERROR|系统繁忙|
|EX_INTERNAL_ERROR|系统内部错误|


### API列表

#### 获取账户列表

##### 请求
```
GET /u/accounts
```

##### 返回参数
|Path|Type|Description|
|--- |--- |--- |
|data|Array|返回数据(JSON)|
|data[].coin|String|币种|
|data[].balance|String|余额|
|data[].frozen|String|冻结金额|
|data[].avail|String|可用金额|
|data[].state|String|账户状态|

##### 错误代码
| code | Description |
|--- |--- |
| EX_CUSTOMER_NOT_FOUND | 用户不存在 |

##### 请求示例:
```
GET /u/accounts
```

##### 返回示例:
```json
{  
   "data":[  
      {  
         "coin":"USDT",
         "balance":"618267.5337771",
         "frozen":"26.56",
         "avail":"618240.9737771",
         "state":"normal"
      },
      ...
}
```

#### 创建委托单
##### 请求
```
POST /orders 
```

请求参数(JSON):

|Path|Type|Description|
|--- |--- |--- |
|coinPair|String|货币对(必填)|
|unitPrice|String|交易单价(必填)|
|amount|String|交易数量(必填)|
|direction|String|买卖方向(必填);买buy/卖sell|
|clientOrderId|Number|第3方委托id(可选)|


##### 返回参数
|Path|Type|Description|
|--- |--- |--- |
|data|Object|返回数据|
|data.orderId|Number|委托id|
|data.clientOrderId|Number|3方委托id|
|data.coinPair|String|货币对|
|data.orderTime|Number|委托时间|
|data.handleTime|Null|处理时间|
|data.tradeTotalFee|Null|交易手续费|
|data.tradeTotalPrice|Null|总金额|
|data.tradeUnitPrice|Null|已成交金额|
|data.direction|String|委托方向|
|data.unitPrice|String|委托价格|
|data.amount|String|委托数量|
|data.dealtAmount|String|已成交数量|
|data.canceledAmount|String|取消数量|
|data.orderState|String|委托状态|

##### 错误代码
| code | Description |
|--- |--- |
|EX_MARKET_NOT_OPEN | 市场已关闭 |
|EX_MISSING_COIN_PAIR|缺少货币对 |
|EX_ILLEGAL_ARGUMENT|参数错误 |
|EX_MISSING_ENTRUST_DIR|缺少买卖方向 |
|EX_MISSING_ENTRUST_PRICE|缺少交易单价 |
|EX_MISSING_ENTRUST_NUM|缺少交易数量 |
|EX_CUSTOMER_NOT_FOUND|用户不存在 |
|EX_TRADE_NOT_ALLOWED|禁止交易 |
|EX_ORDER_ID_ALREADY_EXISTED|委托编号已存在 |
|EX_PRODUCT_COIN_NOT_FOUND|商品币不存在 |
|EX_CURRENCY_COIN_NOT_FOUND|货币不存在 |
|EX_COIN_PAIR_NOT_FOUND|货币对未找到 |
|EX_COIN_PAIR_NOT_OPEN|货币对未开放交易 |
|EX_INVALID_ENTRUST_NUM|无效的委托数量 |
|EX_ACCOUNT_NOT_ACTIVATED|该币种账户未激活 |
|EX_ACCOUNT_NOT_ENOUGH|此币种下账户余额不足 |
|EX_ENTRUST_CREATE_FAILURE|委托单创建失败 |


##### 请求示例:
```
POST /orders 
Content-Type: application/json;charset=UTF-8
{  
   "coinPair":"TMC/BTC",
   "unitPrice":"0.01",
   "amount":"1",
   "direction":"buy",
   "clientOrderId":123
}
```

##### 返回示例:
```JSON
{  
   "data":{  
      "orderId":1,
      "clientOrderId":111,
      "coinPair":"TMC/BTC",
      "direction":"buy",
      "orderTime":1548921970559,
      "handleTime":null,
      "unitPrice":"0",
      "amount":"0",
      "dealtAmount":"0",
      "canceledAmount":"0",
      "tradeTotalFee":null,
      "tradeTotalPrice":null,
      "tradeUnitPrice":null,
      "orderState":"released"
   }
}
```

#### 撤销委托单(第3方委托号)
##### 请求
```
DELETE /orders?clientOrderId=<clientOrderId>
```
clientOrderId为第三方委托号	

##### 错误代码
| code | Description |
|--- |--- |
|EX_CUSTOMER_NOT_FOUND |用户不存在|
|EX_ENTRUST_NOT_FOUND |委托单未找到|
|EX_ENTRUST_CAN_NOT_CANCEL_BY_FINISHED |此委托已经成交，不能撤销|
|EX_ENTRUST_CAN_NOT_CANCEL_BY_CANCELED |此委托已经撤销，不能重复操作|
|EX_ENTRUST_CAN_NOT_CANCEL |委托单不能撤销|
|EX_ENTRUST_CANCEL_FAILURE |委托单撤销失败|

##### 请求示例
```
DELETE /orders?clientOrderId=1234
```

##### 返回示例
```JSON
{"data":null}
```

#### 撤销委托单

##### 请求
```
DELETE /orders/cancel/{id}
```
其中id为委托id

##### 错误代码
| code | Description |
|--- |--- |
|EX_CUSTOMER_NOT_FOUND |用户不存在|
|EX_ENTRUST_NOT_FOUND |委托单未找到|
|EX_ENTRUST_CAN_NOT_CANCEL_BY_FINISHED |此委托已经成交，不能撤销|
|EX_ENTRUST_CAN_NOT_CANCEL_BY_CANCELED |此委托已经撤销，不能重复操作|
|EX_ENTRUST_CAN_NOT_CANCEL |委托单不能撤销|
|EX_ENTRUST_CANCEL_FAILURE |委托单撤销失败|

##### 请求示例
```
DELETE /orders/cancel/13
```
##### 返回示例
```
{"data":null}
```

#### 委托详细
##### 请求
```
GET /orders/details/{id}
```
##### 返回
|Path|Type|Description|
|--- |--- |--- |
|data|Object|返回数据|
|data.orderId|Number|委托id|
|data.clientOrderId|Number|3方委托id|
|data.coinPair|String|货币对|
|data.orderTime|Number|委托时间|
|data.handleTime|Number|处理时间|
|data.tradeTotalFee|String|交易手续费|
|data.tradeTotalPrice|String|总金额|
|data.tradeUnitPrice|String|已成交金额|
|data.direction|String|委托方向|
|data.unitPrice|String|委托价格|
|data.amount|String|委托数量|
|data.dealtAmount|String|已成交数量|
|data.canceledAmount|String|取消数量|
|data.orderState|String|委托状态|

##### 错误代码
| code | Description |
|--- |--- |
|EX_CUSTOMER_NOT_FOUND|用户不存在|
|EX_ENTRUST_NOT_FOUND|委托单未找到|

##### 请求示例
```
GET /orders/details/13
```
##### 返回示例
```JSON
{  
   "data":{  
      "orderId":1,
      "clientOrderId":111,
      "coinPair":"TMC/BTC",
      "direction":"buy",
      "orderTime":1548921970194,
      "handleTime":1548921970194,
      "unitPrice":"0",
      "amount":"0",
      "dealtAmount":"0",
      "canceledAmount":"0",
      "tradeTotalFee":"0",
      "tradeTotalPrice":"0",
      "tradeUnitPrice":"0",
      "orderState":"released"
   }
}
```


#### 委托详细(第3方委托号)
##### 请求
```
GET /orders/details/client/{clientOrderId}
```
##### 返回
|Path|Type|Description|
|--- |--- |--- |
|data|Object|返回数据|
|data.orderId|Number|委托id|
|data.clientOrderId|Number|3方委托id|
|data.coinPair|String|货币对|
|data.orderTime|Number|委托时间|
|data.handleTime|Number|处理时间|
|data.tradeTotalFee|String|交易手续费|
|data.tradeTotalPrice|String|总金额|
|data.tradeUnitPrice|String|已成交金额|
|data.direction|String|委托方向|
|data.unitPrice|String|委托价格|
|data.amount|String|委托数量|
|data.dealtAmount|String|已成交数量|
|data.canceledAmount|String|取消数量|
|data.orderState|String|委托状态|

##### 错误代码
| code | Description |
|--- |--- |
| EX_CUSTOMER_NOT_FOUND|用户不存在|
| EX_ENTRUST_NOT_FOUND|委托单未找到|


##### 请求示例
```
GET /orders/details/client/111
```
##### 返回示例
```JSON
{  
   "data":{  
      "orderId":1,
      "clientOrderId":111,
      "coinPair":"TMC/BTC",
      "direction":"buy",
      "orderTime":1548921969790,
      "handleTime":1548921969790,
      "unitPrice":"0",
      "amount":"0",
      "dealtAmount":"0",
      "canceledAmount":"0",
      "tradeTotalFee":"0",
      "tradeTotalPrice":"0",
      "tradeUnitPrice":"0",
      "orderState":"released"
   }
}
```



#### 当前委托查询
##### 请求

```
GET /orders/processing?coinPair=<coinPair>&direction=<direction>&page=<page>&size=<size>
```

|Parameter|Description|
|--- |--- |
|coinPair|货币对|
|direction|买卖方向:买buy/卖sell|
|page|page|
|size|size|

##### 返回
|Path|Type|Description|
|--- |--- |--- |
|data|Object|返回数据(JSON)|
|data.totalPages|Number|总页数|
|data.totalItems|Number|总条数|
|data.items|Array|委托列表|
|data.items[].orderId|Number|委托ID|
|data.items[].clientOrderId|Number|第3方委托ID|
|data.items[].coinPair|String|货币对|
|data.items[].direction|String|委托方向|
|data.items[].orderTime|Number|委托时间|
|data.items[].handleTime|Number|处理时间|
|data.items[].unitPrice|String|价格|
|data.items[].amount|String|委托数量|
|data.items[].dealtAmount|String|已成交量|
|data.items[].canceledAmount|String|取消数量|
|data.items[].tradeTotalFee|String|手续费|
|data.items[].tradeTotalPrice|String|成交总额|
|data.items[].tradeUnitPrice|String|成交均价|
|data.items[].orderState|String|委托状态|

##### 错误代码
| code | Description |
|--- |--- |
|EX_ILLEGAL_ARGUMENT|参数错误|
|EX_CUSTOMER_NOT_FOUND|用户不存在|
|EX_COIN_PAIR_NOT_FOUND|货币对未找到|

##### 请求示例
```
GET /orders/processing?coinPair=TMC/BTC&direction=buy&state=released&page=1&size=10 
```
##### 返回示例
```JSON
{  
   "data":{  
      "items":[  
         {  
            "orderId":111,
            "clientOrderId":234,
            "coinPair":"TMC/BTC",
            "direction":"buy",
            "orderTime":1548921970030,
            "handleTime":1548921970030,
            "unitPrice":"0.33",
            "amount":"0.33",
            "dealtAmount":"0",
            "canceledAmount":"0",
            "tradeTotalFee":"0",
            "tradeTotalPrice":"0",
            "tradeUnitPrice":"0",
            "orderState":"released"
         }
      ],
      "totalPages":2,
      "totalItems":30
   }
}
```

#### 历史委托查询
##### 请求

```
GET /orders/his?coinPair=<coinPair>&direction=<direction>&state=<state>&page=<page>&size=<size>
```

|Parameter|Description|
|--- |--- |
|coinPair|货币对|
|direction|买卖方向:买buy/卖sell|
|state|released(委托中), finished(已完成), canceled(已撤销), failed(已失败), canceling(撤销中))|
|page|page|
|size|size|

##### 返回
|Path|Type|Description|
|--- |--- |--- |
|data|Object|返回数据(JSON)|
|data.totalPages|Number|总页数|
|data.totalItems|Number|总条数|
|data.items|Array|委托列表|
|data.items[].orderId|Number|委托ID|
|data.items[].clientOrderId|Number|第3方委托ID|
|data.items[].coinPair|String|货币对|
|data.items[].direction|String|委托方向|
|data.items[].orderTime|Number|委托时间|
|data.items[].handleTime|Number|处理时间|
|data.items[].unitPrice|String|价格|
|data.items[].amount|String|委托数量|
|data.items[].dealtAmount|String|已成交量|
|data.items[].canceledAmount|String|取消数量|
|data.items[].tradeTotalFee|String|手续费|
|data.items[].tradeTotalPrice|String|成交总额|
|data.items[].tradeUnitPrice|String|成交均价|
|data.items[].orderState|String|委托状态|

##### 错误代码
| code | Description |
|--- |--- |
|EX_ILLEGAL_ARGUMENT|参数错误|
|EX_CUSTOMER_NOT_FOUND|用户不存在|
|EX_COIN_PAIR_NOT_FOUND|货币对未找到|

##### 请求示例
```
GET /orders/processing?coinPair=TMC/BTC&direction=buy&state=released&page=1&size=10 
```
##### 返回示例
```JSON
{  
   "data":{  
      "items":[  
         {  
            "orderId":111,
            "clientOrderId":234,
            "coinPair":"TMC/BTC",
            "direction":"buy",
            "orderTime":1548921970030,
            "handleTime":1548921970030,
            "unitPrice":"0.33",
            "amount":"0.33",
            "dealtAmount":"0",
            "canceledAmount":"0",
            "tradeTotalFee":"0",
            "tradeTotalPrice":"0",
            "tradeUnitPrice":"0",
            "orderState":"canceled"
         }
      ],
      "totalPages":2,
      "totalItems":30
   }
}
```


#### 成交明细
委托id下所有的成交信息

##### 请求

```
GET /trades/his/<id>
```

|Parameter|Description|
|--- |--- |
|id|委托id(必填)|


##### 返回
|Path|Type|Description|
|--- |--- |--- |
|data|Array|返回数据|
|data[].orderId|Number|委托id|
|data[].clientOrderId|Number|3方委托id|
|data[].tradeId|Number|成交委托id|
|data[].coinPair|String|货币对|
|data[].direction|String|委托方向|
|data[].time|Null|交易时间|
|data[].fee|String|手续费|
|data[].num|String|交易数量|
|data[].unitPrice|Number|交易价格|

##### 错误代码
| code | Description |
|--- |--- |
| EX_CUSTOMER_NOT_FOUND|用户不存在|
| EX_ENTRUST_NOT_FOUND|委托单未找到|

##### 请求示例
```
GET /trades/his/13
```

##### 返回示例
```json
{  
   "data":[  
      {  
         "orderId":1,
         "clientOrderId":111,
         "coinPair":"BTC/USDT",
         "tradeId":1,
         "direction":"buy",
         "time":null,
         "unitPrice":3456.23000000000001818989403545856475830078125,
         "num":"0.200000000000000011102230246251565404236316680908203125",
         "fee":"0"
      }
   ]
}
```

#### 成交明细(第3方委托号)
##### 请求
```
GET /trades/his/client/<clientOrderId>
```
|Parameter|Description|
|--- |--- |
|clientOrderId|第3方委托id(必填)|

##### 返回
|Path|Type|Description|
|--- |--- |--- |
|data|Array|返回数据|
|data[].orderId|Number|委托id|
|data[].clientOrderId|Number|3方委托id|
|data[].tradeId|Number|成交委托id|
|data[].coinPair|String|货币对|
|data[].direction|String|委托方向|
|data[].time|Null|交易时间|
|data[].fee|String|手续费|
|data[].num|String|交易数量|
|data[].unitPrice|Number|交易价格|

##### 错误代码
| code | Description |
|--- |--- |
|EX_CUSTOMER_NOT_FOUND|用户不存在|
|EX_ENTRUST_NOT_FOUND|委托单未找到|

##### 请求示例
```
GET /trades/his/client/13
```
##### 返回示例
```json
{  
   "data":[  
      {  
         "orderId":1,
         "clientOrderId":111,
         "coinPair":"BTC/USDT",
         "tradeId":1,
         "direction":"buy",
         "time":null,
         "unitPrice":3456.23000000000001818989403545856475830078125,
         "num":"0.200000000000000011102230246251565404236316680908203125",
         "fee":"0"
      }
   ]
}
```

#### 历史成交列表
##### 请求
```
GET /trades/his?coinPair=<coinPair>&page=<page>&size=<size>
```
|Parameter|Description|
|--- |--- |
|coinPair|货币对（必填）|
|page|page|
|size|size|


##### 返回
|Path|Type|Description|
|--- |--- |--- |
|data|Object|返回数据(JSON)|
|data.totalPages|Number|总页数|
|data.totalItems|Number|总条数|
|data.items|Array|委托列表|
|data.items[].orderId|Number|委托id|
|data.items[].clientOrderId|Number|3方委托id|
|data.items[].tradeId|Number|成交委托id|
|data.items[].coinPair|String|货币对|
|data.items[].direction|String|委托方向|
|data.items[].time|Null|交易时间|
|data.items[].fee|String|手续费|
|data.items[].num|String|交易数量|
|data.items[].unitPrice|Number|交易价格|

##### 错误代码
| code | Description |
|--- |--- |
|EX_MISSING_COIN_PAIR|缺少货币对|
|EX_CUSTOMER_NOT_FOUND|用户不存在|
|EX_COIN_PAIR_NOT_FOUND|货币对未找到|

##### 请求示例
```
GET /trades/his?&coinPair=TMC/BTC&page=2&size=10
```
##### 返回示例
```json
{  
   "data":{  
      "items":[  
         {  
            "orderId":1,
            "clientOrderId":111,
            "coinPair":"BTC/USDT",
            "tradeId":1,
            "direction":"buy",
            "time":null,
            "unitPrice":3456.23000000000001818989403545856475830078125,
            "num":"0.200000000000000011102230246251565404236316680908203125",
            "fee":"0"
         }
      ],
      "totalPages":2,
      "totalItems":30
   }
}
```

#### 深度信息查询
##### 请求
```
GET /deep/info?coinPair=<coinPair>&deep=<deep>
```

|Parameter|Description|
|--- |--- |
|coinPair|货币对（必填）|
|deep|深度类型（0—​6）|


##### 返回
|Path|Type|Description|
|--- |--- |--- |
|data|Array|返回数据|
|data[].deepNum|Number|深度等级|
|data[].updateTime|String|更新时间|
|data[].buyList[].num|String|数量/买|
|data[].buyList[].price|String|价格/买|
|data[].buyList[].total|String|累计数|
|data[].sellList[].num|String|数量/卖|
|data[].sellList[].price|String|价格/卖|
|data[].sellList[].total|String|累计数|

##### 错误代码
| code | Description |
|--- |--- |
|EX_MISSING_COIN_PAIR | 缺少货币对 |
|EX_ILLEGAL_ARGUMENT | 参数错误 |
|EX_COIN_PAIR_NOT_FOUND	 | 货币对未找到 |


##### 请求示例
```
GET /deep/info?coinPair=TMC/BTC&deep=1
```

##### 返回示例
```json
{  
   "data":[  
      {  
         "deepNum":1,
         "updateTime":"2019-01-31T08:06:11.343+0000",
         "buyList":[  
            {  
               "price":"0",
               "num":"0",
               "total":"0"
            }
         ],
         "sellList":[  
            {  
               "price":"0",
               "num":"0",
               "total":"0"
            }
         ]
      }
   ]
}
```

#### 商品列表(货币对列表）
##### 请求
```
GET /coinpairs?coin=<coin>
```
|Parameter|Description|
|--- |--- |
|coin|币种|

##### 返回
|Path|Type|Description|
|--- |--- |--- |
|data|Array|返回数据(JSON)|
|data[].coinPair|String|货币对|
|data[].openState|Boolean|货币对开放状态|
|data[].priceScale|Number|价格精度|
|data[].buyRate|Number|商品币的手续费率|
|data[].sellRate|Number|计价货币手续费率|

##### 错误代码
| code | Description |
|--- |--- |
|EX_COIN_PAIR_NOT_FOUND | 货币对未找到|

##### 请求示例
```
GET /coinpairs
```
##### 返回示例
```json
{  
   "data":[  
      {  
         "coinPair":"TMC/BTC",
         "openState":true,
         "priceScale":2,
         "buyRate":0,
         "sellRate":0
      }
   ]
}
```