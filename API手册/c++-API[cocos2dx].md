## init

```
int32_t init(MatchVSResponse *pMatchVSResponse, const MsString &channel, const MsString &platform, uint32_t gameID);
```

### 参数

| 参数             | 类型            | 描述                 | 示例值    |
| ---------------- | --------------- | -------------------- | --------- |
| pMatchVSResponse | MatchVSResponse | MatchVSResponse派生类指针 |           |
| channel          | const MsString  | 渠道，固定值              | "Matchvs" |
| platform         | const MsString  | MatchVSResponse派生类指针 | "alpha"   |
| gameID           | uint32_t        | 游戏ID                    | 2001003   |

### 说明

1.在连接至 Matchvs 前须对SDK进行初始化操作。此时选择连接测试环境还是正式环境。

2.如果游戏属于调试阶段则连接至测试环境，游戏调试完成后即可发布到正式环境运行。

**注意** 发布之前须到官网控制台申请“发布上线” ，申请通过后在接口传”release“才会生效，否则将不能使用release环境。

### 错误码

|错误码| 含义                                     |
| ---- | ---------------------------------------- |
| -1   | 失败                                     |
| -2   | channel 非法，请检查是否正确填写为"Matchvs"|
| -3   | platform 非法，请检查是否正确填写为"alpha" 或 "release" |

## 

## uninit

```
int32_t uninit();
```

### 说明

SDK反初始化工作

### 错误码

|错误码| 含义 |
| ---- | ---- |
| -1   | 失败 |

## registerUser  	

```
int32_t registerUser();
```

#### 返回值

| 错误码 | 含义     |
| ------ | -------- |
| 0      | 成功     |
| -1     | 失败     |
| -2     | 未初始化 |



### 说明

注册用户信息，用以获取一个合法的userID，通过此ID可以连接至Matchvs服务器。一个用户只需注册一次，不必重复注册

## registerUserResponse

```
virtual int registerUserResponse(const MsUserInfo &userInfo) = 0;
```

### 参数userInfo的属性

| 属性   | 类型    | 描述                               | 示例值                                               |
| ------ | ------- | ---------------------------------- | ---------------------------------------------------- |
| userID | int     | 用户ID                             | 123456                                               |
| token  | MsString| 用户Token                          | "XGBIULHHBBSUDHDMSGTUGLOXTAIPICMT"                   |
| name   | MsString| 用户名称                           | "张三"                                               |
| avatar | MsString| 头像                               | "http://pic.vszone.cn/upload/head/1416997330299.jpg" |
| status | int32_t | 接口回调状态码,0表示成功，其他失败 | 0 成功                                               |

### 说明

注册用户的回调函数

## login

```
int32_t login(int userid, const MsString &token, int gameid, int gameversion, const MsString &appkey, const MsString &secretkey, const MsString &deviceid, int gatewayid);
```

### 参数

| 参数        | 类型           | 描述                       | 示例值 |
| ----------- | -------------- | -------------------------- | ------ |
| userid      | int            | 用户ID，调用注册接口后获取             | 123456 |
| token       | const MsString | 用户token，调用注册接口后获取          | ""     |
| gameid      | int            | 游戏ID，来自Matchvs控制台游戏信息      | 210329 |
| gameversion | int            | 游戏版本，自定义，用于隔离匹配空间     | 1      |
| appkey      | const MsString | 游戏App key，来自Matchvs控制台游戏信息 | ""     |
| secretkey   | const MsString | 用户token，调用注册接口后获取          | ""     |
| deviceid    | const MsString | 设备ID，用于多端登录检测，请保证是唯一ID     | ""     |
| gatewayid   | int            | 服务器节点ID，默认为0                  | 0      |

### 错误码

|错误码| 含义           |
| ---- | -------------- |
| -1   | 失败           |
| -2   | 未初始化，请先调用初始化接口 |
| -3   | 正在登录           |
| -4   | 已经登录，请勿重复登录    |

