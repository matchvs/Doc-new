## 准备
### HTTPS证书
需准备一个数字证书：xxx.crt和xxx.key。

可参考网上教程：https://blog.csdn.net/luckymama/article/details/64904729

### 部署机器
如果要运行 JavaScript 版的 GameServer，需要有 node.js 环境。

### 部署目标机器
部署目标机器要求 root 用户可以 ssh 远程连接。

## 工具简介
前往Matchvs官网[下载中心](http://www.matchvs.com/serviceDownload)下载独立部署工具，以独立部署工具1.5.6版本（standalone_tools_v1.5.6.zip）为例， 解压后文件结构如下：
```
.
├── bin
│   └── matchvs_tool
├── CHANGELOG.md
├── conf
│   ├── cp.toml.sample
│   ├── scale_add.toml.sample
│   ├── scale_delete.toml.sample
│   └── tool.toml
├── data
│   └── get-pip.py
└── test
    ├── benchmark
    │   └── bin
    │       └── minion_benchmark
    └── frigate
        ├── bin
        │   └── frigate
        └── conf
            └── frigate.toml
```

**进入到 bin 目录**，执行`./matchvs_tool -h`，可查看部署工具简介，包括所有命令的描述及使用。

![](http://imgs.matchvs.com/static/deploy/1.png)

###  版本
执行`./matchvs_tool version`，可查看工具的版本。

![](http://imgs.matchvs.com/static/deploy/18.png)

## 部署

只需四步，即可完成Matchvs服务的整套部署。

![](http://imgs.matchvs.com/static/deploy/2.png)
### 第一步：修改配置文件
将`/conf/cp.toml.sample`复制一份为`cp.toml`，将`cp.toml`按如下规则进行修改：
#### 配置许可证
```
License     = "test"
```

将test替换为取得的许可证字符串，目前许可证字符串需进入QQ群（450335262）向管理员索取。

####  配置域名

将自己的域名做相应的替换，**其中Dashboard、Grafana为可选项，您可以跳过这两项的配置**。

```
Wss         = "testgateway.matchvs.com"

Dashboard   = "testdashboard.matchvs.com" 

Grafana     = "testgrafana.matchvs.com"

```

####  配置证书路径
```
CrtFile     = "/data/standalone_tools/data/matchvs.com.crt"

KeyFile     = "/data/standalone_tools/data/matchvs.com.key"

```

将数字证书的路径改为自己许可证所在的路径即可

####  配置用户名和密码

**配置给 dashboard 和 grafana 使用，非必填项，可以跳过此步。**

```
Username    = "test"

Password    = "test"

```

####  配置部署目标机器
```
[[Machines]]

  Host =      "192.168.9.5"

  ExternalIP  = "192.168.9.5"

  SSHPort     = "22"

  RootPWD     = "haha"

```


配置root用户密码与端口，机器IP。

Host 为部署目标机器的内网 IP，ExternalIP 为部署目标机器的外网 IP。

SSHPort 为部署目标机器的 SSH 的服务端口，RootPWD 为部署目标机器的 root 用户密码。**所有机器的SSHPort和RootPWD应该一致**

如果要部署多台，复制此项多份，更改相应参数，配置文件中有示例。

### 第二步：查看所有引擎版本
执行`./matchvs_tool deploy versions`，显示所有有效服务版本

![](http://imgs.matchvs.com/static/deploy/19.png)

### 第三步：验证指定版本
如果我们想要验证 3.8.0.4 版本在当前配置的目标机器上是否可以安装，执行`./matchvs_tool deploy check 3.8.0.4`

![](http://imgs.matchvs.com/static/deploy/20.png)

此过程会安装一些python包，如果提示失败，则手动安装即可。

`pip install 安装失败的包名`

### 第四步：安装指定版本
执行`./matchvs_tool deploy install 3.8.0.4`，安装指定版本，3.8.0.4 为期望安装的版本号。

![](http://imgs.matchvs.com/static/deploy/21.png)

**注意：此过程时间比较长，会依赖网络拉取镜像。**

部署成功如下图

![](http://imgs.matchvs.com/static/deploy/22.png)
安装成功后，服务就已启动运行。

### 查看部署结果
执行`./matchvs_tool deploy info`，可查看部署结果及运行服务。

![](http://imgs.matchvs.com/static/deploy/3.png)

### 停止引擎服务
执行`./matchvs_tool deploy stop`，可以停止引擎服务

![](http://imgs.matchvs.com/static/deploy/15.png)
**注意：停止过程可能要等待一段时间。**

### 启动引擎服务
执行`./matchvs_tool deploy start`，可以启动引擎服务。

![](http://imgs.matchvs.com/static/deploy/16.png)



## 游戏信息

使用 Matchvs 独立部署，需要配置相关的游戏信息。

这些游戏信息在客户端 SDK 接入时会用到。

Matchvs 会区分游戏进行游戏人数、房间数等数据统计。

![](http://imgs.matchvs.com/static/deploy/4.png)

### 添加游戏
假设我们现在要添加一个游戏信息：

```
gameID：1000，appKey：appkey123abc，appSecret：appsecret123abc
```

执行`./matchvs_tool game add 1000 appkey123abc appsecret123abc`  

![](http://imgs.matchvs.com/static/deploy/5.png)

### 查看游戏列表
执行`./matchvs_tool game list`，可以查看已添加的游戏信息列表。

![](http://imgs.matchvs.com/static/deploy/6.png)

### 删除游戏
假设我们现在删除gameID为1000的游戏，执行`./matchvs_tool game delete 1000`

![](http://imgs.matchvs.com/static/deploy/7.png)

## token
![](http://imgs.matchvs.com/static/deploy/8.png)
配置游戏登录时是否要进行token验证，默认是不需要。

### 添加游戏token
假设我们现在要给gameID为1000的游戏添加token验证，URL为http://www.matchvs.com/。

执行`./matchvs_tool token add 1000 1 http://www.matchvs.com/`

![](http://imgs.matchvs.com/static/deploy/9.png)

### 查询游戏token
执行`./matchvs_tool token list`，查询所有游戏token



![](http://imgs.matchvs.com/static/deploy/10.png)

### 删除游戏token
假设我们现在要删除gameID为1000的游戏的token设置，执行`./matchvs_tool token delete 1000`

![](http://imgs.matchvs.com/static/deploy/11.png)

## 测试服务

在部署完成后，我们可以执行 `./matchvs_tool test` 来检测服务是否正常部署。



![](http://imgs.matchvs.com/static/deploy/12.png)

### gameServer
如果想用测试工具验证 gameServer ，需要先启动 gameServer。

gameServer框架可以前往 Matchvs 官网[下载中心](http://www.matchvs.com/serviceDownload)下载。

#### 配置
配置文件`gameserver_nodejs/framework/conf/config.json`中，内容如下：
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
    }
}
```
gameID为配置的游戏ID，remoteHost为部署目标机器的IP地址，localHost为gameServer所在的IP地址，只需要修改此三项为对应值。

#### 安装依赖包
```sh
npm install
```

#### 启动gameServer
![](http://imgs.matchvs.com/static/deploy/14.png)

### frigate
通过此命令可以验证整个部署服务是否正常运行。建议添加一个专用的game，且不需要token验证。

![](http://imgs.matchvs.com/static/deploy/13.png)

#### 启动测试
假设我们测试的gameID为1000（默认只发送到客户端，走TCP协议，次数为1）

执行`./matchvs_tool test frigate 1000`

![](http://imgs.matchvs.com/static/deploy/17.png)
所有调用都成功，表明服务已全部正常启动。

## 独立部署控制台

为方便开发者查看游戏数据，以及许可证信息，Matchvs 提供了独立部署控制台。

执行`./matchvs_tool deploy info` ，可查看控制台的链接（**matchvsadmin**）



![](http://imgs.matchvs.com/static/deploy/26.png)

复制 matchvsadmin 后的URL，如图为'http://192.168.8.21:8081'，在浏览器打开，如下：



![](http://imgs.matchvs.com/static/deploy/28.png)

```
初始密码为：
账号：matchvs
密码：matchvs
```

登录后，即可查看各个游戏下的在线玩家数，房间数等数据。



![](http://imgs.matchvs.com/static/deploy/29.png)

## 升降级

当服务有更新时，可以进行升级。如果想使用某些旧版本，也可以降级。

![](http://imgs.matchvs.com/static/deploy/100.png)

### 第一步：查看所有可升降级版本

执行`./matchvs_tool grade versions`，显示当前已安装的版本可升级到哪些版本和可降级到哪些版本。
![](http://imgs.matchvs.com/static/deploy/101.png)

执行升降级时，指定版本只能是能查询到的，否则会提示失败。

### 第二步：修改配置文件

**由于升降级需要更新许可证，请先去官网刷新许可证**

将刷新到的许可证更新到`cp.toml`的`License`字段。

### 第三步：升级

假设，查询到当前版本可升级到 3.8.0.5，执行`./matchvs_tool grade upgrade 3.8.0.5`

### 第四步：降级

假设，查询到当前版本可降级到 3.8.0.3，执行`./matchvs_tool grade degrade 3.8.0.3`

## 扩容与缩容

当服务负载有变动时，可以做相应的扩容和缩容，来更有效地提供服务。

![](http://imgs.matchvs.com/static/deploy/102.png)

**扩容与缩容时，不需要刷新许可证**

### 扩容

#### 第一步：修改配置文件

将`/conf/scale_add.toml.sample`复制一份`/conf/scale_add.toml`，修改如下

```
[[Machines]]

  Host =      "192.168.9.5"

  ExternalIP  = "192.168.9.5"

  SSHPort     = "22"

  RootPWD     = "haha"

```

配置root用户密码与端口，机器IP。

Host 为部署目标机器的内网 IP，ExternalIP 为部署目标机器的外网 IP。

SSHPort 为部署目标机器的 SSH 的服务端口，RootPWD 为部署目标机器的 root 用户密码，**需与`/conf/tool.toml`中的机器配置一致**。

如果要扩容多台，复制此项多份，更改相应参数，配置文件中有示例。

#### 第二步：执行扩容

执行`./matchvs_tool scale add`即可

### 缩容

#### 第一步：修改配置文件

将`/conf/scale_delete.toml.sample`复制一份`/conf/scale_delete.toml`。

假设，要将外网IP为`192.168.9.5`的机器缩容掉，修改配置如下

```
Hosts = ["192.168.9.5"]
```

假设，要将外网IP为`192.168.9.5`和`192.168.9.6`的机器缩容掉，修改配置如下

```
Hosts = ["192.168.9.5", "192.168.9.6"]
```

缩容更多台以此类推。

**`/conf/cp.toml`中的首台机器是不能被缩容的**

#### 第二步：执行缩容

执行`./matchvs_tool scale delete`即可

## 常见问题

### 未启gameServer而发送到gameServer

执行`./matchvs_tool test frigate 1000 -g 1`，发送至gameServer
当未启动gameServer，而要发送到gameServer，会出现发送出错，错误码为521，如下图

![](http://imgs.matchvs.com/static/deploy/24.png)