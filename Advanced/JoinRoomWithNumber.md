/*

Title: 分享房间短号加入房间

Sort: 99

*/

# 如何使用Matchvs实现分享房间短号邀请加入指定房间的功能



利用`微信`/`短信`/`URL`/`二维码`来邀请好友一起玩游戏,打造专用私密的游戏场景.

## 流程图

 

![flow](https://raw.githubusercontent.com/matchvs/Doc/master/flow.png)

高清示意图链接 (./JoinRoomWithNumber.Assets/flow.png)

##  流程说明:

- 以UserID作为房间短号,并以房间短号作为属性来创建房间 
- 通过微信/短信/URL/二维码分享房间短号 
- 扫一扫/二维码识别/输入短号/URL取参数来加入指定房间 

## 流程详解

见[微信约战](./wechatInvite)(./wechatInvite) 