## loginResponse

```
virtual int loginResponse(const MsLoginRsp &tRsp) = 0;
```

### 参数MsLoginRsp的属性

| 属性   | 类型   | 描述                            | 示例值 |
| ------ | ------ | ------------------------------- | ------ |
| status | int    | 状态返回 <br>200 成功<br>402 应用校验失败，确认是否在未上线时用了release环境，并检查gameID、appkey 和 secret<br>403 检测到该账号已在其他设备登录<br>404 无效用户 <br>500 服务器内部错误 | 200    |
| roomID | uint64_t | 房间号                          | 210039 |

### 说明

- 1.如果用户加入房间之后掉线，再重新登录进来，则roomID为之前加入的房间的房间号。

- 2.登录Matchvs服务端，与Matchvs建立连接。

- 3.服务端会校验游戏信息是否合法，保证连接的安全性。

- 4.如果一个账号在两台设备上登录，则后登录的设备会连接失败。



## logut

```
int32_t logout();
```

### 说明

退出登录，断开与Matchvs的连接。

### 错误码

| 错误码 | 含义 |
| ------ | ---- |
| 0      | 成功 |
| -1     | 失败 |

## logoutResponse

```
virtual int logoutResponse(const MsLogoutRsp &tRsp) = 0;
```

### 参数MsLogoutRsp的属性

| 参数   | 类型 | 描述   |示例值|
| ------ | ---- | ------ | ---- |
| status | int  | 返回值 | 200  |


## createRoom

``` 
int32_t createRoom(const MsCreateRoomInfo &roomInfo, const MsString &userProfile);
```

### 参数

| 参数        | 类型             | 描述                       | 示例值 |
| ----------- | ---------------- | -------------------------- | ------ |
| roomInfo    | MsCreateRoomInfo | name:房间名;<br/>maxPlayer:最大玩家数;<br/>mode:模式;<br/>canWatch:是否可以观战;<br/>visibility:是否可见;<br/>roomProperty:房间属性 | name:'roomName';<br/>maxPlayer:3;<br/>mode:0;<br/>canWatch:1可观战，2不可观战;<br/>visibility:1是可见，0是不可见;<br />roomProperty:'roomProperty' |
| userProfile | const MsString   | 玩家简介   | ""     |

### 说明

- 开发者可以在客户端主动创建房间，创建成功后玩家会被自动加入该房间，创建房间者即为房主，如果房主离开房间则matchvs会自动转移房主并通知房间内所有成员，开发者通过设置CreateRoomInfo创建不同类型的房间

## createRoomResponse

``` 
virtual void createRoomResponse(const MsCreateRoomRsp &rsp) = 0;
```

### 参数MsCreateRoomRsp返回值

| 参数        | 类型           | 描述                       | 示例值 |
| ----------- | -------------- | -------------------------- | ------ |
| status      | int32_t  | 进房状态| 200    |
| roomID      | uint64_t | 房间号  | 15452154312454 |
| owner       | uint32_t | 房主ID  | 34556775435 |



## getRoomList

```
int32_t getRoomList(const MsRoomFilter &filter);
```

### 参数 

| 参数        | 类型           | 描述                       | 示例值 |
| ----------- | -------------- | -------------------------- | ------ |
| filter      | MsRoomFilter   | maxPlayer:最大玩家数;<br />mode:模式;<br />canWatch:是否可以观战<br />roomProperty:房间属性 | maxPlayer:3;<br />mode:0;<br />canWatch:1可观战，2不可观战;<br />roomProperty:'roomProperty' |

## getRoomListResponse

``` 
virtual void getRoomListResponse(const MsRoomListRsp &rsp) = 0;
```

### 参数MsRoomListRsp返回值

| 参数      | 类型                | 描述                            | 示例值 |
| --------- | ------------------- | ------------------------------- | ------ |
| status    | int32_t              | 状态返回，200表示成功<br>500 服务器内部错误 | 200    |
| roomInfos | std::vector<MsRoomInfoEx> |                                 |        |

