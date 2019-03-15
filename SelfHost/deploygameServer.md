/*
Title: 自托管部署gameServer
Sort: 3
*/

自托管下，gameServer 可以放至任一部署了引擎的服务器上。

gameServer 框架可以前往 Matchvs 官网[下载中心](http://www.Matchvs.com/serviceDownload)下载。

## 环境准备

### Node.js

支持**Node.js 8.0及以上版本**。

### Java

需要java开发环境 下载JDK环境,支持JDK1.7以上

### C# #

支持 Visual Studio 2017 及以上版本，另外也支持 .NET Core 命令行接口 (CLI) 工具。

### Golang

使用 golang 版本的 gameServer 须知：

- 需要在本地安装好 golang 运行环境。如果还没有配置好 golang 环境的请先配置好。以下内容教程是默认你已经配置好并且熟悉 golang 开发环境。

## 修改配置

以 JavaScript 版的 gameServer 为例，配置文件`gameserver_nodejs/framework/conf/config.json`中，内容如下：

```json
{
    "addr": "0.0.0.0:30054",
    "log": {
        "appenders": {
            "console": {
                "type": "console"
            }
        },
        "categories": {
            "default": {
                "appenders": [ "console" ], 
                "level": "debug"
            }
        }
    },
    "register": {
        "enable": true,
        "gameID": 1000,
        "svcName": "svc-abc",
        "podName": "pod-abc",
        "remoteHost": "192.168.8.127",
        "remotePort": 9981,
        "localHost": "192.168.8.121",
        "localPort": 30054
    },
    "roomConf": {
        "enable": false,
        "svcName": "svc-abc",
        "podName": "pod-abc",
        "remoteHost": "192.168.8.1",
        "remotePort": 9982
    },
}
```

**addr**：gameServer服务监听地址。**IP默认为 “0.0.0.0” 即可，不建议修改**。

- **logLevel**：日志配置。gameServer 使用 [log4js](https://www.npmjs.com/package/log4js) 作为日志管理框架，如需自定义 log 输出可在了解 log4js 的前提下自行修改配置文件。

- **register**：独立部署配置，仅在使用 Matchvs 独立部署解决方案时开启。

  ```
  - enable：register 注册服务控制开关，设为 true 时开启独立部署模式，设为 false 时关闭独立部署模式。独立部署时应设为 true。
  - gameID：开发者自定义游戏ID，须保证 gameID 唯一性。
  - svcName：gameServer 服务名，由开发者自定义，与 podName 组合作为该 gameServer 的唯一标识。 
  - podName：gameServer 实例名，由开发者自定义，与 svcName 组合作为该 gameServer 的唯一标识。
  - remoteHost：gameServer 注册服务地址，由独立部署方案提供，使用独立部署工具执行 ./matchvs_tool deploy info ，显示的 gs_regit IP 即为注册服务地址。
  - remotePort：端口号固定为9981，不需要修改。
  - localHost：gameServer 所在的服务器IP地址。部署前需要配置防火墙，允许独立部署机器访问该IP。
  - localPort：gameServer 所开放的端口号，由开发者自己定义。部署前需要配置防火墙，允许独立部署机器访问该端口。
  ```

- roomConf：gameServer 支持房间管理功能，开发者通过在 gameServer 里调用 API 接口可以实现房间创建、设置房间存活时常和房间删除等操作。

  ```
  - enable：房间管理服务控制开关，设为 true 时开启房间管理服务，设为 false 时关闭房间管理服务。
  - svcName：gameServer 服务名，由开发者自定义，与 podName 组合作为该 gameServer 的唯一标识。 
  - podName：gameServer 实例名，由开发者自定义，与 svcName 组合作为该 gameServer 的唯一标识。
  - remoteHost：gameServer 注册服务地址，由独立部署方案提供，使用独立部署工具执行 ./matchvs_tool deploy info ，显示的 gs_regit IP 即为注册服务地址。
  - remotePort：端口号固定为9982，不需要修改。
  ```

## 启动服务

#### Node.js

进入项目目录，执行如下命令，启动服务即可。

```sh
$ npm install 	# 只需在第一次启动时执行
$ node main.js
```

本地搭建项目时，`npm install`失败或进度慢,可尝试使用`cnpm install`。

`npm install`和`cnpm install`混用时报错,建议删除`node_modules`, 重新使用一种方式安装。

#### Java

使用IDE工具打开JavaGameServer工程。 `IDE`工具建议使用`IDEA`或者`Eclipse`

在APP.java文件中 path填入本地配置文件信息。配置文件参考下载文件中的Config.json文件。

```java
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

#### C# #

进入项目路径下，执行如下命令，启动服务即可。

```sh
$ cd myGameServer/gameServer
$ cp -r conf bin/Debug/netcoreapp2.0/
$ dotnet run
```

这里使用`dotnet`工具启动 gameServer，实际使用时也可以使用 Visual Studio IDE 启动。

####  Golang

在项目目录下执行如下命令可以看到运行结果：

```sh
go run main.go
2018/11/30 17:35:01.21 game_service.go:55 ▶ INFO 001 log level to set [DEBUG]
2018/11/30 17:35:01.21 room_manager.go:51 ▶ INFO 002 room manager is disable
2018/11/30 17:35:01.21 dir_register.go:46 ▶ INFO 003 register is disable
2018/11/30 17:35:01.21 stream_server.go:66 ▶ INFO 004 game server start success [0.0.0.0:30523]
```