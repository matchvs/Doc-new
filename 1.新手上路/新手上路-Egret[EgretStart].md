# Egret集成Matchvs

在Egret游戏开发引擎中使用 Matchvs 游戏联网引擎，快速的开发联网对战游戏。

## Egret 环境配置

#### 1、Lanucher 安装。

- [Lanucher 下载](https://www.egret.com/) 

#### 2、安装egret引擎版。

打开Lanucher选择对应的egret引擎版本下载安装（egret更新较快建议安装最新的Stableb版本），如下图：

![](Egret新手上路img\egret_start1.png)

#### 3、安装IDE工具Wing

打开Lanucher 定位到工具页面，下载wing并安装。

## Matchvs 集成



#### 1、创建egret游戏开发项目。

打开Lanucher 定位到项目页面。创建项目。在项目创建页面勾选Matchvs游戏云。并创建。

![](Egret新手上路img\egret_start2.png)

#### 2、使用 wing打开项目

创建好项目后，在打开项目的wing左边导航可以的libs目录下可以看到有 matchvs文件夹，如下图：

![](Egret新手上路img\egret_start3.png)

#### 3、替换matchvs文件夹下面的内容

到 [Matchvs官网下载](http://www.matchvs.com/serviceDownload) TypeScript版本的 MatchvsSDK 解压并替换 egret项目`matchvs`文件下的三个文件(matchvs.d.ts、matchvs.js、matchvs.min.js)。

> 注意：如果不替换 matchvs官网下载的 SDK就无法使用 Matchvs账号在Matchvs官网创建的游戏服务。



以上步骤已经完美集成MatchvsSDK，接下来就开启你的 联网对战之旅。

## Matchvs 使用

在使用 Matchvs 服务之前请确认您已经在 Matchvs官网注册账号并新建好了一款游戏服务。如果还没有注册Matchvs 游戏服务请参考 [Matchvs游戏注册]() 文档。

Matchvs SDK 接口服务分为 **请求服务** 和 **回调服务** ， 使用是以简单的接口调用和接口返回的方式实现相关联网操作。比如随机加入房间只需要调用`joinRandRoom接口`，加入房间结果就以接口 `joinRoomResponse` 返回。在整个使用过程中，开发者只需要关心`MatchvsEngine`(接口请求调用对象)和 `MatchvsResponse`(接口调用返回对象)。接口请求使用 `MatchvsEngine`对象实例，接口返回使用 `MatchvsResponse` 对象实例。后面后介绍这两个对象的使用方法。此文档只是用于引导开发者接入SDK，需要接口详细的参数说明请看 [API手册](http://www.matchvs.com/service?page=js)  

#### Matchvs 变量定义

定义两个全局唯一的Matchvs 服务变量，一个是  `MatchvsEngine` 接口请求对象变量，另一个是`MatchvsResponse` 接口回调对象。示例代码如下：

```typescript
let engine:MatchvsEngine = new MatchvsEngine();
let response:MatchvsResponse = new MatchvsResponse();
```

在egret 项目中使用示例如下：

```typescript
//这Main是Egret项目中的入口
class Main extends eui.UILayer {
    ......
    private engine:MatchvsEngine = new MatchvsEngine();
	private response:MatchvsResponse = new MatchvsResponse();
	......
}

```

> 注意：请确保两个对象是全局唯一的，您可以根据自己的喜好进行封装，使用静态变量或者单例类都可以。



#### Matchvs 初始化

获取到对象实例后，需要开发者把 `MatchvsResponse` 实例注册到 `MatchvsEngine` 用于注册、登录、加入房间等接口请求后的异步回调。调用 `init` 接口初始化SDK。

请求示例：

```typescript
engine.init(response, "Matchvs", "alpha", 201016);
```

Matchvs 提供了两个环境，alpha 调试环境和 release 正式环境。游戏开发调试阶段请使用 alpha 环境，即 platform 传参"alpha"，具体可参考 [环境说明]()

参数说明:

| 参数     | 含义                                          |
| :------- | --------------------------------------------- |
| response | 回调对象(`MatchvsResponse` 实例)              |
| channel  | 渠道，填“Matchvs”即可                         |
| platform | 平台，调试环境填“alpha” ，正式环境填“release” |
| gameID   | 游戏ID，来自 Matchvs 官网控制台创建的游戏信息 |

> **注意** 在整个应用全局，开发者只需要对引擎做一次初始化

回调示例:

```typescript
response.initResponse = function(status:number){
    .......
}
```

参数说明:

| 参数   | 含义              |
| :----- | ----------------- |
| status | 200-成功 其他失败 |

在egret 项目中使用示例如下：

```typescript
class Main extends eui.UILayer {
    .....
    private runMatchvs(){
        this.response.initResponse = this.initResponse.bind(this);
        this.engine.init(this.response, "Matchvs", "alpha", 201016);
    }

	private initResponse(status:number){
        if(status === 200){
            console.log("初始化成功!");
        }
    }
}
```

> 注意：this.response.initResponse = this.initResponse; 是指定 engine.init 请求的回调函数，在调用init前需要指定一个回调函数用于处理回调结果，可以在在统一的一个地方处理你将要使用的回调函数。在示例中我们约定在调用`MatchvsEngine` 请求接口前指定回调函数。



#### 注册合法用户

Matchvs提供的 `userID` 被用于在各个服务中校验连接的有效性，调试前开发者需要先获取到一个合法的`userID`。调用registerUser接口获取，在registerResponse回调返回。

请求示例：

```typescript
engine.registerUser();
```

回调示例：

```typescript
response.registerUserResponse = function(userInfo:MsRegistRsp){
    ......
}
```

MsRegistRsp 参数说明

| 参数   | 类型   | 说明            |
| ------ | ------ | --------------- |
| status | number | 0-成功 其他失败 |
| userID | number | 用户ID          |
| token  | string | 合法令牌        |
| name   | string | 随机用户名      |
| avatar | string | 随机头像        |

> **注意** : 每次调用 registerUser 接口都会生成新的 `userID` 为了节省资源消耗， `userID`和 `token` 有需要的可以缓存起来，在之后的应用启动中不必重复获取。如果你有自己的用户系统，可以将Matchvs 提供的 userID 和用户系统进行映射。可以参考 [Matchvs 第三方账号绑定]()，让您的用户唯一对应一个userID，以节省资源。
>
> 为了资源节省，我们在registerUserResponse 回调前把userID信息缓存在本地，数据会暂存在浏览器中。所以使用同一个浏览器调用 registerUser 接口会返回相同的 userID信息。

在egret 项目中使用示例如下：

```typescript
//这是初始化成功的回调，我们初始化成功后调用 registerUser 注册用户
private initResponse(status:number){
    if(status === 200){
        console.log("初始化成功!");
        this.response.registerUserResponse = this.registerUserResponse.bind(this);
        this.engine.registerUser();
    }
}
//注册用户回调
private registerUserResponse(userInfo:MsRegistRsp){
    if(userInfo.status === 0){
        console.log("注册成功");
    }
}
```

#### 登录Matchvs SDK 联网服务

成功获取 `userID` 后即可连接Matchvs服务。调用 login 接口登录，loginResponse 回调。

请求示例：

```typescript
engine.login(userInfo.userID, userInfo.token, 201016, 1, "xxxxxappkey", "xxxxxsecret", "v", 0);
```

回调示例：

```typescript
response.loginResponse = function(rsp:MsLoginRsp){
    ......
}
```

参数说明:

| 参数        | 含义                                     |
| ----------- | ---------------------------------------- |
| userID      | 用户ID，调用注册接口后获取               |
| token       | 用户token，调用注册接口后获取            |
| gameID      | 游戏ID，来自Matchvs官网控制台游戏信息    |
| gameVersion | 游戏版本，自定义，用于隔离匹配空间       |
| appkey      | 游戏Appkey，来自Matchvs控制台游戏信息    |
| secret      | secret key，来自Matchvs控制台游戏信息    |
| deviceID    | 设备ID，用于多端登录检测，请保证是唯一ID |
| gatewayID   | 服务器节点ID，默认为0                    |

- 其中，appKey，secret，gameID是你在Matchvs官网创建游戏后获取的信息，可以[前往控制台](http://www.matchvs.com/manage/gameContentList)查看。appkey和secret是校验游戏合法性的关键信息，请妥善保管secret信息。  
- userID 和 token 是调用 registerUser 接口 **注册成功** 的回调信息。
- deviceID 用于检测是否存在多个设备同时登录同一个用户的情况，如果一个账号在两台设备上登录，则后登录的设备会连接失败。
- Matchvs默认将相同游戏版本的用户匹配到一起。如果开发者对游戏进行了版本升级，不希望两个版本的用户匹配到一起，此时可以在登录的时候通过`gameVersion`区分游戏版本。 

在egret 项目中使用示例如下：

```typescript
private registerUserResponse(userInfo:MsRegistRsp){
    if(userInfo.status == 0){
        console.log("注册成功");
        this.response.loginResponse = this.loginResponse.bind(this);
        this.engine.login(userInfo.userID, userInfo.token, 201489, 1, "xxxxxappkey", "xxxxxsecret", "v",0);
    }
}

private loginResponse(rsp:MsLoginRsp){
    if(rsp.status === 200){
        console.log("登录Matchvs联网SDK成功");
    }
}
```



#### 随机匹配

登录成功后，可以调用Matchvs加入房间接口，将若干用户匹配至一个房间开始一局游戏（如：《荒野行动》的开始匹配、《球球大作战》的开始比赛等）

Matchvs默认提供了随机加入房间的模式，调用加入房间逻辑后，Matchvs服务器会自动帮助用户寻找当前可用房间，只有在同一个房间里的用户才可以互相通信。

随机加入房间的模式下，Matchvs服务器能够快速找到合适的房间，开发者只需要自定义房间人数上限，Matchvs服务端会根据当前房间人数判断是否可继续加入。  加入房间回调有 joinRoomResponse 和 joinRoomNotify 两个，前者是调用joinRoom者受到是否成功加入房间回调的信息，后者是在房间其他的玩家收到的回调信息。

请求示例：

```typescript
engine.joinRandomRoom(3,"hello matchvs");
```

参数说明:

| 参数       | 含义                                     |
| ---------- | ---------------------------------------- |
| maxPlayer  | 最大玩家数，不超过20（后面会放宽到100）  |
| uerProfile | 玩家自定义内容，可以填写昵称、段位等信息 |

回调示例：

```typescript
this.response.joinRoomNotify = function(roomUserInfo:MsRoomUserInfo){
    ......
}

this.response.joinRoomResponse = function(status:number, roomUserInfoList: Array <MsRoomUserInfo>, roomInfo:MsRoomInfo){
    ......
}
```

status 说明：200 成功 其他值失败

MsRoomUserInfo 说明

| 属性        | 类型   | 描述     | 示例值 |
| ----------- | ------ | -------- | ------ |
| userID      | number | 用户ID   | 32322  |
| userProfile | string | 玩家简介 | ""     |

MsRoomInfo 说明

| 属性         | 类型   | 描述               | 示例值 |
| ------------ | ------ | ------------------ | ------ |
| roomID       | string | 房间号             | 238211 |
| roomProperty | string | 房间属性           | ""     |
| owner        | number | 房间创建者的用户ID | 0      |

> **注意** ：
>
> 1、随机匹配不能匹配到客户端主动创建的房间里，即通过`createRoom()`（见联网扩展）创建的房间。
>
> 2、加入房间后，服务器会指定一个房主，当房主离开房间后，服务器会随机指定下一个房主，并通过`leaveRoomNotify` 通知房间内其他成员。

在egret 项目中使用示例如下：

```typescript
private loginResponse(rsp:MsLoginRsp){
    if(rsp.status === 200){
        console.log("登录Matchvs联网SDK成功");
        this.response.joinRoomNotify = this.joinRoomNotify.bind(this);
        this.response.joinRoomResponse = this.joinRoomResponse.bind(this);
        this.engine.joinRandomRoom(3,"hello matchvs");
    }else{
        console.log("登录Matchvs联网失败",userInfo.status );
    }
}

private joinRoomResponse(status:number, roomUserInfoList:Array<MsRoomUserInfo>, roomInfo:MsRoomInfo){
    if(status === 200){
        console.log("我自己进入房间成功");
    }
}

private joinRoomNotify(roomUserInfo:MsRoomUserInfo){
    console.log("有其他人进入房间");
}
```

Matchvs 提供四种加入房间的操作，他们加入房间的操作都是相互平行的，随机匹配只能和随机配的玩家一起，属性匹配只能和属性匹配的玩家一起，但是加入指定房间 与 创建房间是一起的：

- 随机匹配( joinRandRoom ): 由Matchvs 自动匹配用户。
- 属性匹配( joinRoomWithProperties ): 由开发设定属性相同的用户匹配到一起
- 加入指定房间 ( joinRoom )：加入指定房间，这个是与 createRoom配合使用，只能加入到 createRoom 创建的房间。
- 创建房间( createRoom )：开发者自己创建房间。其他玩家要加入必须使用 joinRoom 加入。





