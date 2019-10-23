# 交易所 broker API 测试文档

## 说明
域名: http://dev-broker.whaletrade.cn:9999

accessKeyId: WWCRFLFRECXVTVZSFEA9

secretAccessKey: Cl2Asgjls1LGWpMqHQC6ggyqzjA5y0RR

service: broker

返回为json格式

1.正常
```json
{
    "data": {
        ......
    }
}
```
2.异常
```json    
{
    "code": "ILLEGAL_ARGUMENT",
    "msg": "非法参数"
}   (交易所侧报错) 
{
    "errorCode": "SignatureNotMatch",
    "errorMessage": "signature does not match"
}   (网关侧报错)
{
    "timestamp": "2019-09-02T13:00:34.037+0000",
    "path": "/",
    "status": 404,
    "error": "Not Found",
    "message": null
}   (原生异常,其他报错)
```

## 服务调用(默认端口号：8080)

### 服务请求方法为GET时调用
```
POST /remote_services/get
```

#### 参数说明
| 参数              | 必填 | 类型                 | 说明                          |
| ---------------- | ---- |-------------------- | ---------------------------- |
| url              | 是   | String              | 请求url，如：http://dev-broker.whaletrade.cn:9999/transactions/exchange|
| params           | 否   | Map<String, String> | 请求参数，所有请求参数组装成Map    |
| accessKeyId      | 是   | String              | 请求方id,见说明                 |
| secretAccessKey  | 是   | String              | 请求方secret,见说明             |
| service          | 是   | String              | 请求服务，见说明                 |

#### 返回说明
同json格式

### 服务请求方法为POST时调用
```
POST /remote_services/post
```

#### 参数说明
| 参数              | 必填 | 类型                 | 说明                          |
| ---------------- | ---- |-------------------- | ---------------------------- |
| url              | 是   | String              | 请求url，如：http://dev-broker.whaletrade.cn:9999/vcode/register|
| params           | 否   | Map<String, String> | 请求参数，所有请求参数组装成Map    |
| body             | 否   | Map<String, Object> | 请求body，body中内容组装成Map    |
| accessKeyId      | 是   | String              | 请求方id,见说明                 |
| secretAccessKey  | 是   | String              | 请求方secret,见说明             |
| service          | 是   | String              | 请求服务，见说明                 |

#### 返回说明
同json格式

## API列表

### 查询币币交易明细（id倒序）
```
GET transactions/exchange
```

#### 参数说明
| 参数         | 必填 | 类型           | 说明                         |
| ----------- | ---- |-------------  | ---------------------------- |
| startTime   | 否   | Long          | 开始时间，时间戳                 |
| endTime     | 否   | Long          | 截止时间，时间戳                 |
| coinPair    | 否   | String        | 货币对，如：BTC/USDT            |
| coin        | 否   | String        | 币种code                       |          
| cursor      | 否   | String        | 游标                           |
| size        | 否   | Integer       | 显示条数，10 - 100              |

#### 返回说明
```
{
    "data": {
        "items": [ 
            {
                "id": "cursor_nZwExjwI1G",
                "mobile": "+86.18018629787",
                "coinPair": "BTC/USDT",
                "type": "buy",
                "price": "11519.46",
                "amount": "2.077732",
                "fee": "0.00207773",
                "time": 1567992209000
            },
            {
                "id": "cursor_O8lBO9lcXg",
                "mobile": "+86.18018629787",
                "coinPair": "BTC/USDT",
                "type": "buy",
                "price": "11519.46",
                "amount": "0.0001",
                "fee": "0.0000001",
                "time": 1567990821000
            },
            {
                "id": "cursor_yG1w9BYHmZ",
                "mobile": "+86.15800919478",
                "coinPair": "BTC/USDT",
                "type": "sell",
                "price": "11519.46",
                "amount": "102.7",
                "fee": "0.1027",
                "time": 1567569722000
            }, ...
        ],
        "hasNext": true,
        "nextCursor": "cursor_pl8oq4Tag3"
    }
}
```

#### 字段说明
| 字段              |  说明                     |
| -----------      |  ----------------------- |
| mobile           | 手机号                    |
| coinPair         | 交易货币对                 |
| type             | 买卖类型（buy/sell）       |
| price            | 成交价                    |
| amount           | 交易量                    |
| fee              | 手续费                    |
| time             | 时间                      |
| hasNext          | 是否有下一条               |
| nextCursor       | 游标(下一条的id)           |

### 查询微盘交易报表（id倒序）
```
GET transactions/wp
```

#### 参数说明
| 参数         | 必填 | 类型           | 说明                         |
| ----------- | ---- |-------------  | ---------------------------- |
| startTime   | 否   | Long          | 开始时间，时间戳                 |
| endTime     | 否   | Long          | 截止时间，时间戳                 |
| cursor      | 否   | String        | 游标                           |
| size        | 否   | Integer       | 显示条数，10 - 100              |

#### 返回说明
```
{
    "data": {
        "items": [
            {
                "id": "cursor_yZnB9jfwZz",
                "mobile": "+86.15800919478",
                "product": "0.01BTC",
                "type": "buy",
                "lots": "1",
                "coin": "USDT",
                "position": "-0.0134",
                "fee": "0.5",
                "time": 1568268000000                             
            }, ...
        ],
        "hasNext": false,
        "nextCursor": null
    }
}
```

#### 字段说明
| 字段              |  说明                     |
| -----------      |  ----------------------- |
| product          | 产品code                  |
| lots             | 手数                      |
| position         | 头寸                      |

