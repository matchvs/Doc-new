## 创建房间

房间被创建时，gameServer 会触发`onCreateRoom()`消息，如有"房间创建“的相关逻辑应写在该方法里。

```c#
public override IMessage OnCreateRoom(ByteString msg)
{
    Request request = new Request();
    ByteUtils.ByteStringToObject(request, msg);

    Reply reply = new Reply()
    {
        UserID = request.UserID,
        GameID = request.GameID,
        RoomID = request.RoomID,
        Errno = ErrorCode.Ok,
        ErrMsg = "OnCreateRoom success"
    };

    Logger.Info("OnCreateRoom start, userId={0}, gameId={1}, roomId={2}", request.UserID, request.GameID, request.RoomID);

    CreateExtInfo createEx = new CreateExtInfo();
    ByteUtils.ByteStringToObject(createEx, request.CpProto);
    Logger.Info("OnCreateRoom CreateExtInfo, userId={0}, roomId={1}, state={2}, CreateTime={3}", createEx.UserID, createEx.RoomID, createEx.State, createEx.CreateTime);

    return reply;
}
```

`Request`数据结构：

| 字段    | 类型       | 含义     |
| :------ | :--------- | :------- |
| UserID  | uint       | 用户ID   |
| GameID  | uint       | 游戏ID   |
| RoomID  | ulong      | 房间ID   |
| CpProto | ByteString | 附加消息 |

通过`Request`.`CpProto`反序列化获取创建房间扩展信息`CreateExtInfo`，数据结构如下：

| 字段         | 类型       | 含义                                         |
| :----------- | :--------- | :--------------------------------------- |
| UserID       | uint       | 创建者ID                                     |
| UserProfile  | ByteString | 创建者profile                                |
| RoomID       | ulong      | 房间ID                                       |
| State        | uint       | 房间状态：1开放、2关闭                       |
| MaxPlayer    | uint       | 最大人数                                     |
| Mode         | int        | 游戏模式                                     |
| CanWatch     | int        | 是否可观战                                   |
| RoomProperty | ByteString | 房间属性                                     |
| CreateFlag   | uint       | 房间创建途径：1 系统创建房间、2 玩家创建房间 |
| CreateTime   | ulong      | 创建时间                                     |

Matchvs 提供了在 gameServer 里主动创建房间的接口`CreateRoom`。调用该接口向 Matchvs 请求创建一个空房间。

```c#
public CreateRoomAck CreateRoom(CreateRoom request)
{
	return roomManger.CreateRoom(request);
}
```

`CreateRoom`数据结构：

| 字段     | 类型     | 含义                                                         |
| -------- | -------- | ------------------------------------------------------------ |
| SvcName  | string   | gameServer 服务名，该字段由内部自动设置，开发者无须设置      |
| PodName  | string   | gameServer 实例名，该字段由内部自动设置，开发者无须设置      |
| GameID   | uint     | 游戏ID                                                       |
| RoomInfo | RoomInfo | 房间信息                                                     |
| Ttl      | uint     | 空房间存活时长，单位秒。Mathcvs 从房间变成空时开始计时，超过 TTL 后销毁房间。在 TTL 内如有玩家加入房间则重置超时时长，而当房间再次变为空时重新开始计时。 |

`RoomInfo`数据结构：

| 字段         | 类型       | 含义                                                       |
| ------------ | ---------- | ---------------------------------------------------------- |
| RoomID       | ulong      | 房间ID，保留字段，目前未使用                               |
| RoomName     | string     | 房间名                                                     |
| MaxPlayer    | uint       | 房间最大人数                                               |
| Mode         | int        | 模式                                                       |
| CanWatch     | int        | 是否可以观战 1-可以 2-不可以                               |
| Visibility   | int        | 是否可见默认 0-不可见 1-可见                               |
| RoomProperty | ByteString | 房间属性                                                   |
| Owner        | uint       | 房主，保留字段，目前未使用                                 |
| State        | RoomState  | 房间状态，保留字段，目前 gameServer 创建的房间均为开放状态 |

