# 交易所 org API 测试文档

## 说明
测试域名: http://dev-org.whaletrade.cn:9999

accessKeyId: WQJCF76HGBGRZBKUKY5A

secretAccessKey: EdEouktRAE0KZjYHrJpfB0vE8WvsF42L

service: org

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
| url              | 是   | String              | 请求url，如：http://dev-org.whaletrade.cn:9999/user/invite_code|
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
| url              | 是   | String              | 请求url，如：http://dev-org.whaletrade.cn:9999/vcode/register|
| params           | 否   | Map<String, String> | 请求参数，所有请求参数组装成Map    |
| body             | 否   | Map<String, Object> | 请求body，body中内容组装成Map    |
| accessKeyId      | 是   | String              | 请求方id,见说明                 |
| secretAccessKey  | 是   | String              | 请求方secret,见说明             |
| service          | 是   | String              | 请求服务，见说明                 |

#### 返回说明
同json格式

## API列表

### 用户注册
```
POST user/register
```

#### 参数说明
| 参数         | 必填 | 类型    | 说明                         |
| ----------- | ---- |-----  | ---------------------------- |
| mobile      | 是   | String | 手机号                        |
| vcode       | 是   | String | 验证码                        |
| loginPass   | 是   | String | 登录密码                      |
| inviteCode  | 否   | String | 邀请码                        |

#### 返回说明
```
{
    "data": {
        "mobile": "",
        "inviteCode": "",
        "registerTime": 2222222,
        "inviterMobile": null
    }
}
```

#### 字段说明
| 字段              |  说明                     |
| -----------      |  ----------------------  |
| mobile           | 手机号                    |
| inviteCode       | 邀请码                     |
| registerTime     | 注册时间                    |
| inviterMobile    | 邀请人手机号                 |

### 发送注册验证码
```
POST vcode/register
```

#### 参数说明(params)
| 参数         | 必填 | 类型           | 说明                         |
| ----------- | ---- |-------------  | ---------------------------- |
| mobile      | 是   | String        | 手机号                         |

#### 返回说明
无

### 查询用户邀请码
```
GET user/invite_code
```

#### 参数说明
| 参数         | 必填 | 类型           | 说明                         |
| ----------- | ---- |-------------  | ---------------------------- |
| mobile      | 是   | String        | 手机号                         |

#### 返回说明
```
{
    "data": {
        "inviteCode": "El39OH"
    }
}
```

### 设置用户的邀请人
```
PUT user/inviter
```

#### 参数说明
| 参数         | 必填 | 类型    | 说明                         |
| ----------- | ---- |-----  | ---------------------------- |
| mobile      | 是   | String | 手机号                        |
| inviteCode  | 否   | String | 邀请码 **                     |
| inviteMobile| 否   | String | 邀请人手机号 **                |

** 邀请码/邀请人手机号至少传入一项，如两项都传入则忽略邀请人手机号

#### 返回说明

```
{
    "data": {
        "mobile": "",
        "inviteCode": "",
        "registerTime": 2222222,
        "inviterMobile": "13800000002"
    }
}
```

### 查询用户列表
```
GET users
```

#### 参数说明
| 参数            | 必填 | 类型           | 说明                            |
| -------------- | ---- |-------------  | ------------------------------ |
| startTime      | 否   | Long          | 起始时间(注册)                    |
| endTime        | 否   | Long          | 截止时间(注册)                    |
| inviterMobile  | 否   | String        | 邀请人手机号                      |
| page           | 否   | Integer       | 当前页码                         |
| size           | 否   | Integer       | 显示条数，10 - 100                |

#### 返回说明
```
{
    "items": [
        {
            "mobile": "",
            "inviteCode": "",
            "registerTime": 2222222,
            "inviterMobile": null
        }
    ],
    "totalPages": 1,
    "totalItems": 1
}
```

### 查询用户详情
```
GET user
```

#### 参数说明
| 参数         | 必填 | 类型           | 说明                         |
| ----------- | ---- |-------------  | ---------------------------- |
| mobile      | 是   | String        | 手机号                         |

#### 返回说明
```
{
    "data": {
        "mobile": "",
        "inviteCode": "",
        "registerTime": 2222222,
        "inviterMobile": null,
        "rnvLevel": 0,
        "cardNo": null,
        "accounts": [
            {
                "coin": "BTC",
                "balance": "0",
                "frozen": "0",
                "avail": "0"
            }
        ]
    }
}
```

