**API调用时序图**

![](http://imgs.matchvs.com/static/时序图.jpg)

## 初始化

**注意** 在整个应用全局，开发者只需要对引擎做一次初始化。

调用下面的代码获取Matchvs引擎对象：

```javascript
var jsMatchvs = require("matchvs.all");
var engine = new jsMatchvs.MatchvsEngine();
```

另外创建一个回调对象，在进行注册、登录、发送消息等操作之后，该对象的方法会被异步调用：

```javascript
var jsMatchvs = require("matchvs.all");
var response = new jsMatchvs.MatchvsResponse();
```

接入下可以调用初始化方法init：

```javascript
engine.init(response, channel, platform, gameID);
```

Matchvs 提供了两个环境，alpha 调试环境和 release 正式环境。游戏开发调试阶段请使用 alpha 环境，即 platform 传参"alpha"。参数列表如下：


gameID获取请[前往控制台](http://www.matchvs.com/manage/gameContentList)

## 注册

**注意** userID和token有需要的可以缓存起来，在之后的应用启动中不必重复获取。如果你有自己的用户系统，可以将Matchvs 提供的 userID 和用户系统进行映射，也可以使用我们提供的第三方账号绑定功能，详情参考[第三方绑定解决方法]()。

Matchvs提供的用户ID被用于在各个服务中校验连接的有效性，调试前开发者需要先获取到一个合法的用户ID。

```javascript
engine.registerUser();
```

> 需要注意：调用此接口成功注册用户后，SDK会缓存用户信息。即使再次调用`regist()`会返回相同的UserID,如需重新注册新的用户须调用SDK的 `LocalStore_Clear()` 函数以清除缓存的用户信息。
>
> 如果需要同时调试多个客户端，则需要打开多个不同的浏览器进行调试。

```javascript
response.registerUserResponse : function(userInfo) {
	// 用户ID
	console.log("userID: ", userInfo.userID);
	// token
	console.log("token: ", userInfo.token);
}
```

## 登录

成功获取用户ID后即可连接Matchvs服务：

```javascript
engine.login(userID, token, gameID, gameVersion, appKey, secret, deviceID, gatewayID);
```


- 其中，appKey，secret，gameID是你创建游戏后从官网获取的信息，可以[前往控制台](http://www.matchvs.com/manage/gameContentList)查看。appkey和secret是校验游戏合法性的关键信息，请妥善保管secret信息。  
- userID 和 token 是第二步 **注册成功** 的回调信息。  
- deviceID 用于检测是否存在多个设备同时登录同一个用户的情况，如果一个账号在两台设备上登录，则后登录的设备会连接失败。
- Matchvs默认将相同游戏版本的用户匹配到一起。如果开发者对游戏进行了版本升级，不希望两个版本的用户匹配到一起，此时可以在登录的时候通过`gameVersion`区分游戏版本。 

登录成功会收到回调 ：

**注意** SDK支持房间断线重连，掉线重新登录后可以选择加入原来的房间，loginResponse里的`roomID` 即为上次异常退出的房间ID。如果登录时没有异常退出的房间，则`roomID`为0。

```javascript
response.loginResponse : function(loginRsp) {
	// 返回值
	var status = loginRsp.status;
	// 房间号
	var roomID = loginRsp.roomID;
}
```

## 加入房间

**注意**  随机匹配不能匹配到客户端主动创建的房间里，即通过`createRoom()`创建的房间。

登录成功后，可以调用Matchvs加入房间逻辑将用户匹配至一个房间开始一局游戏（如：《荒野行动》的开始匹配、《球球大作战》的开始比赛等）。
Matchvs默认提供了随机加入房间的模式，调用加入房间逻辑后，Matchvs服务器会自动帮助用户寻找当前可用房间，只有在同一个房间里的用户才可以互相通信。
随机加入房间的模式下，Matchvs服务器能够快速找到合适的房间，开发者只需要自定义房间人数上限，Matchvs服务端会根据当前房间人数判断是否可继续加入。  
加入房间后，服务器会指定一个房主，当房主主动离开房间后，服务器会随机指定下一个房主，并通过`leaveRoomNotify` 通知房间内其他成员。

```javascript
engine.joinRandomRoom(maxPlayer, userProfile);
```

参数说明:

| 参数       | 含义                  |
| ---------- | --------------------- |
| maxPlayer  | 最大玩家数，不超过100       |
| uerProfile | 玩家简介，可以填写昵称、段位等信息 |

加入房间的回调：

**注意** 如果开发者想用户匹配成功后可查看对方信息，可以通过填充`userProfile`的方式，将当前用户的头像昵称信息填充至`userProfile`，Matchvs会在匹配成功时将`userProfile`广播给所有用户。 

如果用户已经在房间里，此时再次调用加入房间：如果房间未JoinOver，则玩家会退出房间然后随机加入房间；如果房间已经JoinOver，则SDK会返回重复加入的错误提示。

```javascript
response.joinRoomResponse = function(status, roomUserInfoList, roomInfo) {
	console.log("加入房间结果：", status);
	console.log("房间用户列表：", roomUserInfoList);
	console.log("房间信息：", roomInfo);
}
```

## 房间关闭

如果房间内游戏人数已经满足开始条件，此时客户端需要通知 Matchvs 无需再向房间里加人。如:原本设置的房间人数上限为6，而房间人数≥4个即可开始游戏，开发者就需调用停止加入接口。

任意客户端发起`JoinOver`，其他客户端均会收到该房间被`JoinOver`的通知。

**注意：** Matchvs 服务器判断房间**人未满**且**未JoinOver**时，会向房间加人。为避免出现：房间人满开始游戏，在游戏过程中有人退出，此时Matchvs向房间加人。所以建议大家在任何不希望中途加入的游戏里，只要满足开始游戏条件则发送停止加入。

`cpProto` 为开发者自定义的协议内容，如果没有自定义协议可填`''`。`cpProto`的内容会伴随消息的广播以`Notify`的方式发给房间所有成员。其他接口里的`cpProto`机制均是如此。

房间关闭 ：

```javascript
engine.joinOver(cpProto);
```



停止加入的回调 ：

```javascript
response.joinOverResponse = function(joinOverRsp) {
	// 返回值
	console.log("加入房间结果：", joinOverRsp.status);
	// 负载数据
	console.log("负载信息：", joinOverRsp.cpProto);
}
```

## 游戏数据传输

当玩家在同一个房间时，即可互相通信。开发者可用该接口将数据发送给其他玩家，Matchvs默认将数据广播给当前房间内除自己以外的所有用户。

默认广播数据：

```javascript
engine.sendEvent(msg)
```

参数说明:

| 参数   | 含义   |
| ---- | ---- |
| msg  | 消息内容 |

返回结果为一个对象，该对象的属性为：

| 参数       | 含义               |
| -------- | ---------------- |
| result   | 错误码，0表示成功，其他表示失败 |
| sequence | 事件序号，作为事件的唯一标识   |

数据传输回调 ：

```javascript
response.sendEventResponse = function(sendEventRsp) {
	console.log("返回状态：", sendEventRsp.status);
	console.log("事件序号：", sendEventRsp.sequence);
}
```


收到其他人发的数据：

```javascript
response.sendEventNotify = function(eventInfo) {
    console.log("推送方用户ID：", eventInfo.srcUserID);
    console.log("消息内容：", eventInfo.cpProto);
}
```

## 离开房间

在成功加入房间后，开发者可调用离开房间使得用户退出当前房间，退出房间后将不能再与房间内的成员进行通信。  

- 当房间内有用户离开时，剩余用户也会收到离开的消息。
- 如果用户已经离开房间，此时可以随时再次加入其他房间。


离开房间 ：

```javascript
engine.leaveRoom(cpProto);
```



自己离开房间回调 ：

```javascript
response.leaveRoomResponse = function(leaveRoomRsp) {
	console.log("状态返回：", leaveRoomRsp.status);
	console.log("房间ID：", leaveRoomRsp.roomID);
	console.log("用户ID：", leaveRoomRsp.userID);
	console.log("负载信息：", leaveRoomRsp.cpProto);
}
```


其他成员离开房间回调 ：

```javascript
response.leaveRoomNotify = function(leaveRoomInfo) {;
	console.log("房间号：", leaveRoomInfo.roomID);
	console.log("离开房间的用户的ID：", leaveRoomInfo.userID);
	console.log("新房主的ID：", leaveRoomInfo.owner);
	console.log("负载信息：", leaveRoomInfo.cpProto);
}
```

## 游戏登出

如果用户不会再加入游戏，此时可以调用登出与Matchvs服务端断开连接。  

**注意** 游戏退出时，务必要调用登出。

```javascript
engine.logout();
```

登出成功的回调 ：

```javascript
response.logoutResponse = function(status) {
	console.log("状态返回值：", status);
}
```

## 反初始化

在登出后，调用反初始化对资源进行回收。  

```javascript
engine.uninit();
```

[更多完整API介绍请参考API手册](http://www.matchvs.com/service?page=js)