`RoomState`枚举值：

| 字段   | 含义                 |
| ------ | -------------------- |
| Nil    | 保留                 |
| Open   | 开放，允许玩家加入   |
| Closed | 关闭，不允许玩家加入 |



## 设置空房间存活时长

gameServer 在创建一个房间之后，可以通过`TouchRoom()`重新设置该房间的存活时长:

```c#
public TouchRoomAck TouchRoom(TouchRoom request)
{
	return roomManger.TouchRoom(request);
}
```

| 字段    | 类型   | 含义                                                         |
| ------- | ------ | ------------------------------------------------------------ |
| SvcName | string | gameServer 服务名，该字段由内部自动设置，开发者无须设置      |
| PodName | string | gameServer 实例名，该字段由内部自动设置，开发者无须设置      |
| GameID  | uint   | 游戏ID                                                       |
| RoomID  | ulong  | 房间ID                                                       |
| Ttl     | uint   | 空房间存活时长，单位秒。Mathcvs 从房间变成空时开始计时，超过 TTL 后销毁房间。在 TTL 内如有玩家加入房间则重置超时时长，而当房间再次变为空时重新开始计时。 |



## 删除房间

房间被销毁时，gameServer 会触发`OnHotelCloseConnect()`，开发者可以将“销毁房间的逻辑”写到该方法里。

```c#
public override IMessage OnHotelCloseConnect(ByteString msg)
{
    CloseConnect close = new CloseConnect();
    ByteUtils.ByteStringToObject(close, msg);
    Logger.Info("CloseConnect, gameID:{0} roomID:{1}", close.GameID, close.RoomID);

    baseServer.DeleteStreamMap(close.RoomID);

    return new CloseConnectAck() { Status = (UInt32)ErrorCode.Ok };
}
```

`CloseConnect`数据结构：

| 字段   | 类型  | 含义   |
| :----- | :---- | :----- |
| GameID | uint  | 游戏ID |
| RoomID | ulong | 房间ID |

Matchvs 提供了在 gameServer 里主动删除房间的接口`DestroyRoom`。调用该接口向 Matchvs 请求删除一个房间。允许在房间内还有玩家时删除房间，这时会先踢出房间内的玩家再执行删除操作。

```c#
public DestroyRoomAck DestroyRoom(DestroyRoom request)
{
	return roomManger.DestroyRoom(request);
}
```

`DestroyRoom`数据结构：

| 字段    | 类型   | 含义                                                    |
| ------- | ------ | ------------------------------------------------------- |
| SvcName | string | gameServer 服务名，该字段由内部自动设置，开发者无须设置 |
| PodName | string | gameServer 实例名，该字段由内部自动设置，开发者无须设置 |
| GameID  | uint   | 游戏ID                                                  |
| RoomID  | ulong  | 房间ID                                                  |



## 加入房间

玩家进入房间时，gameServer 会触发`onJoinRoom()`，开发者可以将“玩家加入房间的逻辑”写到该方法里。

```c#
public override IMessage OnJoinRoom(ByteString msg)
{
    Request request = new Request();
    ByteUtils.ByteStringToObject(request, msg);

    Reply reply = new Reply()
    {
        UserID = request.UserID,
        GameID = request.GameID,
        RoomID = request.RoomID,
        Errno = ErrorCode.Ok,
        ErrMsg = "OnJoinRoom success"
    };

    Logger.Info("OnJoinRoom start, userId={0}, gameId={1}, roomId={2}", request.UserID, request.GameID, request.RoomID);

    JoinExtInfo joinEx = new JoinExtInfo();
    ByteUtils.ByteStringToObject(joinEx, request.CpProto);
    Logger.Info("OnJoinRoom JoinExtInfo, userId={0}, roomId={1}, JoinType ={2}", joinEx.UserID, joinEx.RoomID, joinEx.JoinType);

    return reply;
}
```

`Request`数据结构：

