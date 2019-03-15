/*
Title: 部署工具教程
Sort:5
*/

## 查看部署结果

执行`./matchvs_tool deploy info`，可查看部署结果及运行服务。

![](http://imgs.Matchvs.com/static/deploy/3.png)

## 查看许可证信息

执行`./matchvs_tool license info`，可查看当前许可证的资源信息。示例如下：

```sh
$ ./matchvs_tool license info
+----------+----------------------------------------------------------------------+
| 标题     | 内容                                                                 |
+----------+----------------------------------------------------------------------+
| 许可证   | 110000000001000000005b6bca5039c514723c81d35200818bc09c924e24b5f61b8e |
| 有效期至 | 2028-01-01T00:00:00+08:00                                            |
| 机器个数 | 1                                                                    |
| 游戏个数 | 2                                                                    |
| CCU 数量 | 20                                                                   |
+----------+----------------------------------------------------------------------+
```

## 停止引擎服务

执行`./matchvs_tool deploy stop`，可以停止引擎服务

![](http://imgs.Matchvs.com/static/deploy/15.png)

**注意：停止过程可能要等待一段时间。**

## 启动引擎服务

执行`./matchvs_tool deploy start`，可以启动引擎服务。

![](http://imgs.Matchvs.com/static/deploy/16.png)

## 查看游戏配置列表

执行`./matchvs_tool game list`，可以查看已添加的游戏信息列表。

![](http://imgs.Matchvs.com/static/deploy/6.png)

## 删除游戏信息

如果前面步骤输入了错误的信息，或者游戏不再运营，可以通过命令删除不需要的游戏信息。

假设我们现在删除gameID为1000的游戏，执行`./matchvs_tool game delete 1000`

![](http://imgs.Matchvs.com/static/deploy/7.png)

## 服务升降级

当 Matchvs 服务有更新时，可以进行升级。如果想使用某些旧版本，也可以降级。

> 进程版不支持升降级，没有grade子命令。

![](http://imgs.Matchvs.com/static/deploy/100.png)

#### 查看可升降级版本

执行`./matchvs_tool grade versions`，显示当前已安装的版本可升级到哪些版本和可降级到哪些版本。

![](http://imgs.Matchvs.com/static/deploy/101.png)

执行升降级时，指定版本只能是能查询到的，否则会提示失败。

#### 修改配置

**由于升降级需要更新许可证，请先去官网刷新许可证**

将刷新到的许可证更新到`cp.toml`的`License`字段。

#### 升级服务版本

假设，查询到当前版本可升级到 3.8.0.5，执行`./matchvs_tool grade upgrade 3.8.0.5`

#### 降级服务版本

假设，查询到当前版本可降级到 3.8.0.3，执行`./matchvs_tool grade degrade 3.8.0.3`

## 服务扩容与缩容

当服务负载有变动时，可以做相应的扩容和缩容，来更有效地提供服务。

> 进程版不支持扩容与缩容，没有scale子命令。

![](http://imgs.Matchvs.com/static/deploy/102.png)

**扩容与缩容时，不需要刷新许可证**

#### 服务扩容

##### 修改配置文件

将`/conf/scale_add.toml.sample`复制一份`/conf/scale_add.toml`，修改如下

```
[[Machines]]
  Host        = "192.168.9.5"
  ExternalIP  = "192.168.9.5"
  SSHPort     = "22"
  RootPWD     = "haha"

```

配置root用户密码与端口，机器IP。

Host 为部署目标机器的内网 IP；
ExternalIP 为部署目标机器的外网 IP；
SSHPort 为部署目标机器的 SSH 的服务端口；
RootPWD 为部署目标机器的 root 用户密码；
**需与`/conf/tool.toml`中的机器配置一致**。

如果要扩容多台，复制此项多份，更改相应参数，配置文件中有示例。

##### 执行扩容

执行`./matchvs_tool scale add`即可

#### 服务缩容

##### 修改配置文件

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

##### 执行缩容

执行`./matchvs_tool scale delete`即可