### 参数MsRoomInfoEx返回值

| 参数         | 类型           | 描述                       | 示例值 |
| ------------ | -------------- | -------------------------- | ------ |
| status       | int            | 返回值   | 200            |
| roomID       | uint64_t       | 房间ID   | "156231454561" |
| roomName     | MsString       | 房间名称 | "matchvs"      |
| maxPlayer    | uint32_t       | 最大人数 | 3              |
| mode         | int32_t        | 模式     | 0              |
| canWatch     | int32_t        | 可否观战 | 1可观战，2不可观战 |
| roomProperty | MsString       | 房间属性 | ""             |

### 说明

- 开发者通过该接口可以获取所有客户端主动创建的房间列表。不同模式(mode)下的玩家不会被匹配到一起，开发者可以利用mode区分竞技模式，普通模式等游戏模式。开发者可以通过设置RoomFilter来对获取的房间进行过滤

## getRoomListEx

```
int32_t getRoomListEx(const MsRoomFilterEx &filter);
```

### MsRoomFilterEx参数 

| 参数        | 类型           | 描述                       | 示例值 |
| ----------- | -------------- | -------------------------- | ------ |
| filter      | MsRoomFilter   | 获取房间列表过滤条件       |        |
| maxPlayer   | uint32_t       | 最大玩家数                 | 10     |
| mode  	  | int32_t        | 模式                       | 0      |
| canWatch    | int32_t        | 是否可观战                 | 0是全部，1是可观战，2是不可观战|
| roomProperty| MsString       | 房间属性                   | ""     |
| full        | int32_t        | 0-全部 1-满 2-未满         | 0      |
| state       | RoomState      | 枚举值                     | ""     |
| sort        | RoomListSort   | 枚举值                     | ""     |
| order       | SortOrder      | 枚举值                     | ""     |
| pageNo      | int32_t        | 页码，0为第一页            | ""     |
| pageSize    | int32_t        | 每一页的数量应该大于 0     | ""     |

### 说明

获取房间列表拓展接口

## getRoomListExResponse

```
virtual void getRoomListExResponse(const MsGetRoomListExRsp &rsp) = 0;
```


### MsGetRoomListExRsp参数

| 参数        | 类型           | 描述                       | 示例值 |
| ----------- | -------------- | -------------------------- | ------ |
| status      | int32_t        | 返回值  					| 200    |
| total       | int32_t        | total  					| 5      |
| roomAttrs   | std::vector<MsRoomAttribute>        | 房间列表扩展  | 5  |

### MsRoomAttribute参数

| 参数        | 类型           | 描述                   | 示例值 |
| ----------- | -----------| -------------------------- | ------ |
| roomId      | uint64_t   | 返回值  					| 200    |
| roomName    | MsString   | total  					| 5      |
| maxPlayer   | uint32_t   | 房间最大人数 			    | 3      |
| gamePlayer  | uint32_t   | 游戏人数     				| 2      |
| watchPlayer | uint32_t   | 观战人数  					| 1      |
| mode        | int32_t    | 模式  						| 0      |
| canWatch    | int32_t    | 是否可观战  				| 2      |
| roomProperty| MsString   | 房间属性  					| "roomProperty"|
| owner   	  | uint32_t   | 房主  						| 5      |
| state       | RoomState  | 房间状态  					| 5      |
| createTime  | uint64_t   | 房间创建事件  				| 5      |

### 说明

- 是接口 getRoomListEx 的回调，参数total  与 roomAttrs 列表length 可能会不同，但是 total 会 >= roomAttrs 列表的 length。

## getRoomDetail

```
int32_t getRoomDetail(uint64_t roomId);
```

### 参数 

| 参数        | 类型           | 描述           | 示例值 |
| ----------- | -------------- | -------------- | ------ |
| roomId      | uint64_t       | 房间ID         | 237890 |

