# 交易所 mch API 测试文档

## 说明
测试域名: http://dev-mch.whaletrade.cn:9999

accessKeyId: WKADNJ6TZ9EYR9JG0DDZ

secretAccessKey: 3GYJMKkyxWeSlURCpG2bsQVXkXAlaA4b

service: mch

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
| url              | 是   | String              | 请求url，如：http://dev-mch.whaletrade.cn:9999/mch/order|
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
| url              | 是   | String              | 请求url，如：http://dev-mch.whaletrade.cn:9999/mch/pay_to_customer|
| params           | 否   | Map<String, String> | 请求参数，所有请求参数组装成Map    |
| body             | 否   | Map<String, Object> | 请求body，body中内容组装成Map    |
| accessKeyId      | 是   | String              | 请求方id,见说明                 |
| secretAccessKey  | 是   | String              | 请求方secret,见说明             |
| service          | 是   | String              | 请求服务，见说明                 |  

#### 返回说明
同json格式

## API列表

### 付款给用户
```
POST mch/pay_to_customer
```

#### 参数说明
| 参数         | 必填 | 类型    | 说明                         |
| ----------- | ---- |-----  | ---------------------------- |
| mobile      | 是   | String | 手机号                        |
| coin        | 是   | String | 币种code                      |
| amount      | 是   | String | 数量                          |
| comment     | 否   | String | 备注                          |

#### 返回说明
```
{
    "data": {
        "customer": "+86.15195805918",
        "coin": "BTC",
        "amount": "0.1",
        "orderTs": 223344,
        "comment": null
    }
}
```

#### 字段说明
| 字段              |  说明                      |
| -----------      |  ----------------------   |
| customer         | 用户手机号                  |
| orderTs          | 下单时间                    |

### 订单记录
```
GET mch/order
```

#### 参数说明
| 参数         | 必填                   | 类型    | 说明                         |
| ----------- | ---------------------  |-----  | ---------------------------- |
| orderId     | 否(与outOrderNo二选一)   | String | 订单id                        |
| outOrderNo  | 否(与orderId二选一)      | String | 外部订单号                      |

#### 返回说明
```
{
    "data": {
        "id": "",
        "customer": "+86.15195805918",
        "outOrderNo": "",
        "orderType": "payout",
        "coin": "BTC",
        "amount": "0.1",
        "orderTs": 223344,
        "comment": null
    }
}
```

#### 字段说明
| 字段              |  说明                          |
| -----------      |  --------------------------   |
| id               | 订单id                         |
| orderType        | 订单类型(payout:代扣；income:代付)|