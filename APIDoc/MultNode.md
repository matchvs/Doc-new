/*
Title: 多节点接口说明
Sort: 49
*/



# 多节点接口

为了解决不同地域使用 Matchvs 延迟过高问题，可以给游戏创建多个区域的节点。在 Matchvs SDK中 使用接口获取节点信息，并切换到指定的节点。

>  注意：SDK v3.7.9+ 开放多节点功能

## init

init 接口和之前的 init 接口是同一个，这里只是在 init 接口中新增了一个参数 threshold，只有传了该参数，才能获取节点列表和使用指定节点登录。[参考init接口说明](./JavaScript)

## getNodeList

获取节点列表信息。在 init 成功后才能使用，并且init 一定要传入 threshold参数。不然返回值为 null。

```
engine.getNodeList()
```

#### 无请求参数

#### 返回值

| 属性    | 类型   | 描述                  | 示例         |
| ------- | ------ | --------------------- | ------------ |
| nodeID  | number | 节点ID                | 1            |
| area    | string | 节点区域名称          | 中国1区-华南 |
| latency | number | 延迟，单位 毫秒（ms） | 10           |



## login

 登录接口和前面 API文档描述的登录接口是同一个，[login接口说明](./JavaScript) 。只是加了一个 nodeID 参数，如果不传这个参数或者传入的参数为0，login 则使用默认节点登录。否则会使用指定的 nodeID登录，nodeID 必须是从 getNodeList 接口获取的有效ID。



## changeNode

切换到指定节点中，切换节点只能在拥有多个节点的情况下使用，并且只能切换到 getNodeList 获取到的节点中。所有在 init 的时候设置好 threshold 参数。

切换节点是指在使用 login 接口登录了默认节点后，想换一个节点就可以使用 changNode 接口切换到指定节点，所以，要使用 changeNode 接口必须是在登录后。

```
engine.changeNode(args)
```

#### args 的属性

| 参数   | 类型   | 描述                      | 示例 |
| ------ | ------ | ------------------------- | ---- |
| nodeID | number | 从 getNodeList 获取的信息 | 1    |

返回值参考 [错误码说明](../ErrCode)