### 返回值

| 错误码 | 含义         |
| ------ | ------------ |
| 0      | 调用成功     |
| -1     | 调用失败     |
| -2     | 未初始化     |
| -4     | 未登陆       |
| -7     | 正在加入房间 |
| -3     | 正在初始化   |

### 说明

- 获取房间详细信息，可获取的时候 createRoom 接口创建的房间。



## getRoomDetailResponse

```
virtual void getRoomDetailResponse(const MsGetRoomDetailRsp &rsp) = 0;
```

### MsGetRoomDetailRsp参数

| 参数        | 类型           | 描述                       | 示例值 |
| ----------- | -------------- | -------------------------- | ------ |
| status      | int32_t        | 返回值                     | 200    |
| state       | uint32_t       |房间状态 0-全部 1-开放 2-关闭|   0   |
| maxPlayer   | uint32_t       | 最大人数                   |    10  |
| mode        | int32_t        | 游戏模式                   |    0   |
| canWatch    | int32_t        | 是否可以观战               | 1 可观战，2不可观战  |
| roomProperty| MsString       | 房间属性                   |   ""   |
| owner       | uint32_t       | 房主                       | 655522 |
| createFlag  | uint32_t       |  房间创建途径              | 0未知，1系统创建，2玩家创建     |
| userInfos   | std::list<MsRoomUserInfo>  | 房间用户信息列表 |      |

### MsRoomUserInfo参数

| 参数        | 类型           | 描述                       | 示例值 |
| ----------- | -------------- | -------------------------- | ------ |
| userID      | uint32_t       |  用户ID                    |432444  |
| userProfile | MsString       |  用户简介                  | ""     |



### 说明

根据roomID获取房间详细信息


## joinRandomRoom

```
int32_t joinRandomRoom(uint32_t iMaxPlayer, const MsString &userProfile);
```

### 参数

| 参数           | 类型           | 描述     | 示例值|
| -------------- | -------------- | -------- | ----- |
| iMaxPlayer     | uint32_t       | 房间内最大玩家数 | 3    |
| userProfile    | const MsString | 玩家简介 | ""    |

### 说明

当房间里人数等于IMaxPlayer时，房间人满。系统会将玩家随机加入到人未满且没有JoinOver的房间。

### 错误码

| 错误码  | 含义                                       |
| ------- | ------------------------------------------ |
| -1      | 正在加入房间                               |
| -4      | 未login，请先调用login                     |
| -12     | 已经JoinRoom并JoinOver，不允许重复加入房间 |
| -20     | maxPlayer 超出范围 ，maxPlayer须≤20        |

## joinRoomWithProperties

```
int32_t joinRoomWithProperties(const MsMatchInfo &matchInfo, const MsString &userProfile);
```

### 参数

| 参数           | 类型           | 描述     | 示例值  |
| -------------- | -------------- | -------- | ------- |
| matchInfo      | MsMatchInfo    | 配置信息 |         |
| userProfile    | MsString       | 玩家简介 |         |

### MsMatchInfo 参数

| 参数           | 类型           | 描述     | 示例值  |
| -------------- | -------------- | -------- | ------- |
| maxPlayer      | uint32_t       | 最大人数 | 10      |
| mode           | int32_t        | 模式     | 1       |
| canWatch       | int32_t        | 是否可以观战 1-可以 2-不可以 | 1       |
| tags           | std::map<MsString, MsString> |  匹配标签   |     |

### 说明

通过指定匹配属性匹配指定玩家

## joinRoom

```
int32_t joinRoom(uint64_t roomID, const MsString &userProfile);
```

### 参数

| 参数           | 类型           | 描述     | 示例值  |
| -------------- | -------------- | -------- | ------- |
| roomID         | uint64_t       | 房间ID   | 258744  |
| userProfile    | MsString       | 房间信息 |         |


### 说明

