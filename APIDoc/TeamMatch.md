/*
Title: 组队匹配
*/

[组队示例](http://demo-ge.matrix.jdcloud.com/RombBoy/)

当前页面是组队相关的API说明。我们同样是以 MatchvsEngine 和 MatchvsResponse 的对象 engine 和 response 来说明。

组队匹配信息可以使用getRoomDetail 接口查看房间是否有组队。



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
| roomName     | string              | 房间名称                                                     | “jdge”  |
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

队伍匹配结果通过这个接口通知队伍中的所有人。匹配成功后，默认已经加入了房间，不用额外的调用加入房间接口。

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