| 字段    | 类型       | 含义     |
| :------ | :--------- | :------- |
| UserID  | uint       | 用户ID   |
| GameID  | uint       | 游戏ID   |
| RoomID  | ulong      | 房间ID   |
| CpProto | ByteString | 附加消息 |

通过`Request`.`CpProto`反序列化获取加入房间扩展信息`JoinExtInfo`，数据结构如下：

| 字段        | 类型       | 含义                                                         |
| :---------- | :--------- | :----------------------------------------------------------- |
| UserID      | uint       | 加入房间的玩家ID                                             |
| UserProfile | ByteString | 加入房间的玩家profile                                        |
| RoomID      | ulong      | 房间ID                                                       |
| JoinType    | uint       | 加入类型：1指定roomID、2属性匹配、3随机匹配、4重新加入、5创建房间并随后自动加入房间 |



## 加入房间成功

客户端调用JoinRoom进入房间，Matchvs会先通知gameServer有用户要加入房间，然后再向客户端发送JoinRoomResponse，所以当gameServer收到OnJoinRoom通知时，用户可能还没有真正进入房间（没有收到JoinRoomResponse），如果这时gameServer向该用户发送消息将会失败。所以我们增加了一个状态通知接口`OnHotelCheckin()`，用于通知gameServer用户已经真正进入了房间，这时向用户发送消息是可靠的。

```c#
public override IMessage OnHotelCheckin(ByteString msg)
{
    PlayerCheckin checkin = new PlayerCheckin();
    ByteUtils.ByteStringToObject(checkin, msg);
    Logger.Info("PlayerCheckin, gameID:{0} roomID:{1} userID:{2}", checkin.GameID, checkin.RoomID, checkin.UserID);
    return new PlayerCheckinAck() { Status = (UInt32)ErrorCode.Ok };   
}
```

`PlayerCheckin`数据结构：

| 字段      | 类型   | 含义                                  |
| :-------- | :----- | :------------------------------------ |
| UserID    | uint   | 用户ID                                |
| GameID    | uint   | 游戏ID                                |
| RoomID    | ulong  | 房间ID                                |
| MaxPlayer | uint   | 最大人数                              |
| Checkins  | uint[] | 已经checkin的玩家ID                   |
| Players   | uint[] | 已进入房间（可能还未checkin）的玩家ID |



## 停止加入

当客户端调用停止加入接口`joinOver()`时，gameServer会触发`onJoinOver()`，开发者可以将收到客户端停止加入后的逻辑写到该方法里。

```c#
public override IMessage OnJoinOver(ByteString msg)
{
    Request request = new Request();
    ByteUtils.ByteStringToObject(request, msg);

    Reply reply = new Reply()
    {
        UserID = request.UserID,
        GameID = request.GameID,
        RoomID = request.RoomID,
        Errno = ErrorCode.Ok,
        ErrMsg = "OnJoinOver success"
    };

    Logger.Info("OnJoinOver start, userId={0}, gameId={1}, roomId={2}", request.UserID, request.GameID, request.RoomID);

    return reply;
}
```

`Request`数据结构：

| 字段    | 类型       | 含义     |
| :------ | :--------- | :------- |
| UserID  | uint       | 用户ID   |
| GameID  | uint       | 游戏ID   |
| RoomID  | ulong      | 房间ID   |
| CpProto | ByteString | 附加消息 |

Matchvs提供了在gameServer里主动发起JoinOver的接口。调用该接口向Matchvs通知不要再向房间加人。

```c#
public void PushJoinOver(UInt64 roomId, UInt32 gameId, UInt32 userId = 0, UInt32 version = 2)
{
    Logger.Info("PushJoinOver, roomID:{0}, gameID:{1}", roomId, gameId);

    JoinOverReq joinReq = new JoinOverReq()
    {
        RoomID = roomId,
        GameID = gameId
    };
    baseServer.PushToMvs(userId, version, (UInt32)MvsGsCmdID.MvsJoinOverReq, joinReq);
}
```

`JoinOverReq`数据结构：