通过输入房间号码来加入用户自定义房间。

### 错误码

| 错误码  | 含义                            |
| ---- | ----------------------------- |
| -1   | 正在加入房间                        |
| -4   | 未login，请先调用login              |
| -12  | 已经JoinRoom并JoinOver，不允许重复加入房间 |
| -20  | maxPlayer 超出范围 ，maxPlayer须≤20 |


## roomJoinResponse

```
virtual int roomJoinResponse(const MsRoomJoinRsp *tRsp) = 0;
```

### MsRoomJoinRsp 参数列表

| 参数         | 类型           | 描述       | 示例值 |
| ------------ | -------------- | ---------- | ------ |
| status       | int            | 返回值     | 200    |
| state        | uint32_t       | 房间状态,状态值 0-未知， 1-打开， 2- 关闭 |     |
| userInfo_v   | std::vector<MsRoomUserInfo> | 房间已有用户列表 |        |
| roomInfo     | MsRoomInfo     | 房间信息   |        |

### MsRoomUserInfo 参数列表

| 参数         | 类型           | 描述       | 示例值 |
| ------------ | -------------- | ---------- | ------ |
| userID       | uint32_t       | 用户ID     | 321    |
| userProfile  | MsString       | 用户简介   | ""     |

### MsRoomInfo 参数列表

| 参数         | 类型           | 描述       | 示例值 |
| ------------ | -------------- | ---------- | ------ |
| roomID       | uint64_t       | 房间号     | 200289 |
| roomProperty | const MsString | 房间属性   | ""     |
| owner        | uint32_t       | 房主ID     | 200289234 |

### 说明
- 如果本房间是某个玩家调用joinRandomRoom随机加入房间时创建的，那么roomInfo中的owner为服务器随机指定的房主ID。在调用engine.createRoom主动创建房间时owner为创建房间者（即房主）的ID。以上两种情况下，如果房主离开房间，服务器均会指定下一个房主，并通过`leaveRoomNotify`通知房间其他成员。
- roomUserInfoList用户信息列表是本玩家加入房间前的玩家信息列表，不包含本玩家。
- roomUserInfoList中的玩家的userProfile的值来自于其他客户端调用joinRandomRoom时传递的userProfile的值。


## roomPeerJoinNotify

```
virtual int roomPeerJoinNotify(const MsRoomUserInfo &objPeerJoin) = 0;
```

### MsRoomUserInfo的参数列表

| 参数        | 类型           | 描述   | 示例值  |
| ----------- | -------------- | ------ | ------- |
| userID      | uint32_t       | 用户ID | 321     |
| userProfile | MsString       | 用户简介 | ""    |

### 说明

加入房间广播通知，当有其他玩家加入房间时客户端会收到该通知。



## joinOver

```
int32_t joinOver(const MsString &cpProto);
```

### 参数

| 参数    | 类型           | 描述     |示例值|
| ------- | -------------- | -------- | ---- |
| cpProto | const MsString | 负载信息 |      |


### 说明

客户端调用该接口通知服务端：房间人数已够，不要再向房间加人。




## roomJoinOverResponse

```
virtual int roomJoinOverResponse(const MsRoomJoinOverRsp *tRsp) = 0;
```

### 返回值

| 参数    | 类型           | 描述   | 示例值 |
| ------- | -------------- | ------ | ------ |
| status  | int            | 返回值 | 200    |
| cpProto | const MsString |负载数据| ""     |


## joinOverNotify

```
virtual void joinOverNotify(const MsJoinOverNotifyInfo &notifyInfo) = 0;
```

### MsJoinOverNotifyInfo参数列表

| 参数   | 类型   | 描述   | 示例值  |
| ------ | ------ | ------ | ------- |
| roomId | uint64_t  | 房间ID | 321233  |
| srcUserID | uint32_t  | 发送通知joinOver通知用户ID | 12435543  |
| cpProto | MsString  | 负载数据 | "" |


