# 接口定义和场景
!>按场景调用业务能力。Last update：{docsify-updated}

# 通用规范

## 请求地址

{环境地址}/api/{接口名称}

## 环境地址

测试环境：
生产环境：

## 请求规范

客户端和服务端采用http通讯，数据发送为表单POST方式或GET方式，建议使用POST。返回数据JSON字符串。由完整请求地址不同控制访问不同的接口。报文的输入格式如reqHead、reqBody，输出resHead、resBody。其中所有请求头附加了部分通用业务字段（小写），定义如下：

* 业务系统的标准reqHead：

| 字段 | 类型（长度） | 必填 | 说明 |
|---|---|---|---|
| chid | string | 是 | 请求渠道号，由本系统分配，请咨询项目经理 |
| txcode | string | 是 | 请求流水号，建议20位，业务系统生成渠道内唯一编号，至少当日唯一 |
| txtime | string(14) | 是 | yyyymmddhhmmss |
| botcode | string | 是 | 机器人编号，跟微信号一一对应，用于绑定模拟客户端实例作用域 |
| sign | string | 是 | 详见鉴权规则 |

* WXRC系统的标准resHead：

| 字段 | 类型 | 说明 |
|---|---|---|
| 无附加 |  |  |

## 鉴权规则

* 客户端请求签名步骤:

拿到HTTP请求body中的JSON串，然后将渠道接入KEY拼接到JSON串后面生成新的字符串，对新串做MD5签名，并在HTTP请求时，在HTTP HEADER中加入X-Ecaspsplus-Signature:签名串  及 X-Ecaspsplus-SignatureType:MD5,签名统一使用UTF-8编码格式.

# 登录场景

* 用户场景说明

允许用户启动模拟微信客户端，并发起登录，支持2种场景：
①未登录状态下发起登录，即首次登录，大约5秒会异步返回二维码。
②登录状态下发起登录，比如WXRC系统重启后，在和微信的心跳连接没有断开之前，业务系统发起登录，WXRC系统可能会用本地登录信息自动登录到微信端，并不会返回二维码，但会异步通知登录成功事件，所以理论上不是必须要用到状态查询接口。

我们不考虑手机号密码登录等其他验证方式

## 网页扫码登录接口{login}

* reqBody：无
* resBody：

| 字段 | 类型 | 说明 |
|---|---|---|
| rtCode | string | 响应码 |
| rtMsg | string | 响应消息 |

接口说明：由于存在过长启动和自动登录，用于登录的二维码会改异步通知过来，这里并不会直接返回。

## mac登录接口{macLogin}

暂无，以示和普通web登录区别


## 登录状态查询接口{botStatus}

本接口暂无

* reqBody：无

* resBody：

| 字段 | 类型 | 说明 |
|---|---|---|
| botStatus | string | 机器人当前登录状态，logout代表未登录，login代表已登录 |
| rtCode | string | 响应码 |
| rtMsg | string | 响应消息 |

# 发送消息给用户

* 场景说明

命令机器人在指定群、向指定人发送消息。支持多种类型，先支持三种最基础的消息：
* 发送文本消息，业务系统的关键字回复、工单状态同步通知、报表通知均需要用到
* 发送小程序，主要面向关键字回复场景，报表通知也可以采用
* 发送图片，这个优先级次之。

## 群发送文本消息{roomSendText}

* reqBody：

| 字段 | 类型（长度） | 必填 | 说明 |
|---|---|---|---|
| text | string | 是 | 需要发送的文字 |
| roomTopic | string | 否 | 发送的目标群名称 |
| atList | Array<string> | 否 | @的对象清单，暂不支持微信客户端@人，我们会采用假的@，等于是不能产生飘红提醒 |


* resBody:

| 字段 | 类型 | 说明 |
|---|---|---|
| msgid | Number | 考虑到并发问题，系统计划给收到的消息编号，记录需求和状态，前期还木有 |
| sendStatus | string | 发送状态，0代表排队中，1代表已发送，2表示群不存在，将不会发送 |
| rtCode | string | 响应码 |
| rtMsg | string | 响应消息 |

## 发送小程序消息{roomSendApp}

* reqBody：

本接口需要等license拿到后才能测试

* resBody:

| 字段 | 类型 | 说明 |
|---|---|---|
| msgid | Number | 考虑到并发问题，系统计划给收到的消息编号，记录需求和状态，前期还木有 |
| sendStatus | string | 发送状态，0代表排队中，1代表已发送，2表示群不存在，将不会发送 |
| rtCode | string | 响应码 |
| rtMsg | string | 响应消息 |

* 小程序卡片对象示例：

{  
    appid：'小程序id，小程序后台获取到',  
    username:'小程序名称，小程序后台获取到',  
    description:'小程序描述',  
    pagepath:'页面路径',  
    title:'页面标题',  
    thumbnailUrl:'卡片图片'  
}

![avatar](./image/minApp.png)

## 发送图片消息

优先级往后