| 字段   | 类型  | 含义   |
| :----- | :---- | :----- |
| GameID | uint  | 游戏ID |
| RoomID | ulong | 房间ID |



## 允许加入

通过重新打开房间可以取消joinOver状态。当客户端调用重新打开房间接口时，gameServer会触发`onJoinOpen()`，开发者可以将"收到客户端重新打开房间的逻辑"写到该方法里。

```c#
public override IMessage OnJoinOpen(ByteString msg)
{
    Request request = new Request();
    ByteUtils.ByteStringToObject(request, msg);

    Reply reply = new Reply()
    {
        UserID = request.UserID,
        GameID = request.GameID,
        RoomID = request.RoomID,
        Errno = ErrorCode.Ok,
        ErrMsg = "OnJoinOpen success"
    };

    Logger.Info("OnJoinOpen start, userId={0}, gameId={1}, roomId={2}", request.UserID, request.GameID, request.RoomID);

    return reply;
}
```

`Request`数据结构：

| 字段    | 类型       | 含义     |
| :------ | :--------- | :------- |
| UserID  | uint       | 用户ID   |
| GameID  | uint       | 游戏ID   |
| RoomID  | ulong      | 房间ID   |
| CpProto | ByteString | 附加消息 |

Matchvs提供了在gameServer里主动发起JoinOpen的接口。调用该接口向Matchvs通知允许向房间加人。

```c#
public void PushJoinOpen(UInt64 roomId, UInt32 gameId, UInt32 userId = 0, UInt32 version = 2)
{
    Logger.Info("PushJoinOpen, roomID:{0}, gameID:{1}", roomId, gameId);
    JoinOpenReq joinReq = new JoinOpenReq()
    {
        RoomID = roomId,
        GameID = gameId
    };
    baseServer.PushToMvs(userId, version, (UInt32)MvsGsCmdID.MvsJoinOpenReq, joinReq);
}
```

`JoinOpenReq`数据结构：

| 字段   | 类型  | 含义   |
| :----- | :---- | :----- |
| GameID | uint  | 游戏ID |
| RoomID | ulong | 房间ID |



## 接收数据

当客户端调用发送数据并指定发给gameServer时，gameServer会触发`OnHotelBroadCast()`，开发者可以将“收到客户端数据时的相关逻辑”写到该方法里。

```c#
public override IMessage OnHotelBroadCast(ByteString msg)
{
    HotelBroadcast broadcast = new HotelBroadcast();
    ByteUtils.ByteStringToObject(broadcast, msg);
    Logger.Info("HotelBroadcast start, userID:{0} gameID:{1} roomID:{2} cpProto:{3}", broadcast.UserID, broadcast.GameID, broadcast.RoomID, broadcast.CpProto.ToStringUtf8());

    HotelBroadcastAck broadcastAck = new HotelBroadcastAck() { UserID = broadcast.UserID, Status = (UInt32)ErrorCode.Ok };
    
 	Logger.Info("HotelBroadcast end, userID:{0} gameID:{1} roomID:{2} cpProto:{3}", broadcast.UserID, broadcast.GameID, broadcast.RoomID, broadcast.CpProto.ToStringUtf8());

    return broadcastAck;
}
```

`HotelBroadcast`数据结构：

| 字段    | 类型       | 含义           |
| :------ | :--------- | :------------- |
| UserID  | uint       | 用户ID         |
| GameID  | uint       | 游戏ID         |
| RoomID  | ulong      | 房间ID         |
| Flag    | uint       | 保留字段       |
| DstUids | uint[]     | 保留字段       |
| CpProto | ByteString | 自定义消息内容 |



## 发送数据

调用`push()`接口，可以在gameServer里给各个客户端异步发送数据。支持发给指定客户端。
消息长度不大于1024字节。

```c#
PushToHotelMsg msg = new PushToHotelMsg()
{
    PushType = PushMsgType.UserTypeAll,
    GameID = gameID,
    RoomID = roomID,
    CpProto = cpProto,
};

public void PushToHotel(UInt64 roomID, IMessage msg, UInt32 userId = 1, UInt32 version = 2)
{
    baseServer.PushToHotel(userId, version, roomID, (UInt32)HotelGsCmdID.HotelPushCmdid, msg);
}
```

