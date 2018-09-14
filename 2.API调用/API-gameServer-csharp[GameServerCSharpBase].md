## 创建房间

房间被创建时，gameServer 会触发`onCreateRoom`消息，如有"房间创建“的相关逻辑应写在该方法里。

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



## 删除房间

房间被销毁时，gameServer 会触发`OnHotelCloseConnect`，开发者可以将“销毁房间的逻辑”写到该方法里。

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



## 加入房间

玩家进入房间时，gameServer 会触发`onJoinRoom`，开发者可以将“玩家加入房间的逻辑”写到该方法里。

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

客户端调用JoinRoom进入房间，Matchvs会先通知gameServer有用户要加入房间，然后再向客户端发送JoinRoomResponse，所以当gameServer收到OnJoinRoom通知时，用户可能还没有真正进入房间（没有收到JoinRoomResponse），如果这时gameServer向该用户发送消息将会失败。所以我们增加了一个状态通知接口`OnHotelCheckin`，用于通知gameServer用户已经真正进入了房间，这时向用户发送消息是可靠的。

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

Matchvs提供了在gameServer里查询房间详情的接口，查询结果在`onRoomDetail`中返回。

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