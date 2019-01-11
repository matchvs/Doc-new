/*
Title: 断线重连
Sort: 25
*/

# 断线重连

Matchvs 提供了断线重连的功能：当客户端网络异常（包含网络关闭、弱网络、挂起至后台等情况），网络异常时应用层会收到“检测到客户端已经断线的`errorResponse` 错误码1001。此时可以调用SDK reconnect()接口进行重连（断线重连接口调用不要直接写在 errorReponse 回调后面，不然会出现反复断线反复重连死循环情况）。在reconnectResponse 接口可以得到重连的结果，如果调用重连成功，断线的用户将收到 reconnectResponse 接口返回 200 状态值，客户端需要把游戏定位到房间或者游戏界面还原游戏场景，其他用户会收到 networkStateNotify 接口的回调信息，根据 state 判断用户是否重连成功。 如果重连失败则会在 reconnectResponse 接口收到错误码，当错误码为 201 的时候说明你已经不能再加入房间了，这个时候你将处于已经登录状态，不用再次调用 login 接口。断线重连的时间默认用户断线20秒内可以重连回房间，如果超过20秒后，就不能重连回房间。



**断线重连超时时间默认是 20秒，可以由开发者自己调用 setReconnectTimeout 自己设置超时时间（可支持的SDK版本 v3.7.5.0+）** ，需要每次进入房间之前设置超时时间。

断线重连接口使用说明请看 [API文档](../APIDoc/JavaScript)    

#### 玩家重连结果通知

![](http://imgs.matchvs.com/static/reconnect4.png)  

reconnectResponse接口是重连的结果。

- 重连如果成功接口会返回 200 的 status。并且会收到 hotel 的心跳日志。这个时候用户处于房间内，用户可以进行房间内的所有操作，比如 sendEvent ， sendEvenEx， kickPlayer, setFrameSync, sendFrameEvent 等等。
- 重连如果返回 201，是因为你断线的时间过久不能重连进入房间了。这个时候玩家是处于已经登录状态，可以看到 gateway 的心跳日志。玩家不用再次调用 login 了。玩家可以进行 joinRandRoom ，createRoom 等操作。

#### 其他玩家重连结果通知

如果服务端检测到客户端网络异常，则服务端会通过`networkStateNotify`告诉其他客户端“检测到客户端C已断线，正在进行重连”。如果重连成功，服务端会通知其他客户端“客户端C已经重连成功”；如果重连失败，服务端会通知其他客户端“客户端C重连失败”。

![](http://imgs.matchvs.com/static/reconnect2.png)

networkStateNotify 接口 state 结果值

- 如果有用断线了 state 为 1。
- 如果用户正在重新连接 state 为 2.
- 如果用户用户不能 再重连了彻底离开了房间 state为 3。
- 如果用户重连成功了 networkStateNotify 接口没有通知，需要玩家自己使用 sendEvent 接口发送消息 告诉其他人。

## 重连时间设置

断线重连超时时间可以通过 setReconnectTimeout 接口设置，每次进入房间都要调用这个接口，设置才能生效，如果进入房间前没有设置，那么就会变成默认时间 20 秒。 超时时间不能设置 0。接口说明可查看这里  [API文档](../APIDoc/JavaScript)   



## 重连方法

玩家断线后在超时时间内可调用reconnect接口重连到原来房间，无论房间处于哪一种状态都可以连上。重连回房间使用方式有两种。

```
1、玩家断开后触发 reconnect 接口。
2、玩家断开后回到登录界面重新登录，loginResponse 返回参数 roomID 不为0 代表可以重新连接。这个时候调用reconnect 重连回房间。
```

> 注意：玩家断开后马上重连，不能写在 errorResponse 中，这样可能会出现无限重连的情况。最好是使用按钮，或者设置重连次数和重连触发时间。



## 断线 login 问题

在房间内的玩家断线后调用 login 接口，loginResposne 的返回值 roomID 会是你原来房间的 roomID，如果你需要加入原来的房间，就直接调用 reconnect 接口。如果你想要加入原来房间，你可以无视这个 roomID，按照正常的逻辑 加入新房间即可。

> 注意：login 后，roomID 有值也不用 调用 leaveRoom 离开房间。不想重连会房间就不用管这个值。





