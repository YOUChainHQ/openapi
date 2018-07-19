# 基本概念

有令开放平台授权登陆是基于 OAuth2.0 协议标准构建。

在进行有令授权接入前，请在开放平台中注册开发者账号，创建应用，获得 appid 和 appsecret，配置相关参数。

# 授权流程说明

整体流程如下：

```
1. 第三方发起授权在用户同意授权第三方应用之后，开放平台会拉起第三方应用或者重定向第三方网站，带上临时票据 code
2. 第三方应用通过 code 参数，带上 appid 和 appsecret，通过调用 API 换取 access_token
3. 第三方应用，通过 access_token 进行接口调用，获取用户信息和实现操作
```

如下图：

![时序图](./arts/oauth20-sequence.png)

# 第一步：请求 code

### 引导用户打开如下页面

```
https://open.youchainapi.com/connect/oauth2/authorize?appid=APPID&redirect_uri=REDIRECT_URI&response_type=code&scope=SCOPE&state=STATE
```

参数说明：

| 参数        | 必须    |  说明  |
| --------   | -----:   | :----: |
| appid        | 是      |   应用的 appid    |
| redirect_uri        | 是     |   授权后重定向的链接地址，请使用 urlEncode 对链接进行处理，并保证与应用管理中心中的配置一致    |
| response_type        | 是     |   必须是 code     |
|scope|是|授权域，snsapi_base 可以获取 openid，snsapi_userinfo 可以获取用户基本信息，snsapi_asset 可以获取用户资产相关信息，后一种 scope 包含前一种 scope|
|否|是|重定向后会带上state参数，开发者可以填写a-zA-Z0-9的参数值，最多128字节|

下图说明授权页面：

//todo

### 用户授权后

如果用户同意授权，页面将跳转至 redirect_uri/?code=CODE&state=STATE。

```
code 说明 ： code 作为换取 access_token 的票据，每次用户授权带上的 code 将不一样，code 只能使用一次，10分钟未被使用自动过期。
```

### 错误说明

|错误号|说明|
| --- | --- |
|10003|redirect_uri 域名与后台配置不一致|

# 第二步：通过 code 换取 access_token

*注意，因为这一步涉及到 appsecret 的使用，十分重要，必须第三方的服务器中发起，同时开放平台也会有 ip 白名单的设置*

### 发起如下请求

```
GET
https://api.youchainapi.com/sns/oauth2/access_token?appid=APPID&secret=SECRET&code=CODE&grant_type=authorization_code
```

参数说明：

| 参数        | 必须    |  说明  |
| --------   | -----:   | :----: |
| appid        | 是      |   应用的 appid    |
| secret        | 是     |   应用的 appsecret    |
|code|是|填写第一步获取的 code 参数|
| grant_type        | 是     |   必须是 authorization_code     |

返回说明：

正确返回时的 json 数据如下：

```
{ 
"access_token":"ACCESS_TOKEN",
"expires_in":7200,
"openid":"OPENID",
"scope":"SCOPE" 
}
```

|字段|说明|
| --- | --- |
|access_token|调用此用户信息 API 的凭证|
|expires_in|access_token 接口调用凭证超时时间，单位（秒）|
|openid|用户唯一标识|
|scope	|用户授权的作用域|

错误返回，示例为 code 无效错误：

```
{"errcode":40029,"errmsg":"invalid code"}
```

# 第三步：拉取用户信息(需scope为 snsapi_userinfo)

### 发起如下请求

```
GET
https://api.youchainapi.com/sns/userinfo?access_token=ACCESS_TOKEN&openid=OPENID&lang=zh_CN
```

参数说明：


| 参数        | 必须    |  说明  |
| --------   | -----:   | :----: |
| access_token        | 是      |   上一步拿到的 access_token  |
| openid        | 是     |  用户唯一标识    |
|lang|是|国家地区和语言版本|

返回说明

正确时返回的JSON数据包如下：

```
{
"openid":" OPENID",
"username": USERNAME,
"gender":"1",
"province_name":"PROVINCE_NAME"
"country_name":"COUNTRY_NAME",
"avatar":"https://ucimg.ihuanqu.com/avatar/54f69c/6b83f463310db6b2c8181d09fc-1600614462193664.jpg",
}
```

|字段|说明|
| --- | --- |
|openid|用户的唯一标识|
|username|用户名|
|gender|性别 0 未设置 1 男 2 女|
|province_name	|省份|
|country_name	|国家|
|avatar	|头像|

错误返回，示例为 openid 无效:

```
{"errcode":40003,"errmsg":" invalid openid "}
```
