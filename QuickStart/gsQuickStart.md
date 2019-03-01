/*
Title: 快速入门-gameServer
Sort: 6
*/



gameServer是基于房间的可以自定义逻辑服务端框架。  

**注意：**gameServer 无需自己搭建服务器即可开始本地调试，仅需三个步骤： 

1. 填写配置文件  

2. 启动本地调试  

3. 启动gameServer  

**详细流程如下 ：**


## 支持语言

目前支持C#，node.js，java，go 4种语言。

如需要其他语言，可以加入技术支持群 （QQ ：450335262）提需求。

## 创建游戏

在开始使用 gameServer 之前，你需要创建自己的游戏。如何创建游戏详见 [快速入门](http://doc-ge.matrix.jdcloud.com/QuickStart/Guide-jdge)。 

## 创建 gameServer  

成功创建游戏后，点击左侧菜单栏 “gameServer” 即可进入 gameServer 列表页。

![](http://imgs.matchvs.com/static/Doc-img/new-start/gameServerimg/gsCLI1.png)

点击 “创建gameServer”，填写 gameServer 基本信息：

![](http://imgs.matchvs.com/static/Doc-img/new-start/gameServerimg/creategameserver.png)

创建成功后的 gameServer 相关信息展示：

![](http://imgs.matchvs.com/static/Doc-img/new-start/gameServerimg/creategameserversuccess.png)

gameServer 创建成功后，Matchvs 会为每个 gameServer 分配一个唯一的 git 仓库地址。

在本示例中，Matchvs 分配的 git 仓库地址为： `ssh://git@git.matchvs.com:3879/1424769556baec5362f5b1513f7e1167.git`。



## 下载框架代码

前往[下载中心](http://home-ge.matrix.jdcloud.com/serviceDownload)，下载对应语言版本的 gameServer 框架。

下载完成并解压，将解压后的所有文件复制到 `myGameServer`目录下。

**请确保目录结构完整，否则可能出现 gameServer 无法发布的情况。**

不同语言版本的文件结构不同:

### Node.js

```shell
myGameServer
├── .gitignore
├── Dockerfile
├── Makefile
├── README.md
├── conf
│   └── config.json
├── gsmeta
├── main.js
├── package.json
└── src
    └── app.js
```



### Java 

```shell
java-gameServer
├── demo         示例工程，修改工程中的App.Java文件即可
├── Config.json  配置文件
├── Dockerfile   docker文件不可修改
├── gsmeta       gameServer配置文件，不可修改。
├── Makefile     配置文件，不可修改。
├── README.md
```



### C # #

```shell
myGameServer
├── .gitignore
├── Makefile
├── README.md
├── gameClient
│   ├── Clienter.cs
│   ├── Program.cs
│   ├── gameClient.csproj
│   └── proto
├── gameServer
│   ├── Dockerfile
│   ├── MainServer.cs
│   ├── conf
│   ├── demo
│   ├── gameServer.csproj
│   ├── proto
│   ├── script
│   ├── src
│   ├── streamcs
│   └── util
├── gameServer.Test
│   ├── CancelTask.cs
│   ├── Disposer.cs
│   └── gameServer.Test.csproj
├── gameServer_csharp.sln
└── gameServermeta
```

### Golang

下载框架代码请到 [github](https://github.com/matchvs/gameServer-go) 中下载最新的，或者使用如下命令

```
go get -u github.com/matchvs/gameServer-go
```

代码目录结构如下：

```
┌ app/  		# 业务代码
├ conf/ 		# 配置文件
├ main.go 		# 程序入口
├ Dockerfile 	# docker部署，这个文件不要修改
├ Makefile 		# make 文件内容不能修改
└ gsmeta 		# 数据源文件内容不能修改
```

## 本地开发环境

### Node.js

支持**Node.js 8.0及以上版本**。

### Java

需要java开发环境 下载JDK环境,参考[JDK安装与环境变量配置](https://jingyan.baidu.com/article/6dad5075d1dc40a123e36ea3.html),支持JDK1.7以上

### C # #

支持 Visual Studio 2017 及以上版本，另外也支持 .NET Core 命令行接口 (CLI) 工具。

### Golang

使用 golang 版本的 gameServer 须知：

- 需要在本地安装好 golang 运行环境。如果还没有配置好 golang 环境的请先配置好。以下内容教程是默认你已经配置好并且熟悉 golang 开发环境。
- 在 Matchvs 官网创建了 gameServer。

## Config 配置

gameServer 配置文件路径为`myGameServer/conf/config.json`，其中包含以下默认配置项：

- **addr**：gameServer服务监听地址。**IP默认为 “0.0.0.0” 即可，不建议修改**。端口号可以从官网查询到：

  ![](http://imgs.matchvs.com/static/Doc-img/new-start/gameServerimg/gameserverdetail.png)

  该端口号由系统在创建 gameServer 时分配以保证全局唯一性。

  **注意：gameServer 只能使用系统分配的端口号，否则发布上线后将无法正常提供服务。**

- **logLevel**：日志配置。gameServer 使用 [log4js](https://www.npmjs.com/package/log4js) 作为日志管理框架，如需自定义 log 输出可在了解 log4js 的前提下自行修改配置文件。

- **register**：独立部署配置，仅在使用 Matchvs 独立部署解决方案时开启。
```
  - enable：register 注册服务控制开关，设为 true 时开启独立部署模式，设为 false 时关闭独立部署模式。
  - gameID：开发者自定义游戏ID。
  - svcName：gameServer 服务名，与 podName 组合作为该 gameServer 的唯一标识。 
  - podName：gameServer 实例名，与 svcName 组合作为该 gameServer 的唯一标识。
  - remoteHost：gameServer 注册服务地址，由独立部署方案提供。
  - remotePort：gameServer 注册服务端口，由独立部署方案提供。
  - localHost：gameServer 对外服务地址，该地址需要能被独立部署的引擎服务访问。
  - localPort：gameServer 对外服务端口。
```
- **roomConf**：gameServer 支持房间管理功能，开发者通过在 gameServer 里调用 API 接口可以实现房间创建、设置房间存活时常和房间删除等操作。**如需在本地调试时使用房间管理功能，则需要开启 roomConf 配置。**
```
  - enable：房间管理服务控制开关，设为 true 时开启房间管理服务，设为 false 时关闭房间管理服务。
  - svcName：gameServer 服务名，开启`matchvs debug`时在终端显示，与 podName 组合作为该 gameServer 的唯一标识。
  - podName：gameServer 实例名，开启`matchvs debug`时在终端显示，与 svcName 组合作为该 gameServer 的唯一标识。
  - remoteHost：gameServer 房间管理服务地址，开启`matchvs debug`时在终端显示。
  - remotePort：gameServer  房间管理服务端口，开启`matchvs debug`时在终端显示。
```

### golang 中的配置如下

- 打开 conf/config.toml.sample 文件可以看到如下内容：我们刚开始只需要关心 [Server] 下面的 Host内容。使用配置文件需要把 config.toml.sample 重新命名为 config.toml。

```toml
[Server]
Host = "0.0.0.0:36520"

[Log]
# Level's value can INFO、DEBUG、ERROR、WARNING, the default value is INFO
Level = "DEBUG"

[Register]
Enable = false
GameID = 123456
SvcName = "svc-abc"
PodName = "pod-abc"
RemoteHost = "192.168.8.1"
RemotePort = 9981
LocalHost = "192.168.8.2"
LocalPort = 30054

[RoomManage]
Enable = false
SvcName = "svc-abc"
PodName = "pod-abc"
RemoteHost = "192.168.8.1"
RemotePort = 9982
```

## 本地开发调试

### 打开命令行工具

- 打开命令行工具[下载地址](http://matchvs.com/serviceDownload) (ps : 在页面底部哦 ~)
- [命令行工具教程](https://doc.matchvs.com/Advanced/GameServerCMD)

为了方便开发者在开发过程中快速调试和定位问题，matchvs 命令行工具提供了本地调试命令`matchvs debug <GS_key>`。

![](http://imgs.matchvs.com/static/Doc-img/gameServer/gameServer04.png)

 **如上图所示，登录账号为官网注册的账号，密码为 控制台 gameServer 列表页顶部密码，选择对应的注册账号平台即可。**

`matchvs list `打印出创建的 gameServer 列表。

 `matchvs debug [GS key] ` 执行命令即可开启本地调试。

```shell
$ matchvs debug 1424769556baec5362f5b1513f7e1167
	==================== Develop config ====================
	SvcName:        svc-201994-0
	PodName:        deploy-201994-0-5f5d8785f8-9545j
	RemoteHost:     directory10.matchvs.com
	RemotePort:     9982
	
2018/11/13 15:38:01 [I] [proxy_manager.go:298] proxy removed: []
2018/11/13 15:38:01 [I] [proxy_manager.go:308] proxy added: [matchvs]
2018/11/13 15:38:01 [I] [proxy_manager.go:331] visitor removed: []
2018/11/13 15:38:01 [I] [proxy_manager.go:340] visitor added: []
2018/11/13 15:38:01 [I] [control.go:240] [37ff6c2d5cc54535] login to server success, get run id [37ff6c2d5cc54535], server udp port [0]
2018/11/13 15:38:01 [I] [control.go:165] [37ff6c2d5cc54535] [matchvs] start proxy success
```

`matchvs debug`命令在启动时与 Matchvs 服务建立代理连接。启动完成后，客户端发送给 gameServer 的消息将通过代理服务转发到开发者本地运行的 gameServer。同样的，gameServer 发送的消息也通过代理服务转发给客户端。

保留这个窗口，然后在另外一个窗口里启动 gameServer 服务，不同语言版本启动方式：

### Node.js

```shell
$ cd myGameServer
$ npm install
$ node main.js
```

本地搭建项目时，`npm install`失败或进度慢,可尝试使用`cnpm install`。

`npm install`和`cnpm install`混用时报错,建议删除`node_modules`, 重新使用一种方式安装。

### Java

使用IDE工具打开JavaGameServer工程。 `IDE`工具建议使用`IDEA`或者`Eclipse`

在APP.java文件中 path填入本地配置文件信息。配置文件参考下载文件中的Config.json文件。

```Java
public static void main(String[] args) {
	String[] path = new String[1];
	//本地调试时在此处填写自己config.Json的绝对路径,正式发布上线注释下行代码即可。
	//path[0] = "C:\\Users\\xing\\Desktop\\java-gameServe\\Config.json";
	try {
		Main.main(path);
	} catch (Exception e) {
		e.printStackTrace();
	}
}
```
### C # #

```
$ cd myGameServer/gameServer
$ cp -r conf bin/Debug/netcoreapp2.0/
$ dotnet run
```

这里使用`dotnet`工具启动 gameServer，实际使用时也可以使用 Visual Studio IDE 启动。

### Golang

假设在 windows 平台，在你的 golang 版本 gameServer 项目下面运行程序，比如我的工程是 gogs-demo ,目录结构如下：

```
┌ app/  		# 业务代码
├ conf/ 		# 配置文件
├ main.go 		# 程序入口
├ Dockerfile 	# docker部署，这个文件不要修改
├ Makefile 		# make 文件内容不能修改
└ gsmeta 		# 数据源文件内容不能修改
```

在 gogs-demo 目录下执行如下命令可以看到运行结果：

```shell
go run main.go
2018/11/30 17:35:01.21 game_service.go:55 ▶ INFO 001 log level to set [DEBUG]
2018/11/30 17:35:01.21 room_manager.go:51 ▶ INFO 002 room manager is disable
2018/11/30 17:35:01.21 dir_register.go:46 ▶ INFO 003 register is disable
2018/11/30 17:35:01.21 stream_server.go:66 ▶ INFO 004 game server start success [0.0.0.0:30523]
```

我们在 app/app.go 文件中 OnCreateRoom 和 OnJoinRoom 等业务处理函数都有输出日志，当有人加入房间会输出如下日志：

```shell
2018/11/30 17:37:31.02 app.go:33 ▶ DEBUG 005  OnCreateRoom map[mode:0 canWatch:2 createFlag:1 roomProperty: maxPlayer:3 state:1 createTime:1543570653 userID:1624685 userProfile:userProfile roomID:1708522620665729120]
2018/11/30 17:37:31.19 app.go:39 ▶ DEBUG 006  OnJoinRoom map[maxPlayers:3 checkins:[1624685] players:[1624685] roomID:1708522620665729120 userID:1624685 gameID:200773 userProfile:userProfile joinType:3]
```

但有人离开房间会输出如下日志：

```shell
2018/11/30 17:38:51.37 app.go:104 ▶ DEBUG 008  OnDeleteRoom map[gameID:200773 roomID:1708522620665729120]
2018/11/30 17:38:51.41 app.go:57 ▶ DEBUG 00a  OnLeaveRoom map[gameID:200773 userID:1624685 roomID:1708522620665729120]
```

> **注意：** 这里只是简单的描述了接口触发，然后以日志的形式处理接口消息，相关的游戏逻辑由开发者自己去处理。



**如果想要退出调试，在Matchvs命令行终端输入`quit`回车即可。**



## Demo 客户端与 gameServer 建立连接

**注意 ：**本地调试模式只支持测试环境，所以 Demo 客户端需要切换到测试环境。即 Demo 客户端 Matchvs `init`接口的 `channel`需要修改为 `Matchvs`，`platform`需要修改为`alpha`。  
本地调试和线上运行的区别，请参考[环境说明](../Advanced/EnvGuide)

运行Demo客户端，此时Matchvs 引擎就会将客户端的请求转发到开发者本地 gameServer 服务，开发者无须提交代码即可在本地调试代码。



## 上线正式环境

- 创建gameServer成功以后点击gameServer的名字，进入gameServer的详情页面，看到gameServer的仓库地址，使用git拉取仓库

![仓库地址](http://imgs.matchvs.com/static/Doc-img/gameServer/gameServer02.png)

- 拿到 git 仓库地址后，使用 git 命令将 gameServer 仓库克隆到本地：

```shell
$ git clone ssh://git@git.matchvs.com:3879/1424769556baec5362f5b1513f7e1167.git myGameServer
Cloning into 'myGameServer'...
warning: You appear to have cloned an empty repository.
```

**注意：因为这是一个新的 git 仓库，所以在克隆成功后 git 工具会发出一条警告，忽略该警告信息即可。**

账号密码在控制台 gameServer 列表页顶部，如下图 ：

![账号密码](http://imgs.matchvs.com/static/Doc-img/gameServer/gameServer03.png)



## 

将本地调试没问题的代码上传到 git 仓库，然后在控制台页面点击发布，再点击启动，此时 gameServer 已经成功运行在 Matchvs 托管云里。

**注意每次更新代码都需要重新发布一次。** 

**注意 在现网远程调试需要游戏需要先发布上线。可以前往 控制台-游戏列表 进行操作**

Java 版 gameServer 须将demo工程打成jar包，必须命名为`GameServer-Java.jar`(Jar包必须为可执行Jar包，入口类为App.java)

### golang 版本上线

还可以参考 golang 版的 [README](https://github.com/matchvs/gameServer-go)

在发布上线之前，请确认你的游戏状态是处于 **已商用** 状态 [前往查看状态](http://www.matchvs.com/manage/gameContentList) 。

发布后的环境，相对客户端SDK来说也就是release环境，所以 `login` 接口参数应该使用 `release`

在编译可执行程序之前一定要把 example 目录下的 Makefile 文件拷贝到你的工程 root目录下。

#### 1、编译工程生成 linux 平台的可执行文件

编译可执行文件分为两种方法

- 使用 make 命令编译：在 windows 下需要安装 make 工具。[windows make 工具下载](http://gnuwin32.sourceforge.net/packages/make.htm) 。
- 使用 go 命令编译：需要指定编译环境。

第一种：使用 make 编译可执行文件会在你的项目目录下生成 docker_build 文件夹，内容结构如下：

```
├ docker_build/ 		#docker 打包文件 文件名不能修改
	├ conf/
		config.toml		# 配置文件
	├ Dockerfile		# docker 文件
	├ gameserver_go	 	# linux下的可执行程序 必须为 gameserver_go
```

第二种：使用 go 命令编译必须制定编译参数，生成的可执行文件名称必须为 gameserver_go，使用如下命令编译：

```
CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -o gameserver_go
```

编程成功会在当前目录下面生成一个 gameserver_go 文件。然后手动新建 docker_build 文件夹，并且把 gameserver_go 、Dockerfile 、conf 文件夹 内容拷贝到 docker_build 文件夹目录下面。目录结构如上描述的。

#### 2、上传文件到仓库

编译好文件后，并且严格要求 docker_build 文件夹中的内容存在。使用 git 工具把工程代码提交到 matchvs 的gameServer 仓库中。提交同时要包含源代码。

> **注意** ：上传代码时一定要有 docker_build 文件和内容存在。并且文件中内容名称不能为其他的。

#### 3、官网发布

上传了编译好的工程文件，在官网对应的 gameServer 点击发布按钮。

发布成功后旁边的启动按钮就可以使用了，点击启动等待 gameServer 程序启动。启动成功后可以进入gameServer 中查看相关的日志啦。看到如下日志说明启动成功

```shell
2018/11/30 17:35:01.21 game_service.go:55 ▶ INFO 001 log level to set [DEBUG]
2018/11/30 17:35:01.21 room_manager.go:51 ▶ INFO 002 room manager is disable
2018/11/30 17:35:01.21 dir_register.go:46 ▶ INFO 003 register is disable
2018/11/30 17:35:01.21 stream_server.go:66 ▶ INFO 004 game server start success [0.0.0.0:xxxxx]
```

#### 提示：

发布代码时一定要需要的目录结构和内容如下：

```
┌ docker_build/ 		#docker 打包文件 文件名不能修改
	├ conf/
		config.toml		# 配置文件
	├ Dockerfile		# docker 文件
	├ gameserver_go	 	# linux下的可执行程序 必须为 gameserver_go
├ Makefile  # make 文件
└ gsmeta 		# 数据源文件内容不能修改
```

但是为了你的代码管理最好把你工程的所有源代码也一起提交到你的 gameServer git 仓库。



## 查看日志

gameServer 运行日志会输出到终端，以 Node.js 为例 ：

```shell
$ node main.js
[2018-09-10T18:43:26.012] [INFO] default - Game server started, listen on: 0.0.0.0:30381
[2018-09-10T18:47:40.370] [DEBUG] default - onCreateRoom: { userID: 0,
  gameID: 201994,
  roomID: '1679176138263367775',
  createExtInfo:
   { userID: 324973,
     userProfile: Uint8Array [ 117, 115, 101, 114, 80, 114, 111, 102, 105, 108, 101 ],
     roomID: '1679176138263367775',
     state: 1,
     maxPlayer: 3,
     mode: 0,
     canWatch: 0,
     roomProperty: Uint8Array [  ],
     createFlag: 1,
     createTime: '1536576460' } }
```

gameServer 详细使用[参考此处](../APIBasic/GameServerNodeJSBase) 。



## 常见问题

#### 1、 gameServer 与 matchvs.exe 无法连接问题。

解决方法：请修改配置文件中的端口号。

#### 2、本地调试用户名或者秘密不正确

解决方法：用户账号为手机号或者邮箱，密码不是官网的登录密码，是生成 官网中 gameServer控制台生成的密码。

#### 3、nodejs gameServer 发布后无效

解决方法：

1、去掉 node_modules 再上传发布一下，依赖模块添加到 package.json 里面，发布时会执行 npm install。

2、多操作几次发布。
#### gameServer git 地址是无效的 url  
解决方法：检查 url 是否多复制了空格
