## Unity开发者新手上路

## 开发环境搭建

Unity安装和下载(必须Unity 5+):

 https://store.unity.com/

### Matchvs SDK下载

Matchvs SDK是一个Unity的插件包,同Unity Stroe里面的其他资源一样,通过UnityIDE Import进工程即可

http://www.matchvs.com/serviceDownload 选择UnitySDK.

## Demo上手

为了便于开发者使用和理解Matchvs的实时联网SDK，Matchvs提供了简洁的Demo来展示多人实时联网游戏的开发过程和效果。  

Demo支持两人或三人同时游戏，匹配成功后，玩家通过向左、向右、加速按钮可以操纵图标移动，先到达终点者胜利。

**注意** 下载Demo源码后，需使用Unity 5+打开工程。

### Demo下载地址

https://github.com/matchvs/demo-unity

## 游戏配置

Demo运行之前需要去 [官网](http://www.matchvs.com ) 配置游戏相关信息，以获取Demo运行所需要的GameID、AppKey、SecretID。如图：

![](http://imgs.matchvs.com/static/2_1.png)

![](http://imgs.matchvs.com/static/2_2.png)

获取到相关游戏信息之后，运行Demo，填写GameID,AppKey,SecretID,点击登录。

如图所示：

![](http://imgs.matchvs.com/static/2_3.jpg)

## 初始化SDK

Matchvs SDK提供了两个重要文件：`MatchVSEngine`和`MatchVSResponse`。想要获取游戏中玩家加入、离开房间、数据收发的信息，需要先实现`MatchVSResponse`中的抽象方法。  

我们现在新建子类MatchVSResponseInner继承自抽象类MatchVSResponse，如下：

```
// 以下是 MatchVSResponseInner.cs 文件的代码片段
public class MatchVSResponseInner : MatchVSResponse
{
	//实现所有父类的抽象方法
}
```

文件路径:`Assets\Script\MainUI\MatchVSResponseInner.cs  `

完成以上步骤后，我们可以调用初始化接口建立相关资源。

```
engine.init(matchVSResponses, channel, platform, gameid);
```

初始化操作，在GameManager.cs文件中完成。

文件路径:`Assets\Script\MainUI\GameManager.cs  ` 

**注意** 在整个应用全局，开发者只需要对引擎做一次初始化。

## 建立连接

接下来，我们就可以从Matchvs获取一个合法的用户ID，通过该ID连接至Matchvs服务端。  

获取用户ID：

```
engine.registerUser();
```

登录：

```
engine.login(userID,token,gameid,gameVersion,appkey,
    secret,deviceID,gatewayid);
```

文件路径 ：`Assets\Script\MainUI\GameManager.cs ` 

## 加入房间

成功连接至Matchvs后，即可点击随机匹配加入一个房间进行游戏。  

代码如下:

```
engine.joinRandomRoom(int iMaxPlayer, string strUserProfile);
```

匹配界面配图：

![](http://imgs.matchvs.com/static/2_6.png)

文件路径:`Assets\Script\MainUI\GameManager.cs`  

## 停止加入

如果游戏人数已经满足开始条件且游戏设计中不提供中途加入，此时需告诉Matchvs不要再向房间里加人。  

代码如下:

```
engine.joinOver(roomID,proto);
```

- 我们设定如果3秒内有3个真人玩家匹配成功则调用JoinOver并立即开始游戏； 
- 如果3秒内只有2个真人玩家，则再添加一个机器人（机器人头像默认为一个即可，昵称为：机器人1），调用JoinOver并开始游戏；  
- 如果3秒内只有1个真人玩家，则添加两个机器人（机器人头像默认即可，昵称为：机器人1，机器人2），调用JoinOver并开始游戏。

```
	// 匹配时间判断
	if (matchTime >= 3)
	{
		// 房主身份判定
		if (!GameManager.Instance.RoomOwner)
			return;
		// 当前房间人数判定
		if (currentCount < 3)
		{
			// 确定剩余人数
			int cout = 3 - currentCount;
			int userid;
			for (int i = 0; i < cout; i++)
			{   
                // 随机创建用户ID
				userid = Random.Range(10000, 100000000);
				GameManager.Instance.AddRoomPlayer(new UserInfo(userid,true));

                items[currentCount].UpdateInfo(userid);

                // 创建负载消息（cpProto），
				JsonData data = new JsonData();
				data["action"] = "robot";
				data["userid"] = userid;
				string value = data.ToJson();
                // 向其他玩家发送机器人消息
				GameManager.SendEvent(value);

				currentCount++;
			}
	
			StartCoroutine(ShowGameRoom());
			// 调用JoinOver结束房间匹配
			GameManager.JoinOver(roomInfo.roomID, roomInfo.roomProperty);
		}
	}
	else
	{
         // 时间统计
		 matchTime += Time.deltaTime;
    }
```

文件路径：`Assets\Script\MainUI\MatchingBoard.cs`  

## 游戏数据传输

当一个玩家进行向左、向右、加速操作时，我们将这些操作广播给房间内其他玩家。界面上同步展示各个玩家的状态变化。  

代码如下：

```
engine.sendEvent(int iPriority, int iType,string pMsg,int iTargetType,int[] pTargetUserId);
```

![](http://imgs.matchvs.com/static/2_7.png)

数据传输回调 ：

```
int sendEventResponse(int status)
```

收到其他人发的数据：

```
int sendEventNotify(MsMsgNotify tRsp)
{
	tRsp.srcUid;
	tRsp.priority;
	tRsp.cpProto;
}
```

向左、向右、加速操作代码如下:

```
	//加速操作
public void AccelerateDown()
{
		//修改移动速度
        target.GetComponent<CharacterMove>().moveSpeed += 0.2f;
        GetPlayerInfoByID(GameManager.userID).UpdateSpeed(80);

		//发送当前状态
        JsonData data = new JsonData();
        data["action"] = "AccelerateDown";
        string value = data.ToJson();
        GameManager.SendEvent(value);
		
		//修改状态消息
        Message("加速");
}

//向右
public void RightDown()
{
		//修改移动速度
        target.GetComponent<CharacterMove>().rightMoveSpeed += 0.2f;

		//发送当前状态
        JsonData data = new JsonData();
        data["action"] = "RightDown";
        string value = data.ToJson();
        GameManager.SendEvent(value);

		//修改状态消息
        Message("开始向右");
}

//向左
public void LeftDown()
{
	// 修改移动速度
    target.GetComponent<CharacterMove>().leftMoveSpeed += 0.2f;
	
	// 发送当前状态
    JsonData data = new JsonData();
    data["action"] = "LeftDown";
    string value = data.ToJson();
    GameManager.SendEvent(value);
		
	// 修改状态消息
     Message("开始向左");
}
	
	
```

在执行相关操作的同时，向其他终端发送相关指令并在控制台输出相关状态变化日志。

文件路径：`Assets\Script\MainUI\GameRoomBoard.cs ` 

## 离开房间

抵达终点后游戏结束，游戏结束后离开房间。  

![](http://imgs.matchvs.com/static/2_8.png)

游戏中途离开房间可能存在匹配过程中、游戏过程中两种情况：

```
 engine.leaveRoom(string payload);
```

- 匹配过程中离开房间，分为房主离开房间和成员离开房间。

1. 成员离开房间：

```
public void LeaveRoom()
{
	GameManager.LeaveRoom();
}
```

文件路径：`Assets\Script\MainUI\MatchingBoard.cs ` 

其他成员消息处理：

```
private void LeavePeerNofity(MsRoomPeerLeaveRsp tRsp)
{
	int userID = tRsp.userID;
	for (int i = 0; i < items.Length; i++)
	{
		items[i].RemoveNotify(userID);
	}
	....
}
```

文件路径：`Assets\Script\MainUI\MatchingBoard.cs`  

做相应的界面更新即可。  

1. 房主离开房间：

```
public void LeaveRoom()
{
	GameManager.LeaveRoom();
}
```

文件路径：`Assets\Script\MainUI\MatchingBoard.cs`  

其他成员消息处理:

```
private void LeavePeerNofity(MsRoomPeerLeaveRsp tRsp)
{
....

	if (!GameManager.Instance.RoomOwner)
	{
		GameManager.Instance.RoomOwner = true;
		for (int i = 0; i < items.Length; i++) {
		items[i].RemoveNotify(GameManager.userID);
		}
		items[0].UpdateInfo(GameManager.userID);
	}
	
	GameManager.Instance.RemovePlayer(userID);
}
```

此时需要确立新房主，并做相应的界面更新。
文件路径：`Assets\Script\MainUI\MatchingBoard.cs ` 

- 游戏过程中离开房间,分为成员离开房间和房主离开房间

1. 成员离开房间:

```
public void OnBack()
{
	....		
	GameManager.LeaveRoom();
}
```

文件路径：`Assets\Script\MainUI\GameRoomBoard.cs`  

房间其他成员：

```
private void LeavePeerNofity(MsRoomPeerLeaveRsp tRsp)
{
    currentCount--;
}
```

文件路径：`Assets\Script\MainUI\GameRoomBoard.cs`  

1. 房主离开房间:

```
public void OnBack()
{
	if (GameManager.Instance.RoomOwner) {
		int roomOwner = 0;
		for (int i = 0; i < GameManager.Instance.RoomPlayers.Count; i++) {
			if (GameManager.Instance.RoomPlayers[i].userid != GameManager.userID &&
			!GameManager.Instance.RoomPlayers[i].robot) {
				roomOwner = GameManager.Instance.RoomPlayers[i].userid;
				break;
			}
		}
	JsonData data = new JsonData();
	data["action"] = "roomLeaveRoom";
	data["RoomOwner"] = roomOwner;
	string value = data.ToJson();
	GameManager.SendEvent(value);
	}
		
	....
}
```

文件路径：`Assets\Script\MainUI\GameRoomBoard.cs`

房主离开房间，此时需要指定房间内其他成员为房主，并发送相关消息，通知其他成员，此时的房主ID。成为房主后，就要完成房主需要完成的相关事宜。  

房间其他成员:

```
private void LeavePeerNofity(MsRoomPeerLeaveRsp tRsp)
{
	currentCount--;
}
	
....
if (action.Equals("roomLeaveRoom"))
{
	int gameowner = (int) data["RoomOwner"];
	if (GameManager.userID == gameowner)
	{
		GameManager.Instance.RoomOwner = true;
	}
}
....
	
```

文件路径：`Assets\Script\MainUI\GameRoomBoard.cs`
房间人数调整，另外确定新房主。

## 数据存取

将游戏里产生的金币和钻石存储至服务器。

```
MatchVSHttp.HashSet(this,gameid,userID,key,value,appkey,token, (context,error) => { });
```

文件路径：Assets\Script\MainUI\GameManager.cs `

将存储的数据取出来：

```
 MatchVSHttp.HashGet(this,gameid,userID,key,appkey,token,rsp);
```

文件路径：Assets\Script\MainUI\GameManager.cs

## 游戏登出

退出游戏时，需要调用登出接口将与Matchvs的连接断开。

```
engine.logout();
```

文件路径：`Assets\Script\MainUI\GameManager.cs ` 

## 反初始化

在登出后，调用反初始化对资源进行回收。  

```
engine.uninit();
```

文件路径：`Assets\Script\MainUI\GameManager.cs ` 

## 错误处理

```
int errorResponse(string error)
```

**注意 ：**Matchvs SDK相关的异常信息可通过该接口获取

文件路径：`Assets\Script\MainUI\GameManager.cs`

## 错误码

错误码表 <http://www.matchvs.com/service?page=ErrCode> 

## 更多详细说明

更多详细说明见 API手册
<http://www.matchvs.com/service?page=CShapeAPI>