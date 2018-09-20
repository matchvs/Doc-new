## 创建房间

房间被创建时，gameServer 会触发`onCreateRoom()`消息，如有"房间创建“的相关逻辑应写在该方法里。

```javascript
/**
 * 创建房间
 * @param {Object} request 
 * @param {number} request.gameID 游戏ID
 * @param {string} request.roomID 房间ID
 * @param {number} request.userID 用户ID
 * @param {Object} request.createExtInfo 房间创建扩展信息
 * @param {Number} request.createExtInfo.userID 创建者ID
 * @param {Uint8Array} request.createExtInfo.userProfile 创建者profile
 * @param {string} request.createExtInfo.roomID 房间ID
 * @param {number} request.createExtInfo.state 房间状态：1开放、2关闭
 * @param {number} request.createExtInfo.maxPlayer 最大人数
 * @param {number} request.createExtInfo.mode 游戏模式
 * @param {number} request.createExtInfo.canWatch 是否可观战
 * @param {Uint8Array} request.createExtInfo.roomProperty 房间属性
 * @param {number} request.createExtInfo.createFlag 房间创建途径：1系统创建房间、2玩家创建房间
 * @param {string} request.createExtInfo.createTime 创建时间
 * @memberof App
 */
onCreateRoom(request)
```

Matchvs 提供了在 gameServer 里主动创建房间的接口`createRoom()`。调用该接口向 Matchvs 请求创建一个空房间。

```js
 /**
 * 创建房间
 * @param {Object} msg 创建房间消息结构
 * @param {number} msg.gameID 游戏ID
 * @param {number} msg.ttl 空房间存活时长，单位秒
 * @param {Object} msg.roomInfo 房间信息
 * @param {string} msg.roomInfo.roomName 房间名称
 * @param {number} msg.roomInfo.maxPlayer 房间最大人数
 * @param {number} msg.roomInfo.mode 模式
 * @param {number} msg.roomInfo.canWatch 是否可观战
 * @param {number} msg.roomInfo.visibility 房间是否可见：0不可见，1可见
 * @param {string|Uint8Array} msg.roomInfo.roomProperty 房间属性
 * @param {function} callback 房间创建结果回调
 * @memberof Push
 */
 createRoom(msg, callback)
```

TTL（time to live）：Mathcvs 从房间变成空时开始计时，超过 TTL 后销毁房间。在 TTL 内如有玩家加入房间则重置超时时长，而当房间再次变为空时重新开始计时。

## 设置空房间存活时长

gameServer 在创建一个房间之后，可以通过`touchRoom()`重新设置该房间的存活时长。

```javascript
/**
* 修改房间存活时长
* @param {Object} msg 修改房间存活时长消息结构
* @param {number} msg.gameID 游戏ID
* @param {string} msg.roomID 房间ID
* @param {number} msg.ttl 空房间存活时长
* @param {function} callback 结果回调
* @memberof Push
*/
touchRoom(msg, callback)
```

## 删除房间

房间被销毁时，gameServer 会触发`onDeleteRoom()`，开发者可以将“销毁房间的逻辑”写到该方法里。

```javascript
/**
* 删除房间
* @param {Object} request
* @param {number} request.gameID 游戏ID 
* @param {string} request.roomID 房间ID
* @memberof App
*/
onDeleteRoom(request)
```

Matchvs 提供了在 gameServer 里主动删除房间的接口`destroyRoom()`。调用该接口向 Matchvs 请求删除一个房间。允许在房间内还有玩家时删除房间，这时会先踢出房间内的玩家再执行删除操作。

```javascript
/**
* 销毁房间
* @param {Object} msg 销毁房间消息结构
* @param {number} msg.gameID 游戏ID
* @param {string} msg.roomID 房间ID
* @param {function} callback 结果回调
* @memberof Push
*/
destroyRoom(msg, callback)
```

## 加入房间

玩家进入房间时，gameServer 会触发`onJoinRoom()`，开发者可以将“玩家加入房间的逻辑”写到该方法里。

```javascript
/**
 * 玩家加入房间
 * @param {Object} request 
 * @param {number} request.gameID 游戏ID
 * @param {string} request.roomID 房间ID
 * @param {number} request.userID 用户ID
 * @param {Object} request.joinExtInfo 房间加入扩展信息
 * @param {number} request.joinExtInfo.userID 加入房间的玩家ID
 * @param {Uint8Array} request.joinExtInfo.userProfile 加入房间的玩家profile
 * @param {string} request.joinExtInfo.roomID 要加入的房间ID
 * @param {number} request.joinExtInfo.joinType 加入类型：1指定roomID、2属性匹配、3随机匹配、4重新加入、5创建房间并随后自动加入房间
 * @memberof App
 */
onJoinRoom(request)
```

