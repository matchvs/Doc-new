微信小游戏如果没有配置可信域名列表. 需要开启调试模式才可以访问非微信第三方服务器. 造成登录Matchvs失败.

请根据微信[小游戏域名配置文档](https://developers.weixin.qq.com/miniprogram/dev/api/api-network.html?search-key=%E5%9F%9F%E5%90%8D%E9%85%8D%E7%BD%AE&q=)进行配置.
> 服务器域名请在 `小程序后台-设置-开发设置-服务器域名 `中进行配置，配置时需要注意：
域名只支持 https (request、uploadFile、downloadFile) 和 wss (connectSocket) 协议；
域名不能使用 IP 地址或 localhost
域名必须经过 ICP 备案；
出于安全考虑，api.weixin.qq.com 不能被配置为服务器域名，相关API也不能在小程序内调用。 开发者应将 appsecret 保存到后台服务器中，通过服务器使用 appsecret 获取 accesstoken，并调用相关 API。
对于每个接口，分别可以配置最多 20 个域名

## request 合法域名:
https://vsuser.matchvs.com

https://sdk.matchvs.com

https://alphavsuser.matchvs.com

https://vsopen.matchvs.com

https://alphavsopen.matchvs.com



## socket 合法域名:
wss://gateway.matchvs.com

wss://alphagateway.matchvs.com

wss://hotel.matchvs.com

wss://alphahotel.matchvs.com



## 说明
`alpha`开头的域名是开发调试用的服务器的域名；

非 alpha 是线上服务器的域名。