### 查询币币交易报表（id倒序）
```
GET trades/exchange
```

#### 参数说明
| 参数         | 必填 | 类型           | 说明                         |
| ----------- | ---- |-------------  | ---------------------------- |
| startDate   | 否   | String        | 开始日期，yyyyMMdd              |
| endDate     | 否   | String        | 截止日期，yyyyMMdd              |
| coin        | 否   | String        | 币种code                       |
| cursor      | 否   | String        | 游标                           |
| size        | 否   | Integer       | 显示条数，10 - 100              |

#### 返回说明
```
{
    "data": {
        "items": [
            {
                "mobile": "+86.15800919478",
                "coin": "USDT",
                "amount": "40374.88",
                "fee": "40.37488002",
                "income": "32.299904016",
                "date": "20190911",
                "cnt": 10
            },
            {
                "mobile": "+86.18018629787",
                "coin": "BTC",
                "amount": "0.01",
                "fee": "0.00001",
                "income": "0.000008",
                "date": "20190911",
                "cnt": 3
            },
            {
                "mobile": "+86.18721391902",
                "coin": "BTC",
                "amount": "4",
                "fee": "0.004",
                "income": "0.0032",
                "date": "20190911",
                "cnt": 8
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
| coin             | 币种code                  |
| amount           | 交易量                    |
| fee              | 手续费                    |
| income           | 手续费渠道分成              |
| date             | 日期                      |
| cnt              | 交易次数                  |
| hasNext          | 是否有下一条               |
| nextCursor       | 游标(下一条的id)           |

### 查询微盘交易报表（id倒序）
```
GET trades/wp
```

#### 参数说明
| 参数         | 必填 | 类型           | 说明                         |
| ----------- | ---- |-------------  | ---------------------------- |
| startDate   | 否   | String        | 开始日期，yyyyMMdd              |
| endDate     | 否   | String        | 截止日期，yyyyMMdd              |
| cursor      | 否   | String        | 游标                           |
| size        | 否   | Integer       | 显示条数，10 - 100              |

#### 返回说明
```
{
    "data": {
        "items": [
            {
                "mobile": "+86.15800919478",
                "coin": "USDT",
                "position": "0",
                "fee": "0",
                "income": "0",
                "date": "20190717",
                "cnt": 9
            },
            {
                "mobile": "+86.13311760298",
                "coin": "USDT",
                "position": "14.025",
                "fee": "0",
                "income": "0",
                "date": "20190716",
                "cnt": 4
            },
            {
                "mobile": "+86.15800919404",
                "coin": "USDT",
                "position": "-4",
                "fee": "0.05",
                "income": "0.05",
                "date": "20190614",
                "cnt": 2
            },...
        ],
        "hasNext": false,
        "nextCursor": null
    }
}
```

#### 字段说明
| 字段              |  说明                     |
| -----------      |  ----------------------- |
| position         | 头寸                      |

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

### 用户账户快照
```
GET user/account/snapshot
```

#### 参数说明
| 参数         | 必填 | 类型           | 说明                          |
| ----------- | ---- |-------------  | ---------------------------- |
| mobile      | 否   | String        | 手机号                         |
| coin        | 否   | String        | 币种code                      |
| fromDt      | 否   | String        | 开始日期，yyyy-MM-dd           |
| toDt        | 否   | String        | 截止日期，yyyy-MM-dd           |
| page        | 否   | Integer       | 当前页码                       |
| size        | 否   | Integer       | 显示条数，10 - 100              |

#### 返回说明
```
{
    "data": {
        "items": [
            {
                "mobile": "+86.13585741786",
                "settleDate": "2019-7-9",
                "coin": "BCH",
                "balance": "0.03",
                "frozen": "0"
            },
            {
                "mobile": "+86.13585741788",
                "settleDate": "2019-7-9",
                "coin": "BCH",
                "balance": "0.01",
                "frozen": "0"
            },
            {
                "mobile": "+86.18018629787",
                "settleDate": "2019-7-9",
                "coin": "BCH",
                "balance": "0.22",
                "frozen": "0"
            },...            
        ],
        "totalPages": 201,
        "totalItems": 10013
    }
}
```

#### 字段说明
| 字段              |  说明                     |
| -----------      |  ----------------------- |
| settleDate       | 日期                      |
| balance          | 账户余额（含冻结）           |
| frozen           | 账户冻结                   |