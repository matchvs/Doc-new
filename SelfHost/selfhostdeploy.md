/*
Title: 自托管安装教程
Sort: 2
*/
## 安装准备

- 部署工具(Standalone_tools) [下载地址]() ,注意在页面底部
- 许可证(license)，在 控制台 - 自托管页面[免费获取]()，许可证信息点击“查看”按钮即可获取。
- 一台或多台 `干净的`（**没有部署过任何服务**） Centos 7 服务器，**提供root账号与密码**,机器需要具备合适外网访问权限，防火墙配置请参考章节--“服务防火墙配置”说明。
- 域名、对应的ssl证书（证书为可选项目，如果不使用wss连接可以不用准备）



## 部署引擎

仅需 **4 步** 即可完成引擎服务的部署 ：

**注意**：使用自托管**无需**在官网创建游戏和gameServer。

### step 1 修改配置文件

将`./conf/cp.toml.sample`复制一份为`cp.toml`，将`cp.toml`按如下规则进行修改

**配置Matchvs授权许可证**

```
License     = "test"
```

将test替换为您所实际获得的许可证字符串, 在控制台-自托管页面可查看许可证信息。

**配置域名**

将自己的域名做相应的替换，**其中Dashboard、Grafana为可选项，您可以跳过这两项的配置**。

```
Wss         = "gateway.mydomain.com"
Dashboard   = "admin.mydomain.com" 
Grafana     = "monitor.mydomain.com"
```

**配置证书路径**

**可选 使用wss连接就必须填写ssl证书，只使用ws连接，请填空字符串。**
```
CrtFile     = "/data/standalone_tools/data/mydomain.crt"
KeyFile     = "/data/standalone_tools/data/mydomain.key"
```

将数字证书的路径改为自己证书所在的路径即可。

**配置用户名和密码**

**配置给 dashboard 和 grafana 使用，非必填项，可以跳过此步。**

```
Username    = "test"
Password    = "test"
```

**配置部署目标机器**

```
[[Machines]]
  Host        = "192.168.9.5"
  ExternalIP  = "192.168.9.5"
  SSHPort     = "22"
  RootPWD     = "haha"
```

配置目标机器的IP及root账号信息，其中：

Host 为部署目标机器的内网IP。
ExternalIP 为部署目标机器的外网 IP。
SSHPort 为部署目标机器的 SSH 的服务端口。
RootPWD 为部署目标机器的 root 用户密码。
注意：**所有机器的SSHPort和RootPWD应该一致**

如果要部署多台，复制此项多份，更改相应参数，配置文件中有示例。

### step 2 查看引擎版本

在 bin 目录下,执行`./matchvs_tool deploy versions`，显示所有有效服务版本

![](http://imgs.Matchvs.com/static/deploy/19.png)

### Step3 验证指定版本

如果我们想要验证 3.8.0.4 版本在当前配置的目标机器上是否可以安装，执行`./matchvs_tool deploy check 3.8.0.4`

![](http://imgs.Matchvs.com/static/deploy/20.png)

### Step4 安装指定版本

执行`./matchvs_tool deploy install 3.8.0.4`，安装指定版本，3.8.0.4 为期望安装的版本号。

![](http://imgs.Matchvs.com/static/deploy/21.png)

**注意：此过程时间比较长，会依赖网络带宽与机器性能等**
**当下载完成后会进行脚本安装，脚本执行时间约为20-60分钟左右，依赖机器性能与网络带宽**

![](http://imgs.Matchvs.com/static/deploy/20190222.png)

**部署阶段显示的红色或者黄色告警不必理会，只要程序没有退出，表示这是正常的部署行为。证书路径不存在，那是因为您没有填wss证书，不影响使用ws来连接Matchvs服务。**
**显示左边的光标可以让您知道程序依然还在进行中。**

**部署成功**如下图

![](http://imgs.Matchvs.com/static/deploy/2019022202.png)

**安装成功后，服务已自动启动运行。**

## 部署验证

部署完成后，我们可以开始验证整套服务是否良好运行。验证仅需2步：

### step 1 添加游戏

```sh
Usage:
  matchvs_tool game add gameID gameName appKey appSecret
```
* gameID为4字节整数
* gameName为字符串，最长为50个字符
* appKey为字符串，最长为120个字符
* appSecret为字符串，最长为120个字符

假设我们现在要添加一个游戏信息：
```
gameID：1000，gameName：斗地主，appKey：appkey123abc，appSecret：appsecret123abc
```

执行`./matchvs_tool game add 1000 斗地主 appkey123abc appsecret123abc`  

![](http://imgs.Matchvs.com/static/deploy/5.png)

### step 2 执行验证

在部署完成后，我们可以执行 `./matchvs_tool test` 来检测服务是否正常部署。

![](http://imgs.Matchvs.com/static/deploy/12.png)



通过`./matchvs_tool test frigate` 命令可以验证整个部署服务是否正常运行。

![](http://imgs.Matchvs.com/static/deploy/13.png)

假设我们测试的gameID为1000（默认只发送到客户端，走TCP协议，次数为1）

执行`./matchvs_tool test frigate 1000`，执行后等待几秒后输出结果，期间不要退出执行。

![](http://imgs.Matchvs.com/static/deploy/17.png)
所有调用都成功，表明服务已全部正常启动。

## Demo 运行

Demo [直接体验地址](http://demo.matchvs.com/demo-creator/) ，[github 地址](https://github.com/matchvs/demo-creator)

进入Demo后，点击首页右下角的`独立部署`，进入独立部署模式，填写基本信息：

![](http://imgs.Matchvs.com/static/deploy/selfhostDemo.png)

![](http://imgs.Matchvs.com/static/deploy/selfhostDemo1.png)



- EndPoint 为部署引擎时配置的域名信息，也可以通过在服务器执行`./matchvs_tool deploy info`查看 proxy 信息。
- gameID、key、secret为添加的游戏信息
- userID 和 token 为自定义，**多个终端调试时，userID不可以重复**

填写后点击确定即可进入游戏。

## 游戏数据查看

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

## 常见问题

### 521 错误

执行`./matchvs_tool test frigate 1000 -g 1`，发送至gameServer
当未启动gameServer，但是设置了frigate需要发送到gameServer，会出现发送出错，错误码为521，如下图

![](http://imgs.Matchvs.com/static/deploy/24.png)

解决办法：需要去掉`-g`参数，或者部署并启动对应的gameServer之后再重新测试。

## 更多教程

- 部署 gameServer 
- 修改 ssl 证书
- 用户登录验证
- 更多部署命令手册，包含扩容、缩容、版本升级等。
- 防火墙配置