## leaveRoom

```
int32_t leaveRoom(const MsString &cpProto);
```

### 参数

| 参数    | 类型           | 最大长度 | 描述   | 示例值  |
| ------- | -------------- | -------- | ------ | ------- |
| cpProto | const MsString |          |负载信息|         |

### 说明

退出房间，玩家退出房间后将不能再发送数据，也不能再接收到其他玩家发的数据。

## roomLeaveResponse

```
virtual int roomLeaveResponse(const MsRoomLeaveRsp &tRsp) = 0;
```

### MsRoomLeaveRsp 参数列表

| 参数    | 类型           | 描述    | 示例值 |
| ------- | -------------- | ------- | ------ |
| status  | int            | 返回值  | 200    |
| roomID  | uint64_t       | 房间号  | 200289 |
| userID  | int            | 用户ID  | 321    |
| cpProto | MsString       | 负载数据| ""     |

## roomPeerLeaveNotify

```
virtual int roomPeerLeaveNotify(const MsLeaveRoomNotifyInfo &notifyInfo) = 0;
```

### MsLeaveRoomNotifyInfo 参数列表

| 参数    | 类型           | 最大长度 | 描述   | 示例值  |
| ------- | -------------- | -------- | ------ | ------- |
| owner   | uint32_t       | 返回值   | 200    |
| roomId  | uint64_t       | 房间号   | 200289 |
| userID  | uint32_t       | 用户ID   | 321    |
| cpProto | MsString       | 负载数据 | ""     |



## kickPlayer

```
int32_t kickPlayer(uint32_t userID, const MsString &cpProto);
```

### 参数

| 参数    | 类型           | 描述    | 示例值  |
| ------- | -------------- | ------- | ------- |
| userID  | uint32_t       | 用户ID  | 2009877 |
| cpProto | const MsString | 负载信息| ""      |

### 说明

房主将指定ID的玩家踢出房间

### 错误码
|错误码| 含义     |
| ---- | -------- |
| -6   | 登录状态无法踢人 |

## kickPlayerRsp

```
virtual void kickPlayerRsp(const MsKickPlayerRsp &rsp) = 0;
```

### 返回值

| 参数      | 类型             | 描述   | 示例值    |
| -------   | --------------   | -------| --------- |
| status    | int32_t          | 返回值 | 200       |
| owner     | uint32_t         | 房主ID | 43564     |
| userID    | uint32_t         | 被踢玩家ID | 43566  |

## kickPlayerNotify

```
virtual void kickPlayerNotify(const MsKickPlayerNotify &notify) = 0;
```

### 返回值

| 参数      | 类型             | 描述   | 示例值    |
| -------   | --------------   | -------| --------- |
| srcUserID | uint32_t         | 返回值 | 200       |
| owner     | uint32_t         | 房主ID | 43564     |
| userID    | uint32_t         | 被踢玩家ID | 43566  |
| cpProto   | MsString         | 负载消息 | ""  |

## sendEvent

```
int32_t sendEvent(const MsString &cpProto, int &seq);
```

| 参数    | 类型           | 最大长度 | 描述     | 示例值  |
| ------- | -------------- | -------- | -------- | ------- |
| cpProto | const MsString |          | 消息内容 | "Hello" |
| seq     | int			   |          | 消息唯一序列 | 001 |


### 说明

简易版发送消息


## sendEvent

```
int32_t sendEvent(int type, const MsString &cpProto, int targetType, int targetSize, const uint32_t *targetUserId, int &seq);
```

### 参数

| 参数          | 类型           | 描述                                     | 示例值   |
| ------------- | -------------- | ---------------------------------------- | -------- |
| type          | int            | 消息类型。0表示转发给其他玩家；1表示转发给game server；2表示转发给其他玩家及game server | 0    |
| msg           | const MsString | 消息内容                                 | ""       |
| targetType    | int            | 目标类型。0表示发送目标为pTargetUserId；1表示发送目标为除pTargetUserId以外的房间其他人 | 0    |
| targetSize    | int            | 目标个数                                 | ""       |
| *targetUserId | const int      | 目标列表                                 |          |
| seq 			| int 			 | 序列器                                   | 001   |

