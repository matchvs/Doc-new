/*
Title: Matchvs开发者后台使用指引
Sort: 2
*/

## 注册 Matchvs

[点击登录](http://home-ge.matrix.jdcloud.com/login) 如果还没有注册账号请先注册

## 新建游戏

登录Matchvs后选择 [控制台](http://home-ge.matrix.jdcloud.com/manage/gameContentList) ，根据以下流程创建游戏

```flow
st=>start: 登录
ed=>end: OK
op1=>operation: 控制台
op2=>operation: 我的游戏
op3=>operation: 新建游戏
st->op1->op2->op3->ed

```

创建好游戏后可以查看创建的游戏信息，比如` appkey, Secret, gameID `等如下图：

![](http://imgs.matchvs.com/static/Doc-img/new-start/MatchvsManageimg/Matchvs_use1.png)

使用 Matchvs SDK 需要用到` appKey、secret, gameID` 等信息。

## 更多Matchvs

[Matchvs环境说明](../Advanced/EnvGuide)


