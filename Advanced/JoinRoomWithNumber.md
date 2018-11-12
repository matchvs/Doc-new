/*

Title: 分享房间短号加入房间

Sort: 99

*/

# 如何使用Matchvs实现分享房间短号邀请加入指定房间的功能

[TOC]

利用`微信`/`短信`/`URL`/`二维码`来邀请好友一起玩游戏,打造专用私密的游戏场景.

## 流程图

 

![flow](https://raw.githubusercontent.com/matchvs/Doc/master/flow.png)

高清示意图链接 (./JoinRoomWithNumber.Assets/flow.png)

##  流程说明:

- 以UserID作为房间短号,并以房间短号作为属性来创建房间 
- 通过微信/短信/URL/二维码分享房间短号 
- 扫一扫/二维码识别/输入短号/URL取参数来加入指定房间 

> 此流程试用有TV版本的游戏应用

## 流程详解

#### 1. 以UserID作为房间短号,并以房间短号作为属性来创建房间

因为UserID是Matchvs的regist接口返回的,具有唯一性,且长度较小, 所以我们用userID作为房间短号来标识一个房间. 调用`createRoom`并设置房间属性

> UserID长度较短,方便用户手动输入. URL和二维码分享的方式没有必要使用短号,直接方向RoomID即可, RoomID通过调用createRoom接口,在 createRoomResponse 取到 .

示意代码如下:

```javascript
 var userID = GameData.UserID;

 engine.createRoom({roomName:'',maxPlayer:4,mode:1,canWatch:1,visibilty:1,roomProperty:userID}, '', {})

```
> 注意：创建房间的时候，如果定向约战，不希望被非目标用户加入，可以设置visbility:0，否则可能被其他用户通过房间列表(getRoomList接口)看到并加入

#### 2. 通过微信/短信/二维码分享传播房间短号

#### 分享传播房间短号有4种方式:

- 1.微信分享:  可参考这篇文章(http://www.matchvs.com/service?page=wechatInvite)
- 2.短信分享:  对与navite应用, 可调用手机系统的短信功能发送给联系人.
- 3.URL分享:  对于H5应用.可以通过web url传参的形式来传递房间短号,直接加入游戏
- 4.二维码分享: 二维码分享建立在URL分享的基础之上, 将URL编码为二维码图片.利用手机扫一扫来启动应用.

#### 3.输入短号来加入指定房间

玩家通过应用的简单的输入交互或者二维码识别,拿到短号 , 再调用`joinRoomWithProperties` 即可达到邀请加入制定房间的目的.
示意代码:

```javascript
//tags参数即为createRoom的roomProperty房间属性,相同属性的玩家将会被匹配到一起,达到加入指定房间的目地
//9527为邀请者的userID,也就是上文提及的加入指定房间的房间短号
engine.joinRoomWithProperties(
{maxPlayer:4,mode:0, canWatch:1,tags:'9527'}
, "");
```