`PushToHotelMsg`数据结构：

| 字段     | 类型        | 含义           |
| :------- | :---------- | :------------- |
| PushType | PushMsgType | 推送类型       |
| GameID   | uint        | 游戏ID         |
| RoomID   | ulong       | 房间ID         |
| DstUids  | uint[]      | 推送目标用户   |
| CpProto  | ByteString  | 自定义消息内容 |

`PushMsgType`枚举值：

| 字段             | 含义                                                    |
| ---------------- | ------------------------------------------------------- |
| NoneType         | 保留                                                    |
| UserTypeSpecific | 推送给`DstUids`列表中的指定用户                         |
| UserTypeExclude  | 推送给除`DstUids`列表中指定用户外的其他用户             |
| UserTypeAll      | 推送给房间内的所有用户，包括自己，`DstUids`列表参数无效 |



## 离开房间

当客户端调用离开房间时，gameServer会触发`onLeaveRoom()`，开发者可以将收到客户端离开房间时的相关逻辑写到该方法里。

```c#
public override IMessage OnLeaveRoom(ByteString msg)
{
    Request request = new Request();
    ByteUtils.ByteStringToObject(request, msg);

    Reply reply = new Reply()
    {
        UserID = request.UserID,
        GameID = request.GameID,
        RoomID = request.RoomID,
        Errno = ErrorCode.Ok,
        ErrMsg = "OnLeaveRoom success"
    };

    Logger.Info("OnLeaveRoom start, userId={0}, gameId={1}, roomId={2}", request.UserID, request.GameID, request.RoomID);

    return reply;
}
```

`Request`数据结构：

| 字段    | 类型       | 含义     |
| :------ | :--------- | :------- |
| UserID  | uint       | 用户ID   |
| GameID  | uint       | 游戏ID   |
| RoomID  | ulong      | 房间ID   |
| CpProto | ByteString | 附加消息 |



## 踢除房间成员

当客户端调用踢人时，gameServer会触发`onKickPlayer()`，开发者可以将"收到客户端踢人时的相关逻辑"写到该方法里。

```c#
public override IMessage OnKickPlayer(ByteString msg)
{
    Request request = new Request();
    ByteUtils.ByteStringToObject(request, msg);

    Reply reply = new Reply()
    {
        UserID = request.UserID,
        GameID = request.GameID,
        RoomID = request.RoomID,
        Errno = ErrorCode.Ok,
        ErrMsg = "OnKickPlayer success"
    };

    Logger.Info("OnKickPlayer start, userId={0}, gameId={1}, roomId={2}", request.UserID, request.GameID, request.RoomID);

    return reply;
}
```

`Request`数据结构：

| 字段    | 类型       | 含义     |
| :------ | :--------- | :------- |
| UserID  | uint       | 用户ID   |
| GameID  | uint       | 游戏ID   |
| RoomID  | ulong      | 房间ID   |
| CpProto | ByteString | 附加消息 |

Matchvs提供了在gameServer里踢除房间成员的接口。当发现有玩家恶意不准备等情况，可以调用该接口将该玩家踢出房间。

```c#
public void PushKickPlayer(UInt64 roomId, UInt32 destId, UInt32 userId = 0, UInt32 version = 2)
{
    Logger.Info("PushKickPlayer, roomID:{0}, destId:{2}", roomId, destId);

    KickPlayer kick = new KickPlayer()
    {
        RoomID = roomId,
        UserID = destId
    };
    baseServer.PushToMvs(userId, version, (UInt32)MvsGsCmdID.MvsKickPlayerReq, kick);
}
```

`KickPlayer`数据结构：

| 字段   | 类型  | 含义         |
| :----- | :---- | :----------- |
| UserID | uint  | 被剔的用户ID |
| RoomID | ulong | 房间ID       |



## 连接状态

