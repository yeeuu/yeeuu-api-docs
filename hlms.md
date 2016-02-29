# 云柚酒店门锁管理系统API

|更新时间|2016-02-29|
|:--|:--|
|API版本| 1.0.0|
|状态|正式|
|作者|Kevin \<kevin at yeeuu dot com\>|
|更新纪录|2016/02/29 更新部分描述。<br>2016/01/20 更新Java SDK地址。<br>2015/11/23 添加约定内容和使用逻辑。<br>2015/10/29 去除相关无用API说明。<br>2015/10/21 分离微信开门API。<br>2015/09/22 更新微信开门页面信任登录方式。<br>2015/09/16 API链接切换成HTTPS方式，更加安全。支持SPDY3.1协议连接。<br>|

### 目录

* [SDK](#sdk)

* [相关约定](#相关约定)

* [授权信息](#授权信息)   
  * [获取所有房间授权信息](#获取所有房间授权信息)
  * [获取单个房间授权信息](#获取单个房间授权信息)
  * [更新单个房间授权信息](#更新单个房间授权信息)
  * [清除单个房间授权信息](#清除单个房间授权信息)

* [微信开门](#微信开门)

* [调试](#调试)
  * [获取服务器时间](#获取服务器时间)

## SDK

下文中提到的SDK已经进行封装，可以进行快速的调用。目前支持Java语言版本的SDK。

[HLMS-Java-SDK](https://github.com/yeeuu/hmls-java-sdk)

## 相关约定

在使用HLMS API之前，请了解以下约定内容：

约定内容|约定说明|
|:----|:----|
|API调用基地址|__https://hlms.yeeuu.com/api__|
|调用请求数据格式|请求数据格式为```form```表单格式。|
|URL中的{xxx}|在URL中会包含{xxx}类型数据，这些数据代表需要根据实际情况进行替换。通常在一般语言中的```%s```类似。|
|必需参数与可选参数|如非特殊说明为可选参数，所有参数均为必需参数，不可选择省略或为空。|
|hotel\_id|代指酒店ID，在调用API接口过程中需要提供此项数据用于处理。<br>```hotel_id```最长30个字符。|
|timestamp|[Unix时间戳](http://baike.baidu.com/view/821460.htm)，10位长度数字，单位为秒，如```1448091239```。<br>API接口中所有时间均以timestamp格式传递。|
|secret|代指每个酒店密钥，调用相关接口时用语生成对应的签名信息。<br>密钥长度在50个字符内。|
|nonstr|随即字符串，长度不做限制，不可为空。生成签名时增强安全性使用，不做额外用途。|
|sign|签名字符串，使用SHA1算法生成的HEX值，签名数据均为字符串格式，数字类型需转换为对应字符串。|
|返回数据格式|为方便处理，在成功调用云柚接口后会返回JSON数据，同时```Content-Type```被设置为```application/json```。|


## 授权信息

授权管理相关API.

### 获取所有房间授权信息

__请求地址：__ ```/auth/?timestamp={timestamp}&nonstr={nonstr}&hotel_id={hotel_id}&sign={sign}```

__请求方式：__ ```GET```

__请求参数说明：__

参数名|参数说明|参数示例
-----|-------|------
timestamp|请求提交的当前时间时间戳|1448091239
nonstr|当前加密签名使用的随机字符串|123456|
hotel_id|酒店对应的ID号|dd2edb87ea9eb7a32fd4057276d3a1
sign|签名算法为sha1(timestamp+secret+hotel_id+nonstr)|dd2edb87ea9eb7a32fd4057276d3a1fab861c1d5

__返回值说明：__

参数名|参数说明
-----|-------
status|调用成功，此值固定为200
msg|调用成功时，此处为返回数据

__请求示例：__

```http
https://hlms.yeeuu.com/api/auth/?timestamp=1448091239&nonstr=1448091239&hotel_id=dd2edb87ea9eb7a32fd4057276d3a1&sign= dd2edb87ea9eb7a32fd4057276d3a1fab861c1d5
```

__调用成功示例：__

```json
        {
            "status": 200,
            "msg": [
                {
                    "room": "101",  // 房间号
                    "auth": {
                        "phone": ["12345678901", "10987654322"], //入住人欣喜
                        "type": 0, //房间入住类型，无意义。
                        "end": 1448091239,   // 授权生效时间
                        "start": 1448091239  // 授权失效时间
                    }
                },
                {
                    "room": "102",
                    "auth": {
                        "phone": [],
                        "type": 1,
                        "end": 0,
                        "start": 0
                    }
                }
            ]
        }
```

__调用失败：__

调用失败时，会出现HTTP状态码非200的情况，可能出现以下问题：

状态码|状态码说明
:----|:-------
400|缺少必需参数
401|签名信息不正确
408|时间戳超过3分钟时间差
500|服务器端错误
503|指定酒店暂时不支持API方式调用

### 获取单个房间授权信息

__请求地址：__ ```/auth/{room}?timestamp={timestamp}&nonstr={nonstr}&hotel_id={hotel_id}&sign={sign}```

__请求方式：__ ```GET```

__请求参数说明：__

参数名|参数说明|参数示例
-----|-------|------
room|房间名|101
timestamp|请求提交的当前时间时间戳|1448091239
nonstr|当前加密签名使用的随机字符串|123456|
hotel_id|酒店对应的ID号|dd2edb87ea9eb7a32fd4057276d3a1
sign|签名算法为sha1(timestamp+secret+hotel_id+nonstr)|dd2edb87ea9eb7a32fd4057276d3a1fab861c1d5

__返回值说明：__

参数名|参数说明
-----|-------
status|调用成功，此值固定为200
msg|调用成功时，此处为返回数据

__请求示例：__

```http
https://hlms.yeeuu.com/api/auth/101?timestamp=1448091239&nonstr=1448091239&hotel_id=dd2edb87ea9eb7a32fd4057276d3a1&sign= dd2edb87ea9eb7a32fd4057276d3a1fab861c1d5
```

__调用成功示例：__

```json
        {
            "status": 200,
            "msg": {
                "auth": {  //授权信息
                    "phone": [],  // 被授权的手机号码列表，若不存在，该列表为空。
                    "type": 0,    // 房间类型，仅标识
                    "end": 0,     // 授权结束时间时间戳
                    "start": 0    // 授权生效时间时间戳
                }
            }
        }
```

__调用失败：__

调用失败时，会出现HTTP状态码非200的情况，可能出现以下问题：

状态码|状态码说明
:----|:-------
400|缺少必需参数
401|签名信息不正确
404|该房间不存在
408|时间戳超过3分钟时间差
500|服务器端错误
503|指定酒店暂时不支持API方式调用

### 更新清除单个房间授权信息

__请求地址：__ ```/auth/{room}?timestamp={timestamp}&nonstr={nonstr}&hotel_id={hotel_id}&sign={sign}```

__请求方式：__ ```POST```

__请求参数说明：__

参数名|参数说明|参数示例
-----|-------|------
room|房间名|101
timestamp|请求提交的当前时间时间戳|1448091239
nonstr|当前加密签名使用的随机字符串|123456|
hotel_id|酒店对应的ID号|dd2edb87ea9eb7a32fd4057276d3a1
sign|签名算法为sha1(timestamp+secret+hotel_id+nonstr)|dd2edb87ea9eb7a32fd4057276d3a1fab861c1d5
phone|授权手机号码，可以出现一次，也允许多次出现。|13400000000
start|授权生效时间Unix时间戳。|1448091239
end|授权失效时间Unix时间戳。|1448091240
type|恒为0|0

__返回值说明：__

参数名|参数说明
-----|-------
status|调用成功时，此值为200
msg|调用成功时，此处为提示信息

__请求示例：__

```http
https://hlms.yeeuu.com/api/auth/101?timestamp=1448091239&nonstr=1448091239&hotel_id=dd2edb87ea9eb7a32fd4057276d3a1&sign= dd2edb87ea9eb7a32fd4057276d3a1fab861c1d5

phone=13400000000&phone=18600000000&start=1448091239&end=1448091240&type=0
```

__调用成功示例：__

```json
{
    "status": 200,  // 非200有错误提示
    "msg": "授权成功"
}
```

__调用失败：__

调用失败时，会出现HTTP状态码非200的情况，可能出现以下问题：

状态码|状态码说明
:----|:-------
400|缺少必需参数
401|签名信息不正确
404|该房间不存在
408|时间戳超过3分钟时间差
500|服务器端错误
503|指定酒店暂时不支持API方式调用

### 清除单个房间授权信息

__请求地址：__ ```/auth/{room}?timestamp={timestamp}&nonstr={nonstr}&hotel_id={hotel_id}&sign={sign}&phone={phone}```

__请求方式：__ ```DELETE```

__请求参数说明：__

参数名|参数说明|参数示例
-----|-------|------
room|房间名|101
timestamp|请求提交的当前时间时间戳|1448091239
nonstr|当前加密签名使用的随机字符串|123456|
hotel_id|酒店对应的ID号|dd2edb87ea9eb7a32fd4057276d3a1
sign|签名算法为sha1(timestamp+secret+hotel_id+nonstr)|dd2edb87ea9eb7a32fd4057276d3a1fab861c1d5
phone|可选参数，指定手机号码时清除单独用户授权，可无此参数。|13400000000

__返回值说明：__

参数名|参数说明
-----|-------
status|调用成功，此值固定为200
msg|调用成功时，此处为返回数据

__请求示例：__

```http
https://hlms.yeeuu.com/api/auth/101?timestamp=1448091239&nonstr=1448091239&hotel_id=dd2edb87ea9eb7a32fd4057276d3a1&sign= dd2edb87ea9eb7a32fd4057276d3a1fab861c1d5
```

__调用成功示例：__

```json
{
    "status": 200,  // 非200有错误提示
    "msg": "清除成功"
}
```

__调用失败：__

调用失败时，会出现HTTP状态码非200的情况，可能出现以下问题：

状态码|状态码说明
:----|:-------
400|缺少必需参数
401|签名信息不正确
404|该房间不存在
408|时间戳超过3分钟时间差
500|服务器端错误
503|指定酒店暂时不支持API方式调用

## 微信开门
        
请查看[云柚微信开门页面API](http://docs.weixinopen.apiary.io/)

## 调试

### 获取服务器时间 [/timestamp/]

用于校准本地与服务器时间。

__请求地址：__ ```/timestamp/```

__请求方式：__ ```GET```

__返回示例：__

```json
{"timestamp": 1440042147}
```
