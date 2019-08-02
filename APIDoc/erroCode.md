/*
Title: 错误码说明
Sort: 100
*/

## 错误码 

Matchvs SDK 错误码分为两个部分。用于检查函数入参的合法性,如空值,越界,状态异常

- 第一部分：MatchvsEngine 类型接口调用的时候返回值。由客户端返回
- 第二部分：MatchvsResponse 回调接口中的 status 和 errorResponse 中的 code 值。由服务端异步返回
### MatchvsEngine 类型接口返回值

| 返回码 | 含义                                  |
| ------ | ------------------------------------- |
| 0      | 成功                                  |
| -1     | 失败                                  |
| -2     | 未初始化                              |
| -3     | 正在初始化                            |
| -4     | 未登录                                |
| -5     | 正在登录                     |
| -6     | 未加入房间                            |
| -7     | 正在创建或者进入房间                  |
| -8     | 已经在房间中                           |
| -9     | 正在重连 	|
| -10    | 正在登出                                |
| -12    | 正在加入观战房间                    |
| -13    | 队伍正在匹配中                   |
| -14    | 正在观战离开房间                   |
| -11    | 正在离开房间                            |
| -20    | 1 <maxPlayer超出范围 ，maxPlayer须≤100 或 confirms 和 cancles 不能都为空，frameRate 不能超过 20，不能小于0 |
| -21    | 接口调用中包含 cpProto参数或者 userProfile 参数的长度过长，一般限制 1024。 |
| -23    | msgType 非法                           |
| -24    | desttype 非法                          |
| -25    | channel 非法，请检查是否正确填写为 “Matchvs”             |
| -26    | platform 非法，请检查是否正确填写为 “alpha” 或 “release” |
| -27    | timeout 超出范围  0=< timeout <=600 |
| -29    | nodeID>0或者不存在该nodeID对应的多节点 |
| -30    | 设置的 rType 值与当前模式冲突。     |



### MatchvsResponse 回调接口 status

Matchvs SDK 一些附带 status 参数的回调接口中具体的参数值可参考下面表格的说明。


**注意** Matchvs相关的异常信息也可通过 errorResponse 接口统一获取。

#### 大厅

| 错误码 | 含义                                                         | 场景                 |
| ------ | ------------------------------------------------------------ | -------------------- |
| 1001   | 网络错误                                                     | 所有API              |
| 200    | 成功                                                         | 所有API              |
| 201    | 重连到大厅，没有进入房间                                     | reconnect            |
| 400    | 请求无法被服务器理解,解包异常                                 | 任何API              |
| 401    | 无效 appKey                                                  | login                |
| 402    | 应用校验失败，确认是否在未上线时用了release环境，并检查gameID、appKey 和 secret | login                |
| 403    | 访问禁止，该用户多端登录。                                   | login                |
| 404    | userID错误                                                   | 所有API              |
| 405    | 请求的游戏ID与登录时的游戏ID不一致                           | 所有API              |
| 500    | 内部错误                                                     | 所有API              |
| 502    | 服务停止，许可证失效 或者账号欠费                            | login                |
| 503    | ccu 超出限额                                                 | login                |
| 504    | 服务停止，许可证失效 或者账号欠费                            | createRoom、joinRoom |
| 702    | 参数异常                                                     | 所有API              |
| 703    | （独立部署）游戏个数超限                                     | login                |
| 704    | （独立部署）机器个数超限                                     | login                |

#### 房间

