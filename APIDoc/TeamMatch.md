/*
Title: 组队匹配
*/

[组队示例](http://demo.matchvs.com/RombBoy/)

当前页面是组队相关的API说明。我们同样是以 MatchvsEngine 和 MatchvsResponse 的对象 engine 和 response 来说明。

组队匹配信息可以使用getRoomDetail 接口查看房间是否有组队。请求接口返回码可以参考 [错误码说明](https://doc.matchvs.com/ErrCode) 



## 创建队伍

组队匹配需要调用这个接口先创建一支队伍。

- 请求接口：createTeam
- 回调接口：createTeamResponse

### createTeam

```
engine.createTeam(teaminfo)
```

#### 参数 

teaminfo 是 MVS.MsCreateTeamInfo 的对象

| 属性        | 类型   | 描述                  | 示例值     |
| ----------- | ------ | --------------------- | ---------- |
| password    | string | 队伍权限              | 1ab2       |
| capacity    | number | 队伍人数容量          | 3          |
| mode        | number | 模式-由开发者自己定义 | 0          |
| visibility  | number | 0-不可见 1-可见       | 1          |
| userProfile | string | 用户自定义信息        | "用户头像" |

#### 返回码

| 返回码 | 说明                               |
| ------ | ---------------------------------- |
| 0      | 接口调用成功                       |
| -2     | 未初始化                           |
| -3     | 正在初始化                         |
| -4     | 未登录                             |
| -5     | 正在登录                           |
| -7     | 正在创建房间，或者正在加入游戏房间 |
| -6     | 不在观战房间                       |
| -10    | 正在离开房间                       |
| -11    | 正在登出                           |
| -12    | 正在加入观战房间                   |
| -13    | 队伍正在匹配中                     |

### createTeamResponse

```
response.createTeamResponse(rsp)
```

#### 参数 rsp 属性

| 属性   | 类型   | 描述            | 示例值                |
| ------ | ------ | --------------- | --------------------- |
| status | number | 状态值 200 成功 | 200                   |
| teamID | number | 队伍编号        | 131113213211323121231 |
| owner  | number | 队长            | 123456                |

#### 示例代码

```javascript
var req = {
    password:"ok",
    capacity:2,
    mode:0,
    visibility:1,
    userProfile:"temamode"
};
engine.createTeam(req)
response.createTeamResponse = function(rsp){
    console.log("[RSP]createTeamResponse:"+JSON.stringify(rsp));
};
```



## 加入队伍

要邀请好友加入队伍，使用 createTeam 接口创建的队伍信息，调用 joinTeam 接口就可以加入指定的队伍。队伍中其他玩家会收到 joinTeamNotify 接口的通知。

- 请求接口：joinTeam
- 回调接口：joinTeamResponse, joinTeamNotify

### joinTeam

```
engine.joinTeam(teaminfo)
```

#### 参数 teaminfo 属性

| 属性        | 类型   | 描述           | 示例值                |
| ----------- | ------ | -------------- | --------------------- |
| teamID      | string | 队伍ID         | 131113213211323121231 |
| password    | string | 队伍权限值     | 1ab2                  |
| userProfile | string | 用户自定义信息 | “用户头像”            |

#### 返回码

| 返回码 | 说明                               |
| ------ | ---------------------------------- |
| 0      | 接口调用成功                       |
| -2     | 未初始化                           |
| -3     | 正在初始化                         |
| -4     | 未登录                             |
| -5     | 正在登录                           |
| -7     | 正在创建房间，或者正在加入游戏房间 |
| -6     | 不在观战房间                       |
| -10    | 正在离开房间                       |
| -11    | 正在登出                           |
| -12    | 正在加入观战房间                   |
| -13    | 队伍正在匹配中                     |



### joinTeamResponse

调用加入队伍接口，自己会收到这个接口通知

```javascript
response.joinTeamResponse(rsp)
```

#### 参数 rsp 属性

| 属性                 | 类型          | 描述                  | 示例值                |
| -------------------- | ------------- | --------------------- | --------------------- |
| team                 | object        | 队伍信息              |                       |
| team.teamID          | string        | 队伍ID                | 131113213211323121231 |
| team.password        | string        | 队伍权限值            | 1ab2                  |
| team.capacity        | number        | 队伍人数容量          | 3                     |
| team.mode            | number        | 模式-开发者自定义的值 | 0                     |
| team.owner           | number        | 队长                  | 123456                |
| status               | number        | 加入队伍状态值        | 200                   |
| userList             | array<object> | 队伍玩家列表          |                       |
| userList.userID      | number        | 玩家ID                | 2356                  |
| userList.userProfile | string        | 玩家自定义信息        | “玩家头像”            |



### joinTeamNotify

有人加入队伍，队伍中其他玩家会收到 joinTeamNotify 接口的通知

```
response.joinTeamNotify(notify)
```

#### 参数 notify 属性

| 属性             | 类型   | 描述           | 示例值     |
| ---------------- | ------ | -------------- | ---------- |
| user             | object | 玩家信息       |            |
| user.userID      | number | 玩家ID         | 2356       |
| user.userProfile | string | 玩家自定义信息 | “玩家头像” |

#### 示例代码

```javascript
response.joinTeamResponse = function(rsp){
	console.log("[RSP]joinTeamResponse:"+JSON.stringify(rsp));
};

response.joinTeamNotify = function(rsp){
	console.log("[RSP]joinTeamNotify:"+JSON.stringify(rsp));
};
var req ={
    teamID: PutIn("teamID"),
    password: PutIn("teamPwd"),
    userProfile:"i come team 哈哈"
};
console.log("[REQ]STJoinTeam:"+engine.joinTeam(req));
```



##离开队伍

在队伍中如果要离开队伍就调用 leaveTeam 接口，其他人会收到 leaveTeamNotify 接口通知，自己收到 leaveTeamResponse 通知

- 请求接口：leaveTeam
- 回调接口：leaveTeamResponse, leaveTeamNotify

### leaveTeam

```
engine.leaveTeam()
```

#### 参数：无

#### 返回码

| 返回码 | 说明                               |
| ------ | ---------------------------------- |
| 0      | 接口调用成功                       |
| -2     | 未初始化                           |
| -3     | 正在初始化                         |
| -4     | 未登录                             |
| -5     | 正在登录                           |
| -7     | 正在创建房间，或者正在加入游戏房间 |
| -6     | 不在观战房间                       |
| -10    | 正在离开房间                       |
| -11    | 正在登出                           |
| -12    | 正在加入观战房间                   |
| -13    | 队伍正在匹配中                     |



### leaveTeamResponse

```
response.leaveTeamResponse(rsp)
```

#### 参数

| 属性   | 类型   | 描述            | 示例值                |
| ------ | ------ | --------------- | --------------------- |
| userID | number | 离开者ID        | 123456                |
| teamID | number | 离开的队伍号    | 131113213211323121231 |
| status | number | 状态值 200 成功 | 200                   |



### leaveTeamNotify

```
response.leaveTeamNotify(rsp)
```

#### 参数

| 属性   | 类型   | 描述         | 示例值                |
| ------ | ------ | ------------ | --------------------- |
| teamID | number | 离开的队伍号 | 131113213211323121231 |
| userID | number | 离开者ID     | 3264                  |
| owner  | number | 队长         | 123456                |

#### 示例代码

```javascript
response.leaveTeamResponse = function(rsp){
	console.log("[RSP]leaveTeamResponse:"+JSON.stringify(rsp));
};
response.leaveTeamNotify = function(notify){
	console.log("[RSP]leaveTeamNotify:"+JSON.stringify(notify));
};
console.log("[REQ]STLeaveTeam:"+engine.leaveTeam());
```



##发起队伍匹配 

加入了队伍后，可以发起队伍匹配，队伍匹配的队伍成员数和队伍数量可在调用匹配的时候调用，比如我需要做5v5 那么队伍成员数是5，队伍数量是2。队伍匹配调用 teamMatch 接口。teamMatch 接口可以由队伍中任意一个人调用。

- 请求接口：teamMatch
- 回调接口：teamMatchResponse, teamMatchStartNotify, teamMatchResultNotify

### teamMatch

```
engine.teamMatch(info)
```

#### 参数 info 属性

info 是 MVS.MsTeamMatchInfo 类型。

| 属性         | 类型                | 描述                                                         | 示例值     |
| ------------ | ------------------- | ------------------------------------------------------------ | ---------- |
| roomName     | string              | 房间名称                                                     | “matchvs”  |
| maxPlayer    | number              | 房间最大人数                                                 | 10         |
| canWatch     | number              | 是否可以观战 1-可以观战 02不可以观战。                       | 1          |
| mode         | number              | 玩家自定义数据                                               | 0          |
| visibility   | number              | 房间是否可见（是否可以被getRoomListEx查看到）。0-不可见 1- 可见 | 1          |
| roomProperty | string              | 房间自定义信息                                               | "房间信息" |
| watchSet     | MVS.MsWatchSet      | 观战设置，但canWatch 设置为1的时候有效                       |            |
| cond         | MVS.MsTeamMatchCond | 匹配设置                                                     |            |

#### 参数 MVS.MsWatchSet属性

| 属性       | 类型    | 描述                 | 示例值          |
| ---------- | ------- | -------------------- | --------------- |
| cacheMS    | number  | 缓存多久的数据       | 6*1000（6分钟） |
| maxWatch   | number  | 最大人数             | 3               |
| delayMS    | number  | 观看延迟多久后的数据 | 2000            |
| persistent | boolean | 是否持久缓存         | false           |

#### 参数 MVS.MsTeamMatchCond属性

| 属性          | 类型   | 描述                                                         | 示例值 |
| ------------- | ------ | ------------------------------------------------------------ | ------ |
| teamNum       | number | 需要的队伍的数量                                             | 2      |
| teamMemberNum | number | 每支队伍的队员数                                             | 5      |
| timeout       | number | 匹配的超时时间,指匹配多久就视为超时，没有匹配到。接口teamMatchResultNotify会报422错误 | 20     |
| weight        | number | 权值                                                         | 10     |
| weightRange   | number | 匹配范围                                                     | 5      |
| weightRule    | number | 匹配规则， 默认是0                                           | 0      |
| full          | number | 是否人满匹配，0-人不满也可以匹配，1-人满匹配 (人不满匹配不到会超时报422错误码) | 0      |

#### 返回码

| 返回码 | 说明                               |
| ------ | ---------------------------------- |
| 0      | 接口调用成功                       |
| -2     | 未初始化                           |
| -3     | 正在初始化                         |
| -4     | 未登录                             |
| -5     | 正在登录                           |
| -7     | 正在创建房间，或者正在加入游戏房间 |
| -6     | 不在观战房间                       |
| -10    | 正在离开房间                       |
| -11    | 正在登出                           |
| -12    | 正在加入观战房间                   |
| -13    | 队伍正在匹配中                     |



### teamMatchResponse

发送匹配的人会收到这个接口的回调，告诉自己队伍正在匹配中。其他人会收到 teamMatchStartNotify 的通知。

```
response.teamMatchResponse(rsp)
```

#### 参数 rsp 属性

| 属性   | 类型   | 描述            | 示例值 |
| ------ | ------ | --------------- | ------ |
| status | number | 状态值 200-成功 | 200    |



### teamMatchStartNotify

在队伍中有人调用了匹配接口，其他人就会收到这个接口的通知，队伍开始进入匹配啦。

```
response.teamMatchStartNotify(notify);
```

#### 参数 notify 属性

| 属性   | 类型   | 描述         | 示例值                |
| ------ | ------ | ------------ | --------------------- |
| teamID | string | 匹配的队伍号 | 131113213211323121231 |
| userID | number | 发起匹配的人 | 123456                |



### teamMatchResultNotify

队伍匹配结果通过这个接口通知队伍中的所有人。匹配成功但并不是加入了房间，收到匹配成功后，马上给其他玩家发送消息是不可行的。匹配成功后SDK会自动处理加入房间的逻辑，开发者不用额外的调用加入房间接口，只需要处理，joinRoomResponse 接口和 joinRoomNotify 接口即可，通过这个两个接口判断是否所有人都加入了房间，如果需要检查谁掉线了可以处理 networkStateNotify 接口。

```
response.teamMatchResultNotify(notify);
```

#### 参数 notify 属性

| 属性     | 类型   | 描述                           | 示例值 |
| -------- | ------ | ------------------------------ | ------ |
| status   | number | 匹配的状态，200 成功，422 超时 |        |
| brigades | object | 匹配到队伍列表信息             |        |
|          |        |                                |        |

#### brigades 属性

| 属性       | 类型          | 描述           | 示例值 |
| ---------- | ------------- | -------------- | ------ |
| brigadeID  | number        | 大队伍的ID     | 1      |
| playerList | Array<object> | 小队伍信息列表 |        |

#### playerList 数据项属性

| 属性        | 类型   | 描述           | 示例值     |
| ----------- | ------ | -------------- | ---------- |
| userID      | number | 用户ID号       | 123456     |
| userProfile | string | 玩家自定义数据 | “玩家头像” |

#### 数据示例：

````json
{
  "status": 200,
  "brigades": [
    {
      "brigadeID": 1,
      "playerList": [
        {
          "userID": 388139,
          "userProfile": "temamode"
        },
        {
          "userID": 388138,
          "userProfile": "i come team 哈哈"
        }
      ]
    },
    {
      "brigadeID": 2,
      "playerList": [
        {
          "userID": 388149,
          "userProfile": "temamode"
        },
        {
          "userID": 388148,
          "userProfile": "i come team 哈哈"
        }
      ]
    }
  ],
  "roomInfo": {
    "roomID": "1722967918502744099",
    "roomProperty": "STTeamMatch",
    "ownerId": 388138,
    "owner": 388138,
    "state": 0
  }
} 
````

#### 示例代码

```javascript
response.teamMatchResponse = function(rsp){
    console.log("[RSP]teamMatchResponse:"+JSON.stringify(rsp));
};
response.teamMatchResultNotify = function(notify){
    console.log("[RSP]TeamMatchResultNotify:"+JSON.stringify(notify));
};
response.teamMatchStartNotify = function(notify){
    console.log("[RSP]TeamMatchStartNotify:"+JSON.stringify(notify));
};

var cond ={
    teamNum:2,
    MemberNum:2,
    timeout:15,
    weight:15,
    weightRange:5,
    weightRule:0,
    full:0
};
var info = new MVS.MsTeamMatchInfo("STTeamMatch", 10, 1, 0,1,"STTeamMatch",
           new MVS.MsTeamMatchCond(cond.teamNum, cond.MemberNum, cond.timeout, cond.weight, cond.weightRange, cond.weightRule, cond.full), 
           new MVS.MsWatchSet(600000, 3, 60000, true)
);
console.log("[REQ]STTeamMatch:"+engine.teamMatch(info));
```



## cancelTeamMatch

开始匹配后，在还没有匹配到队伍的情况下可以取消当前匹配。这个时候所有小队伍内的玩家都会从匹配列表中移除，触发取消匹配的人收到 cancelTeamMatchResponse 回调，其他人收到 cancelTeamMatchNotify 的回调。

```
engine.cancelTeamMatch(args)
```

#### args 属性

| 属性    | 类型   | 说明     | 示例               |
| ------- | ------ | -------- | ------------------ |
| cpProto | string | 附带消息 | “有事，暂时不玩啦” |



## cancelTeamMatchResponse

调用取消匹配接口 cancelTeamMatch 时会收到这个接口的回调。

```
response.cancelTeamMatchResponse(rsp)
```

#### rsp 属性

| 属性   | 类型   | 说明                                                         | 示例 |
| ------ | ------ | ------------------------------------------------------------ | ---- |
| status | number | 状态值 200 成功，其他 [请看说明](https://doc.matchvs.com/ErrCode) | 200  |



## cancelTeamMatchNotify

有队员调用取消匹配接口 cancelTeamMatch 时，其他队员会收到这个接口的回调

```typescript
response.cancelTeamMatchNotify(notify)
```

#### notify 属性

| 属性    | 类型   | 说明     | 示例                |
| ------- | ------ | -------- | ------------------- |
| userID  | number | 用户ID   | 123456              |
| teamID  | string | 队伍ID   | “12345678901234567” |
| cpProto | string | 附带消息 | “有事，不能玩”      |

#### 示例代码

```javascript
response.cancelTeamMatchResponse = function(rsp){
    console.log("[RSP]cancelTeamMatchResponse:"+JSON.stringify(rsp));
};
response.cancelTeamMatchNotify = function(notify){
    console.log("[RSP]cancelTeamMatchNotify:"+JSON.stringify(notify));
};
engine.cancelTeamMatch({cpProto:"cancel team match"});
```



## kickTeamMember

剔除对内成员，在组队期间如果没有开始组队匹配可以使用这个接口剔除其他玩家，不可以剔除自己。

```typescript
engine.kickTeamMember(args)
```

#### args 属性

| 属性    | 类型   | 说明     | 示例         |
| ------- | ------ | -------- | ------------ |
| userID  | number | 用户ID   | 123456       |
| cpProto | string | 附带消息 | “不想和你玩” |

#### 返回值

请看 [错误码说明](https://doc.matchvs.com/ErrCode) 



## kickTeamMemberResponse

调用踢人接口会收到这个接口的回调。

```typescript
response.kickTeamMemberResponse(rsp)
```

#### rsp 属性

| 属性    | 类型          | 说明                                                         | 示例               |
| ------- | ------------- | ------------------------------------------------------------ | ------------------ |
| status  | number        | 状态值 200 成功，其他 [请看说明](https://doc.matchvs.com/ErrCode) | 200                |
| members | Array<number> | 当前队伍成员                                                 | [123456, 678901]   |
| owner   | number        | 队长                                                         | 123456             |
| teamID  | string        | 队伍号                                                       | "1234567890987654" |



## kickTeamMemberNotify

有别的玩家调用了踢人接口，那么另外其他玩家会收到这个接口的回调。

```typescript
response.kickTeamMemberNotify(notify)
```

#### notify 属性

| 属性      | 类型          | 说明       | 示例               |
| --------- | ------------- | ---------- | ------------------ |
| teamID    | string        | 队伍ID     | “1234567890987654” |
| userID    | number        | 发起剔除者 | 123456             |
| dstUserID | number        | 剔除的人   | 678901             |
| owner     | number        | 队长       | 123456             |
| members   | Array<number> | 当前队员   | [123456,234564]    |
| cpProto   | string        | 附带消息   | "不想和你玩"       |

#### 示例代码

```javascript
response.kickTeamMemberResponse = function(rsp){
    console.log("[RSP]kickTeamMemberResponse:"+JSON.stringify(rsp));
};
response.kickTeamMemberNotify = function(notify){
    console.log("[RSP]kickTeamMemberNotify:"+JSON.stringify(notify));
};
engine.kickTeamMember({userID:userid, cpProto:"kick team member"});
```



## sendTeamEvent

 在组队期间，可以使用这个接口发送消息给其他的队内成员，自己收到 sendTeamEvent 的回调，其他成员收到 sendTeamEventNotify的回调，类似 sendEvent接口的使用，这个接口有对消息发送的频率做了限定，目前限定每秒不能超过20次。同时消息长度也有限定。只有在队伍中才能发送消息。

```typescript
engine.sendTeamEvent(args)
```

#### args 属性

| 属性       | 类型          | 说明                                      | 示例    |
| ---------- | ------------- | ----------------------------------------- | ------- |
| dstType    | number        | 0-包含dstUids  1-排除dstUids              | 1       |
| msgType    | number        | 0-只发client  1-只发gs  2-client和 gs都发 | 0       |
| dstUserIDs | Array<number> | 指定的用户列表 配合 dstType 使用          | []      |
| data       | string        | 发送的数据                                | "hello" |

#### 返回值

请看 [错误码说明](https://doc.matchvs.com/ErrCode) 



## sendTeamEventResponse

发送队内消息回调，发送者收到的回调

```typescript
response.sendTeamEventResponse(rsp)
```

#### rsp 属性

| 属性       | 类型          | 说明                                                     | 示例             |
| ---------- | ------------- | -------------------------------------------------------- | ---------------- |
| status     | number        | 状态值，其他 [请看说明](https://doc.matchvs.com/ErrCode) | 200              |
| dstUserIDs | Array<number> | 发给了哪些人                                             | [123456 , 68790] |



## sendTeamEventNotify

有别的玩家调用了发送消息接口，那么另外其他玩家会收到这个接口的回调。

```typescript
response.sendTeamEventNotify(notify)
```

#### notify 属性

| 属性    | 类型   | 说明           | 示例               |
| ------- | ------ | -------------- | ------------------ |
| teamID  | string | 当前队伍号     | “1234567890987654” |
| userID  | number | 发送消息的玩家 | 68790              |
| cpProto | string | 消息内容       | 123456             |

#### 示例代码

```javascript
response.sendTeamEventResponse = function(rsp){
    console.log("[RSP]sendTeamEventResponse:"+JSON.stringify(rsp));
};
response.sendTeamEventNotify = function(notify){
    console.log("[RSP]sendTeamEventNotify:"+JSON.stringify(notify));
};
engine.sendTeamEvent({msgType:0, dstType:1, data:data, dstUserIDs:[]});
```