当客户端和服务端的连接出现断开、重连等情况时，gameServer会触发连接状态的通知。

```c#
public override IMessage OnConnectStatus(ByteString msg)
{
    Request request = new Request();
    ByteUtils.ByteStringToObject(request, msg);

    Reply reply = new Reply()
    {
        UserID = request.UserID,
        GameID = request.GameID,
        RoomID = request.RoomID,
        Errno = ErrorCode.Ok,
        ErrMsg = "OnConnectStatus success"
    };
    string status = request.CpProto.ToStringUtf8();

    Logger.Info("OnConnectStatus start, userId={0}, gameId={1}, roomId={2}, status = {3}", request.UserID, request.GameID, request.RoomID, status);

    //1.掉线了  2.重连成功  3.重连失败
    if (status == "3")
    {
        Logger.Info("OnConnectStatus leaveroom, userId={0}, gameId={1}, roomId={2}, status = {3}", request.UserID, request.GameID, request.RoomID, status);
        return OnLeaveRoom(msg);
    }

    Logger.Info("OnConnectStatus end, userId={0}, gameId={1}, roomId={2}, status = {3}", request.UserID, request.GameID, request.RoomID, status);

    return reply;
}
```

`Request`数据结构：

| 字段    | 类型       | 含义     |
| :------ | :--------- | :------- |
| UserID  | uint       | 用户ID   |
| GameID  | uint       | 游戏ID   |
| RoomID  | ulong      | 房间ID   |
| CpProto | ByteString | 附加消息 |

`Request`.`CpProto`记录了玩家状态：

| 字段   | 类型   | 含义                        |
| ------ | ------ | --------------------------- |
| status | string | 1掉线，2重连成功，3重连失败 |



## 房间详情

Matchvs提供了在gameServer里查询房间详情的接口，查询结果在`onRoomDetail()`中返回。

```c#
public void PushGetRoomDetail(UInt64 roomId, UInt32 gameId, UInt32 userId = 0, UInt32 version = 2)
{
    Logger.Info("PushGetRoomDetail, roomID:{0}, gameId:{1}", roomId, gameId);
    GetRoomDetailReq roomDetail = new GetRoomDetailReq()
    {
        RoomID = roomId,
        GameID = gameId
    };
    baseServer.PushToMvs(userId, version, (UInt32)MvsGsCmdID.MvsGetRoomDetailReq, roomDetail);
}

public override void OnRoomDetail(ByteString msg)
{
    Request request = new Request();
    ByteUtils.ByteStringToObject(request, msg);

    RoomDetail roomDetail = new RoomDetail();
    ByteUtils.ByteStringToObject(roomDetail, request.CpProto);

    Logger.Info("OnRoomDetail, roomId={0}, state={1}, maxPlayer={2}, mode={3}, canWatch={4}, owner={5}",
                roomDetail.RoomID, roomDetail.State, roomDetail.MaxPlayer, roomDetail.Mode, roomDetail.CanWatch, roomDetail.Owner);
    foreach (PlayerInfo player in roomDetail.PlayerInfos)
    {
        Logger.Info("player userId={0}", player.UserID);
    }
}
```

`GetRoomDetailReq`数据结构：

| 字段   | 类型  | 含义   |
| :----- | :---- | :----- |
| GameID | uint  | 游戏ID |
| RoomID | ulong | 房间ID |

`OnRoomDetail`.`Request`数据结构：

| 字段    | 类型       | 含义     |
| :------ | :--------- | :------- |
| UserID  | uint       | 用户ID   |
| GameID  | uint       | 游戏ID   |
| RoomID  | ulong      | 房间ID   |
| CpProto | ByteString | 附加消息 |

通过`OnRoomDetail`.`Request`.`CpProto`反序列化获取创建房间详情信息`RoomDetail`，数据结构如下：