| 错误码 | 含义                                  | 场景                    |
| ------ | ------------------------------------- | ----------------------- |
| 200    | 成功                                  | 房间相关API         |
| 400    | 同一个UserID多处登录,先登录的一方在joinroom时触发         | joinRoom         |
| 401    | gameID 错误                           | 房间相关API         |
| 402    | roomID 错误,或检查调用 leaveRoom 时是否在房间内                           | 房间相关API         |
| 403    | userID 错误                           | 房间相关API         |
| 404    | 未找到房间消息的推送目标用户          | 房间相关API         |
| 405    | 房间已满                              | joinRoom                |
| 406    | 房间已停止加人                        | joinRoom                |
| 407    | 超过总人数（观战人数+玩家人数）的限制 | joinRoom                |
| 408    | 超过观战数据延迟时间限制              | createRoom              |
| 409    | 房间不存在                            | 房间相关API         |
| 410    | 用户不存在                            | 房间相关API         |
| 411    | 请求用户不在房间内                    | 房间相关API         |
| 412    | 目标用户不在房间内                    | kickPlayer              |
| 413    | TTL超过最大限制                       | createRoom              |
| 414    | 房间为空                              |                         |
| 415    | 房间不为空                            | destroyRoom             |
| 416    | 不允许自己踢自己                      | kickRoomPlayer          |
| 439    | 断线重连超时时间错误                  | setRoomReconnectTimeout |
#### 队伍
| 错误码 | 含义                                                         | 场景                            |
| ------ | ------------------------------------------------------------ | ------------------------------- |
| 200    | 成功                                                         | 队伍相关API                     |
| 417    | teamInfo 错误                                                | createTeam                      |
| 418    | playerInfo 错误                                              | createTeam、joinTeam            |
| 419    | 小队不存在                                                   | 队伍相关API                     |
| 420    | 小队密码错误                                                 | joinTeam                        |
| 421    | 小队容量超标                                                 | joinTeam                        |
| 422    | 小队匹配超时                                                 |                                 |
| 423    | 队伍数据错误                                                 | teamMatch                       |
| 424    | 队伍正在匹配                                                 | joinTeam、teamMatch             |
| 425    | 匹配超时时间错误                                             | teamMatch                       |
| 426    | 权重错误                                                     | teamMatch                       |
| 427    | 权重范围错误                                                 | teamMatch                       |
| 428    | 匹配取消                                                     |                                 |
| 429    | 玩家不再指定的小队中                                         | 队伍相关API                     |
| 430    | 玩家已经在房间里面了(不能创建小队，也不能有加入任何小队的操作) | createTeam、joinTeam、teamMatch |
| 431    | 小队已经匹配成功并已加入到了某个房间内（当前小队不允许其他玩家加入） | joinTeam                        |
| 432    | 小队不在匹配状态                                             | cancelMatch                     |
| 433    | 小队消息超过长度                                             | sendTeamEvent                   |
| 434    | 小队消息msgType不合法                                        | sendTeamEvent                   |
| 435    | 小队消息dstType不合法                                        | sendTeamEvent                   |
| 436    | kick小队成员携带数据过大                                     | kickTeamPlayer                  |
| 437    | 被kick小队成员仍然在小队                                     | kickTeamPlayer                  |
| 438    | 小队消息超过限制频率                                         | sendTeamEvent                   |
| 439    | 断线重连超时时间错误                                         | setTeamReconnectTimeout         |

#### 对战

| 错误码 | 含义                                    | 场景                 |
| ------ | ---------------------------------------------- | ----------- |
| 500    | 帧率设置错误,非10的倍数                    | setFrameRate |
| 501    | 重复进入对战房间                    | createRoom、joinRoom |
| 502    | checkin 凭证 key 错误             | checkin                 |
| 503    | 对战房间已满                          | createRoom、joinRoom    |
| 504    | 对战房间未开放                        | joinRoom、checkin       |
| 505    | 对战房间已经存在                      | createRoom、joinRoom    |
| 506    | 创建对战房间失败                      | createRoom、joinRoom    |
| 507    | 对战房间不存在 | 对战相关API |
| 508    | 用户不在对战房间 | 对战相关API |
| 509    | 请求用户不在对战房间 | sendEvent、sendEventEx |
| 510    | checkin bookID 错误 | checkin |
| 511    | 通知内容为空 | checkin |
| 512    | 打开对战房间失败 | createRoom、joinRoom |
| 513    | 心跳连接ID发生变动 | heartbeat |
| 514    | checkin 参数错误 | checkin |
| 515    | 只有未退出、且checkIn的玩家才能发送数据 | sendEvent、sendEventEx |
| 516    | 消息优先级错误 | sendEvent、sendEventEx |
| 517    | 通知消息为空 |                         |
| 518    | 超过单个对战服务最大房间数限制（2000） | createRoom、joinRoom |
| 519    | 帧同步参数错误 | setFrameRate |
| 520    | 未检测到 gameServer ，请检查是否成功启动 gameServer ; 或检查本地调试是否有正确退出（命令行工具输入‘quit’退出） | createRoom、joinRoom |
| 521    | gameServer 不存在 | sendEvent、sendEventEx |
| 522    | 非帧同步状态 | sendFrameEvent |
| 523    | 向 gameServer 发送消息失败失败 | sendEvent、sendEventEx |
| 524    | gameServer 推送的消息类型错误 |                         |
| 525    | 对战服务处理非工作状态 | createRoom、joinRoom |
| 526    | 通知 gameServer 玩家 checkin 失败 | checkin |
| 527    | 消息发送频率过快，超过单房间 500次/秒(总人数 (总接收+总发送)) | sendEvent、sendEventEx、sendFrameEvent |
| 528    | 初始化帧同步缓存失败 | setFrameRate |
| 529    | 开启观战房间失败 | createRoom、joinRoom |
| 530    | 通知观战房间消息失败 | 观战相关API |
| 531    | 观战服务异常 | 观战相关API |
| 532    | 观战房间推送消息错误 | 观战相关API |
| 533    | 观战 LiveID 错误 | 观战相关API |
| 534    | 不支持的观战协议 | 观战相关API |
| 539    | 没有可用的观战服务 | 观战相关API |
| 540    | 房间不支持观战 | 观战相关API |
| 541    | 观战帧缓存初始化失败 | 观战相关API |
| 542    | 观战帧缓存溢出 | 观战相关API |
| 543    | 帧的缓存毫秒数上限为1小时，且不能为小于-1的值 | setFrameRate |
| 544    | 帧缓存溢出 | getOffLineData |
|        |                                       |                         |
| 60X    | 分配对战服务失败                      | createRoom、joinRoom    |
