/*
Title: 用户登录验证
Sort: 5
*/
配置游戏玩家登录时是否要进行 token 的合法性验证，默认是不需要。

为了确保游戏玩家信息安全，正式运营的游戏需要开启该项登录验证。

![](http://imgs.Matchvs.com/static/deploy/8.png)

## 验证接口规范及使用说明

例如用户配置检测URL为`http://token.Matchvs.com/check`，引擎接收到SDK login请求后会发送如下验证请求 `http://token.Matchvs.com/check?gameID=1000&user=x&token=xxxxx`，引擎根据http响应状态码来判定验证是否通过，`statusCode==200`表示验证通过。时序图如下：

![](http://imgs.Matchvs.com/static/deploy/200.png)

## 设置验证配置

```sh
Usage:
  matchvs_tool token add gameID needCheck checkURL
```

- gameID为已添加的游戏ID
- needCheck是否需要验证token，0：不需要；1：需要
- checkURL验证token的URL

假设我们现在要给gameID为1000的游戏添加token验证，URL为http://www.Matchvs.com/。

执行`./matchvs_tool token add 1000 1 http://www.Matchvs.com/`

![](http://imgs.Matchvs.com/static/deploy/9.png)

## 查询验证配置

执行`./matchvs_tool token list`，查询所有游戏的用户登录验证配置信息。

![](http://imgs.Matchvs.com/static/deploy/10.png)

### 取消验证配置

假设我们现在要取消gameID为1000的游戏的用户登录验证，执行`./matchvs_tool token delete 1000`

![](http://imgs.Matchvs.com/static/deploy/11.png)