| 字段         | 类型         | 含义                                         |
| :----------- | :----------- | :------------------------------------------- |
| RoomID       | ulong        | 房间ID                                       |
| State        | uint         | 房间状态：1开放、2关闭                       |
| MaxPlayer    | uint         | 最大人数                                     |
| Mode         | int          | 游戏模式                                     |
| CanWatch     | int          | 是否可观战                                   |
| RoomProperty | ByteString   | 房间属性                                     |
| Owner        | uint         | 房主                                         |
| CreateFlag   | uint         | 房间创建途径：1 系统创建房间、2 玩家创建房间 |
| PlayerInfos  | PlayerInfo[] | 房间用户列表                                 |

`PlayerInfo`数据结构：

| 字段        | 类型       | 含义        |
| ----------- | ---------- | ----------- |
| UserID      | uint       | 用户ID      |
| UserProfile | ByteString | 玩家profile |



## 修改房间属性

当客户端修改房间属性时，gameServer会触发`onSetRoomProperty()`，开发者可以将"房间属性修改的相关逻辑"写到该方法里。

```c#
public override IMessage OnSetRoomProperty(ByteString msg)
{
    Request request = new Request();
    ByteUtils.ByteStringToObject(request, msg);

    Reply reply = new Reply()
    {
        UserID = request.UserID,
        GameID = request.GameID,
        RoomID = request.RoomID,
        Errno = ErrorCode.Ok,
        ErrMsg = "OnSetRoomProperty success"
    };
    string roomProperty = request.CpProto.ToStringUtf8();

    Logger.Info("OnSetRoomProperty start, userId={0}, gameId={1}, roomId={2}, roomProperty={3}", request.UserID, request.GameID, request.RoomID, roomProperty);

    return reply;
}
```

`Request`数据结构：

| 字段    | 类型       | 含义     |
| :------ | :--------- | :------- |
| UserID  | uint       | 用户ID   |
| GameID  | uint       | 游戏ID   |
| RoomID  | ulong      | 房间ID   |
| CpProto | ByteString | 附加消息 |

`Request`.`CpProto`记录了修改后的房间属性：

| 字段         | 类型       | 含义     |
| ------------ | ---------- | -------- |
| RoomProperty | ByteString | 房间属性 |

另外Matchvs提供了在gameServer里修改房间自定义属性的接口。

```c#
public void PushSetRoomProperty(UInt64 roomId, UInt32 gameId, ByteString roomProperty, UInt32 userId = 0, UInt32 version = 2)
{
    Logger.Info("PushSetRoomProperty, roomID:{0}, gameId:{1}", roomId, gameId);
    SetRoomPropertyReq roomPropertyReq = new SetRoomPropertyReq()
    {
        RoomID = roomId,
        GameID = gameId,
        RoomProperty = roomProperty
    };
    baseServer.PushToMvs(userId, version, (UInt32)MvsGsCmdID.MvsSetRoomPropertyReq, roomPropertyReq);
}
```

`SetRoomPropertyReq`数据结构：

| 字段         | 类型       | 含义     |
| :----------- | :--------- | :------- |
| GameID       | uint       | 游戏ID   |
| RoomID       | ulong      | 房间ID   |
| RoomProperty | ByteString | 房间属性 |



## 设置帧同步帧率

当客户端修改房间帧同步帧率时，gameServer 触发`OnHotelSetFrameSyncRate()`，开发者可以将"设置房间帧同步帧率“的相关逻辑写到该方法里。

```c#
public override void OnHotelSetFrameSyncRate(FrameSyncRate request)
{
	Logger.Info("OnHotelSetFrameSyncRate, gameID:{0} roomID:{1} frameRate:{2}", request.GameID, request.RoomID, request.FrameRate);
	return;
}
```

`FrameSyncRate`数据结构：

| 字段       | 类型  | 含义                                           |
| ---------- | ----- | ---------------------------------------------- |
| GameID     | uint  | 游戏ID                                         |
| RoomID     | ulong | 房间ID                                         |
| FrameRate  | uint  | 同步帧率                                       |
| FrameIndex | uint  | 初始帧编号                                     |
| Timestamp  | ulong | 系统时间戳                                     |
| EnableGS   | uint  | GameServer是否参与帧同步（0：不参与；1：参与） |