### 说明

发送消息，可以指定发送对象，比如只给玩家A发送消息。

优先级是相对的，SDK收到多个消息，会优先将优先级低的消息传给上层应用。如果所有消息优先级一样，则SDK会根据接收顺序依次将消息传给上层应用。

可以发送二进制数据，开发者可以将数据用json、pb等工具先进行序列化，然后将序列化后的数据通过SendEvent的一系列接口发送。

### 错误码

|错误码| 含义                            |
| ---- | ------------------------------- |
| -1   | 失败                            |
| -3   | priority 非法，priority须在0-3范围内  |
| -4   | type 非法                       |
| -5   | targetType 非法                 |
| -6   | targetnum 非法，targetnum不可以超过20 |

## sendEventRsp

```
virtual int sendEventRsp(const MsSendEventRsp &tRsp) = 0;
```

### MsSendEventRsp参数列表

| 参数   | 类型     | 描述    |示例值|
| ------ | -------- | ------- | ---- |
| status | int32_t  | 返回值  | 200  |
| seq    | int32_t  |序列器   | 001  |

### 说明
- 客户端调用engine.sendEvent发送消息之后，SDK异步调用reponse.sendEventResponse方法告诉客户端消息是否发送成功。

## sendEventNotify

```
virtual void sendEventNotify(const MsSendEventNotify &tRsp) = 0;
```

### MsSendEventRsp参数列表