## 停止加入

客户端调用停止加入接口时，gameServer 会触发`onJoinOver()`，开发者可以将"收到客户端停止加入后的逻辑"写到该方法里。

```javascript
/**
* 房间停止加人
* @param {Object} request 
* @param {number} request.gameID 游戏ID 
* @param {string} request.roomID 房间ID
* @param {number} request.userID 用户ID
* @memberof App
*/
onJoinOver(request)
```

Matchvs提供了在 gameServer 里主动发起 JoinOver 的接口。调用该接口向 Matchvs 通知不要再向房间加人。

```javascript
/**
* 推送joinOver
* @param {Object} msg joinOver消息结构
* @param {number} msg.gameID 游戏ID 
* @param {string} msg.roomID 房间ID
* @memberof Push
*/
joinOver(msg)
```

## 允许加入

通过重新打开房间可以取消joinOver状态。客户端调用重新打开房间接口时，gameServer 会触发`onJoinOpen()`，开发者可以将"收到客户端重新打开房间的逻辑"写到该方法里。

```javascript
/**
* 房间允许加人
* @param {Object} request 
* @param {number} request.gameID 游戏ID
* @param {string} request.roomID 房间ID
* @param {number} request.userID 用户ID
* @memberof App
*/
onJoinOpen(request)
```

Matchvs 提供了在 gameServer 里主动发起JoinOpen的接口。调用该接口向Matchvs通知允许向房间加人。

```javascript
/**
* 推送joinOpen
* @param {Object} msg joinOpen消息结构
* @param {number} msg.gameID 游戏ID 
* @param {string} msg.roomID 房间ID
* @memberof Push
*/
joinOpen(msg)
```

## 接收数据

客户端调用发送数据并指定发给 gameServer 时，gameServer 会触发`onReceiveEvent()`，开发者可以将“收到客户端数据时的相关逻辑”写到该方法里。

```javascript
/**
* 自定义消息
* @param {Object} request 
* @param {number} request.userID 用户ID
* @param {number} request.gameID 游戏ID 
* @param {string} request.roomID 房间ID
* @param {number[]} request.destsList 目标玩家列表
* @param {Uint8Array} request.cpProto 自定义消息内容
* @memberof App
*/
onReceiveEvent(request)
```

## 发送数据

调用`pushEvent()`接口，可以在gameServer里给各个客户端异步发送数据。支持发给指定客户端。
消息长度不大于1024字节。

```javascript
/**
* 推送自定义消息
* @param {Object} msg 自定义消息结构
* @param {number} msg.gameID 游戏ID
* @param {string} msg.roomID 房间ID 
* @param {number} msg.pushType 推送类型，配合destsList使用
*      1：推送给列表中的指定用户，2：推送给除列表中指定用户外的其他用户，3：推送给房间内的所有用户
* @param {number[]} msg.destsList userID列表
* @param {string|Uint8Array} msg.content 消息内容
* @memberof Push
*/
pushEvent(msg)
```

## 离开房间

客户端调用离开房间时，gameServer 会触发`onLeaveRoom()`，开发者可以将"收到客户端离开房间时的相关逻辑"写到该方法里。

```javascript
/**
* 玩家离开房间
* @param {Object} request 
* @param {number} request.gameID 游戏ID 
* @param {string} request.roomID 房间ID
* @param {number} request.userID 用户ID
* @memberof App
*/
onLeaveRoom(request)
```

## 踢除房间成员

当客户端调用踢人时，gameServer 会触发`onKickPlayer()`，开发者可以将"收到客户端踢人时的相关逻辑"写到该方法里。

```javascript
/**
* 踢人
* @param {Object} request 
* @param {number} request.gameID 游戏ID
* @param {string} request.roomID 房间ID
* @param {number} request.userID 用户ID
* @memberof App
*/
onKickPlayer(request)
```

Matchvs 提供了在 gameServer 里主动踢除房间成员的接口。当发现有玩家恶意不准备等情况，可以调用该接口将该玩家踢出房间。

```javascript
/**
* 推送kickPlayer
* @param {Object} msg kickPlayer消息结构
* @param {string} msg.roomID 房间ID
* @param {number} msg.destID 被踢者
* @memberof Push
*/
kickPlayer(msg)
```

## 连接状态

当客户端和服务端的连接出现断开、重连等情况时，gameServer 会触发连接状态的通知。

```javascript
/**
* 同步玩家状态
* @param {Object} request 
* @param {number} request.gameID 游戏ID 
* @param {string} request.roomID 房间ID
* @param {number} request.userID 用户ID
* @param {number} request.state 1.网络异常、正在重连 2.重连成功 3.重连失败，退出房间
* @memberof App
*/
onUserState(request)
```

## 房间详情