另外 Matchvs 提供了在 gameServer 里设置房间帧同步帧率的接口：

```c#
public void SetFrameSyncRate(UInt64 roomId, UInt32 gameId, UInt32 rate, UInt32 enableGS, UInt32 userId = 1, UInt32 version = 2)
{
    GSSetFrameSyncRate setFrameSyncRateReq = new GSSetFrameSyncRate()
    {
        GameID = gameId,
        RoomID = roomId,
        FrameRate = rate,
        Priority = 0,
        FrameIdx = 1,
        EnableGS = enableGS,
    };
    baseServer.PushToHotel(userId, version, roomId, (UInt32)HotelGsCmdID.GssetFrameSyncRateCmdid, setFrameSyncRateReq);
}
```

`GSSetFrameSyncRate`数据结构：

| 字段       | 类型  | 含义                                                  |
| ---------- | ----- | ----------------------------------------------------- |
| GameID     | uint  | 游戏ID                                                |
| RoomID     | ulong | 房间ID                                                |
| Priority   | uint  | 保留字段，固定设置为0                                 |
| FrameRate  | uint  | 同步帧率（0到20，且能被1000整除）                     |
| FrameIndex | uint  | 初始帧编号（frameRate > 0 时有效），FrameIndex 需 > 0 |
| EnableGS   | uint  | gameServer是否参与帧同步（0：不参与；1：参与）        |



## 接收帧同步消息

当房间启用了 gameServer 帧同步，同时客户端指定了将帧同步消息发往 gameServer 时，gameServer 即可接收到该房间的帧同步消息。

```c#
public override void OnHotelFrameUpdate(FrameData request)
{
    Logger.Info("OnHotelFrameUpdate, gameID:{0} roomID:{1} frameIndex:{2}", request.GameID, request.RoomID, request.FrameIndex);
    foreach (var item in request.FrameItems)
    {
        Logger.Info("SrcUser:{0}, cpProto:{1}", item.SrcUserID, item.CpProto.ToStringUtf8());
    }
    return;
}
```

`FrameData`数据结构：

| 字段           | 类型            | 含义                     |
| -------------- | --------------- | ------------------------ |
| GameID         | uint            | 游戏ID                   |
| RoomID         | ulong           | 房间ID                   |
| FrameIndex     | uint            | 帧编号                   |
| FrameItems     | List<FrameItem> | 帧数据                   |
| FrameWaitCount | int             | 同步帧内的数据包数组数量 |

`FrameItem`数据结构：

| 字段      | 类型       | 含义           |
| --------- | ---------- | -------------- |
| SrcUserID | uint       | 来源用户ID     |
| CpProto   | ByteString | 自定义消息内容 |
| Timestamp | ulong      | 帧数据时间戳   |



## 发送帧同步消息

当房间启用了 gameServer 帧同步时，gameServer 可以主动发送帧同步消息。

```c#
public void FrameBroadcast(UInt64 roomId, UInt32 gameId, ByteString cpProto, Int32 operation, UInt32 userId = 1, UInt32 version = 2)
{
    GSFrameBroadcast frameBroadcastReq = new GSFrameBroadcast()
    {
        GameID = gameId,
        RoomID = roomId,
        CpProto = cpProto,
        Priority = 0,
        Operation = operation,
    };
    baseServer.PushToHotel(userId, gameId, roomId, (UInt32)HotelGsCmdID.GsframeBroadcastCmdid, frameBroadcastReq);
}
```

`GSFrameBroadcast`数据结构：

| 字段      | 类型       | 含义                                            |
| --------- | ---------- | ----------------------------------------------- |
| GameID    | uint       | 游戏ID                                          |
| RoomID    | ulong      | 房间ID                                          |
| Priority  | uint       | 保留字段，固定设置为0                           |
| CpProto   | ByteString | 自定义消息内容                                  |
| Operation | int        | 0：只发客户端；1：只发GS；2：同时发送客户端和GS |

