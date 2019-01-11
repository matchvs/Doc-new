/*
Title: 快速入门-GameServer-Java
Sort: 5
*/

# Java-GameServer 开发文档

### 开发环境

需要java开发环境 下载JDK环境,参考[JDK安装与环境变量配置](https://jingyan.baidu.com/article/6dad5075d1dc40a123e36ea3.html) 

### 下载Java GameServer 框架代码

下载完并解压得到一个文件夹，

```shell
java-gameServer
├── demo         示例工程，修改工程中的App.Java文件即可
├── Config.json  配置文件
├── Dockerfile   docker文件不可修改
├── gsmeta       gameServer配置文件，不可修改。
├── Makefile     配置文件，不可修改。
├── README.md
```

使用IDE工具打开JavaGameServer工程。 `IDE`工具建议使用`IDEA`或者`Eclipse`

### 本地开发调试

下载Matchvs命令行工具 参考 [gameServer命令行工具](../Advanced/GameServerCMD)

为了方便开发者在开发过程中快速调试和定位问题，matchvs 命令行工具提供了本地调试命令matchvs debug <GS_key>

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

启动本地项目 就可以进入调试状态。

### Config配置

localHost IP默认为 “0.0.0.0” 即可，不建议修改。localPort：端口号可以从官网查询到：

![](http://imgs.matchvs.com/static/Doc-img/new-start/gameServerimg/java-GameServer1.png)

**注意** gameServer 只能使用系统分配的端口号，否则发布上线后将无法正常提供服务。

isRemote：本地调试时使用GameServer主动创建房间，销毁房间，设置房间存活时间需要开启。在使用 Matchvs 独立部署解决方案时也需要开启。

remoteHost：gameServer 注册服务地址，从Matchvs命令行工具获取。独立部署方案单独提供。

remotePort：gameServer 注册服务端口，从Matchvs命令行工具获取。独立部署方案单独提供。

gameID： 从官网控制台获取，gameServer独立部署模式下单独提供。

svcName： 从Matchvs命令行工具获取。独立部署方案单独提供。

podName： 从Matchvs命令行工具获取。独立部署方案单独提供。

![](http://imgs.matchvs.com/static/Doc-img/new-start/gameServerimg/java-GameServer3.png)

**注意** 本地调试时将对应的值填入配置文件，isRemote在上传商用环境时候设置为false。

### Demo 客户端与 gameServer 建立连接

本地调试模式只支持测试环境，所以 Demo 客户端需要切换到测试环境，即 Demo 配置文件中的 `channel`需要修改为 `Matchvs`，`platform`需要修改为`alpha`, `GameID` 需要和Config.json文件中的`GameID`一致。

### 查看日志

日志必须使用提供的Logger。

```Java
Logger log = LoggerFactory.getLogger("App");
```

gameServer 本地调试运行日志在IDE的输出终端查看，线上调试再官网的控制台查看。

![](http://imgs.matchvs.com/static/Doc-img/new-start/gameServerimg/java-GameServer2.png)

### 上线

代码编写完成后，将demo工程打成jar包，必须命名为`GameServer-Java.jar`(Jar包必须为可执行Jar包，入口类为App.java)，上传到git仓库。

**注意** GameServer在线上环境读取的是`GameServer-Java.jar`同级目录下的Config.json文件的配置。

git仓库文件列表为

```shell
java-gameServer
├── demo
├── Config.json   
├── Dockerfile
├── gsmeta
├── GameServer-Java.jar
├── Makefile
├── README.md
```

从控制台启动。GameServer现网启动需要游戏转商用后才可以 转商用参考  [Matchvs环境说明](../Advanced/EnvGuide)