| 参数      | 类型   | 描述                                  | 示例值  |
| --------- | ------ | ------------------------------------- | ------- |
| srcUserID | uint32_t | 推送方用户ID，表示是谁发的消息        | 321     |
| cpProto   | MsString | 消息内容，对应[sendEvent](#sendEvent)中的msg参数 | "hello" |

### 说明

数据发送通知，当房间内其他玩家发送数据时，客户端会接收到该通知。



## gameServerNotify

```
virtual void gameServerNotify(const MsGameServerNotifyInfo &info) = 0;
```

### MsGameServerNotifyInfo参数列表

| 参数      | 类型   | 描述                                  | 示例值  |
| --------- | ------ | ------------------------------------- | ------- |
| srcUserID | uint32_t | 推送方用户ID，表示是谁发的消息      | 321     |
| cpProto   | MsString | 消息内容，对应[sendEvent](#sendEvent)中的msg参数 | "hello" |

#### 说明

- 开发者有使用 gameServer 的时候，如果有gameServer发送消息到客户端就会收到这个回调，回调的 srcUserID 是固定为0。




## subscribeEventGroup

```
int32_t subscribeEventGroup(const std::vector<MsString> &subGroups, const std::vector<MsString> &unsubGroups);
```

### 参数

| 参数        | 类型     | 描述   | 示例值  |
| ----------- | -------- | ------ | ------- |
| subGroups   |std::vector<MsString> | 订阅分组     | ""   |
| unsubGroups |std::vector<MsString> | 取消订阅分组 | ""   |

### 说明

订阅分组与取消订阅分组

### 错误码

|错误码| 含义        |
| ---- | ----------- |
| -4   | 初始化状态无法订阅     |
| -20  | 订阅和取消订阅的组为空 |

## subscribeEventGroupResponse

```
virtual void subscribeEventGroupResponse(const MsSubscribeEventGroupRsp &tRsp) = 0;
```

### MsSubscribeEventGroupRsp参数列表

| 参数      | 类型     | 描述   | 示例值  |
| --------- | -------- | ------ | ------- |
| status    | int      | 返回值 | 200     |
| subGroups | std::vector<MsString> | 订阅分组 |       |

## sendEventGroup

```
int32_t sendEventGroup(const MsString &cpProto, std::vector<MsString> &groups);
```

### 参数

| 参数     | 类型     | 描述   | 示例值      |
| -------- | -------- | ------ | ----------- |
| cpProto  | MsString | 消息   | ""          |
| groups   | std::vector<MsString> | 订阅组  | {"matchvs"} |



### 说明

发送订阅消息

### 错误码

| 错误码  | 含义            |
| ---- | ------------- |
| -1   | 未加入房间无法发送订阅消息 |
| -20  | 发送的内容为空或组大小为0 |

## sendEventGroupResponse

```
virtual void sendEventGroupResponse(const MsSendEventGroupRsp &tRsp) = 0;
```

### MsSendEventGroupRsp参数列表

| 参数   | 类型 | 描述    | 示例值  |
| ------ | ---- | --------| ------- |
| status | int32_t| 返回值  | 200     |
| dstNum | int32_t| 目标人数| 2       |

## sendEventGroupNotify

```
virtual void sendEventGroupNotify(const MsSendEventGroupNotify &notify) = 0;
```

### MsSendEventGroupNotify参数列表

| 参数      | 类型   | 描述     | 示例值      |
| --------- | ------ | -------- | ----------- |
| srcUserID |uint32_t| 源用户ID | 277773      |
| groups    | std::vector<MsString> | 组数组   |  |
| cpProto   | MsString | 消息内容 | "test"      |


## setFrameSync

```
int32_t setFrameSync(int32_t frameRate);
```

### 参数

| 参数     | 类型     | 描述   | 示例值      |
| -------- | -------- | ------ | ----------- |
| frameRate| int32_t  | 帧率   | 0是关闭，1~20是每秒的帧率 |

## setFrameSyncResponse

```
virtual void setFrameSyncResponse(const MsSetChannelFrameSyncRsp &rsp) = 0;
```

#### MsSetChannelFrameSyncRsp参数列表

| 参数    | 类型   | 描述           | 示例值 |
| ------- | ------ | -------------- | ------ |
| status  | int32_t| 状态：<br>200 成功<br>519 重复设置<br>500 帧率需被1000整除 | 200    |


## sendFrameEvent

```
int32_t sendFrameEvent(const MsString &cpProto);
```

### 参数

| 参数        | 类型           | 描述     | 示例值|
| ----------- | -------------- | -------- | ----- |
| cpProto 	  | const MsString | 发送帧同步的消息 | ""    |

## sendFrameEventResponse

```
virtual void sendFrameEventResponse(const MsSendFrameEventRsp &rsp) = 0;
```

### MsSendFrameEventRsp的参数列表

| 参数        | 类型           | 描述     | 示例值|
| ----------- | -------------- | -------- | ----- |
| status 	  | int32_t        | status:状态| 200    |

## frameUpdate

```
virtual void frameUpdate(const MsFrameData &data) = 0;
```

### MsFrameData的参数列表

| 参数        | 类型           | 描述     | 示例值|
| ----------- | -------------- | -------- | ----- |
| frameIndex 	  | int32_t    | 帧索引   | 2   |
| frameItems 	  | std::list<MsFrameItem> | 同步帧内的数据包数组 | ""    |
| frameWaitCount  | int32_t |同步帧内的数据包数组数量 | ""    |




## networkStateNotify

```
virtual int networkStateNotify(const MsNetworkStateNotify &notify) = 0;
```

### MsNetworkStateNotify参数列表

| 参数   | 类型      | 描述   | 示例值  |
| ------ | --------- | ------ | ------- |
| roomID | uint64_t  | 房间ID | 32123322  |
| userID | uint32_t  | 网络状况变化用户ID | ""  |
| state  | uint32_t  | 网络状态 | 32123322  |
| owner  | uint32_t  | 房主   | ""  |




## 通用错误码

| status | 含义        |
| ------ | --------- |
| 200    | 成功        |
| 500    | 服务器内部错误      |