Matchvs 提供了在gameServer 里查询房间详情的接口，查询结果在`onRoomDetail()`中返回。

```javascript
/**
* 查询房间详情
* @param {Object} msg getRoomDetail消息结构
* @param {string} msg.roomID 房间ID
* @param {number} msg.gameID 游戏ID 
* @memberof Push
*/
getRoomDetail(msg)
```

```javascript
/**
 * 房间详情信息
 * @param {Object} request
 * @param {number} request.gameID 游戏ID
 * @param {string} request.roomID 房间ID
 * @param {number} request.userID 用户ID
 * @param {Object} request.roomDetail 房间详情
 * @param {string} request.roomDetail.roomID 房间ID 
 * @param {number} request.roomDetail.state 房间状态：1开放、2关闭
 * @param {number} request.roomDetail.maxPlayer 房间最大人数
 * @param {number} request.roomDetail.mode 模式
 * @param {number} request.roomDetail.canWatch 是否可观战
 * @param {Uint8Array} request.roomDetail.roomProperty 房间属性
 * @param {number} request.roomDetail.owner 房主
 * @param {number} request.roomDetail.createFlag 房间创建途径：1系统创建房间、2玩家创建房间
 * @param {Object[]} request.roomDetail.playersList 房间用户列表
 * @param {number} request.roomDetail.playersList[].userID 用户ID
 * @param {Uint8Array} request.roomDetail.playersList[].userProfile 用户profile
 * @memberof App
 */
onRoomDetail(request)
```

## 修改房间属性

客户端修改房间属性时，gameServer 会触发`onSetRoomProperty()`，开发者可以将"房间属性修改的相关逻辑"写到该方法里。

```javascript
/**
* 修改房间自定义属性
* @param {Object} request 
* @param {number} request.gameID 游戏ID
* @param {string} request.roomID 房间ID
* @param {number} request.userID 用户ID
* @param {number} request.roomProperty 房间自定义属性
* @memberof App
*/
onSetRoomProperty(request)
```

另外 Matchvs 提供了在 gameServer 里修改房间自定义属性的接口。

```javascript
/**
 * 推送房间自定义属性修改消息
 * @param {Object} msg 消息结构
 * @param {number} msg.gameID 游戏ID
 * @param {string} msg.roomID 房间ID
 * @param {Uint8Array} msg.roomProperty 房间属性
 * @memberof Push
 */
setRoomProperty(msg)
```

## 设置帧同步帧率

当客户端修改房间帧同步帧率时，gameServer 触发`onSetFrameSyncRate()`，开发者可以将"设置房间帧同步帧率“的相关逻辑写到该方法里。

```javascript
/**
* 设置帧率
* @param {Object} request
* @param {number} request.gameID
* @param {string} request.roomID
* @param {number} request.frameRate 帧率
* @param {number} request.frameIndex 初始帧编号
* @param {string} request.timestamp 时间戳
* @param {number} request.enableGS gameServer 是否参与帧同步
* @memberof App
*/
onSetFrameSyncRate(request)
```

另外 Matchvs 提供了在 gameServer 里设置房间帧同步帧率的接口：

```javascript
/**
* 设置帧率
* @param {Object} msg
* @param {number} msg.gameID 游戏ID 
* @param {string} msg.roomID 房间ID
* @param {number} msg.frameRate 帧率（0到20，且能被1000整除）
* @param {number} msg.enableGS gameServer 是否参与帧同步（0：不参与；1：参与）
* @memberof Push
*/
setFrameSyncRate(msg)
```

## 接收帧同步消息

当房间启用了 gameServer 帧同步，同时客户端指定了将帧同步消息发往 gameServer 时，gameServer 即可接收到该房间的帧同步消息。

```javascript
/**
* 帧数据
* @param {Object} request
* @param {number} request.gameID
* @param {string} request.roomID
* @param {number} request.frameIndex 帧编号
* @param {Object[]} request.frameItems 元数据集合
* @param {number} request.frameItems[].srcUserID 发送用户ID
* @param {string} request.frameItems[].cpProto 自定义消息内容
* @param {string} request.frameItems[].timestamp 时间戳
* @param {number} request.frameWaitCount 等待的帧数
* @memberof App
*/
onFrameUpdate(request) 
```

## 发送帧同步消息

当房间启用了 gameServer 帧同步时，gameServer 可以主动发送帧同步消息。

```javascript
/**
* 发送帧数据
* @param {Object} msg
* @param {number} msg.gameID 游戏ID
* @param {string} msg.roomID 房间ID
* @param {string|Uint8Array} msg.cpProto 消息内容
* @param {number} msg.operation 0：只发客户端；1：只发GS；2：同时发送客户端和GS
* @memberof Push
*/
frameBroadcast(msg)
```

