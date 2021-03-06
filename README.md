# 版本号 （V1.1.0 ）
V1.1.0    
北京有链科技有限公司

### 版本说明
* V1.0.0    
2019-06-28 添加统一下单接口，订单查询接口，支付回调通知
* V1.1.0    
2019-07-24 添加用户资产接口，发放奖励接口、登录授权接入示例
* V1.2.0    
2019-12-03 添加用户YOU行情接口

### 接入前期准备

在接入有令开放平台前，请在开放平台中注册开发者账号，创建应用，获得 appid 和 appsecret，配置相关参数。

[申请应用](./apply.md)


### 本文阅读对象
供有令dapp开发人员参考和查询。

### 环境
```
测试环境：https://dev-open.youchainapi.com
正式环境：https://open.youchainapi.com
```

### 开放平台API 返回语义

```
{
  "ret":0, // 0 表示正常， >0 表示发生错误，数值为错误号
  "data":data, // 可能是任何类型
  "msg":"error msg" // 发生错误时，才会有该字段
}
```

# 授权登录接口 
（需 scope 为 snsapi_base）

请参考 [【授权登录文档】](auth.md) [【授权登录接入示例】](auth-simple.md)

# 获取用户信息接口
（需 scope 为 snsapi_userinfo）

请参考 [【用户信息API文档】](api.md)

# 支付接口
（需dapp已绑定用于收款的有令用户id）

请参考 [【支付API文档】](payment.md)     [【dapp-JS API文档】](jsapi.md) 

# 用户资产接口 
（需 scope 为 snsapi_asset）    

请参考 [【用户资产API文档】](asset.md)

# 发放奖励接口 
（需 scope 为 snsapi_reward）     
（需dapp已绑定用于付款的有令用户id）     

请参考 [【奖励接口API文档】](reward.md)

# YOU价格接口 
（需 scope 为 snsapi_base）    

请参考 [【YOU价格API文档】](price.md)

# 附录

### 随机数生成 

有令开放API接口协议中包含字段nonceStr，主要保证签名不可预测。我们推荐生成随机数算法如下：使用不带"-"的uuid。

### 数字签名规则
请参考 [【数字签名规则】](sign.md) 

### 支付及奖励支持的币种
已开通支付和奖励的dapp默认支持you支付和奖励you，其它币种需要单独申请   

| 币种（小写）    | 说 明             |
| ---           | ---               |   
| you           | YOU               |
| copper_coin   | 有令创世纪念铜币    |
| whale_coin    | 鲸鱼创世通证幸运星  |

### 公共错误码

请参考 [【公共错误码】](error.md)

# 支付接入异常情况重要说明 

- 1、提示：客户端版本错误
  * [重要提醒] 确保 js 能够注入，否则不能与客户端进行交互。实例创建成功不能代表 js 注入成功。
  * 请确保 SDK 初始化成功。即可通过监听版本信息，是否有回调。
  
- 2、提示：appid 错误
  * 确认接入 appid、appSecret、mch_id 是否配置正确。
  * 确认使用环境是否使用正确。测试APP，对应 dev-open，对应测试 appid ; 线上APP，对应 open，对应线上 appid 。
  * 请自行抓取相关接口，进行确认是否使用正确。
  * 如若还存在问题，请联系相关人员，配合查找问题。
  
- 3、提示：用户余额不足
  * 联系相关人员，提供测试账号。我们会对测试账号转账一笔 YOU 。  

- 4、iframe 嵌套问题
  * 存在 iframe 嵌套的情况时，sdk 不能与 [有令客户端App] 通过 onMessage 进行通信，会导致支付流程有问题。 