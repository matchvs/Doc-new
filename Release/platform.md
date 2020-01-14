/*
Title: 平台支持说明
Sort: 16
*/

## 支持平台

微信小程序、oppo小游戏、vivo小游戏、华为小游戏、百度小游戏、Facebook Instant Games、QQ玩一玩、安卓、iOS/Mac、Windows、Web、H5小游戏。
目前暂不支持头条小游戏

## 编译注意事项

1.使用CocosCreator 2.0.9 编译 **vivo 小游戏**时，不需要填写“**自定义npm文件夹路径**”，填写可能会引发错误。

2.目前Cocos 编译的**百度小游戏**仅支持 1.15.3 版本的百度开发者工具，详见Cocos官方文档说明

[Cocos官方文档](https://docs.cocos.com/creator/manual/zh/publish/publish-baidugame.html)

3.**百度小游戏**编译时，需要开启matchvsSDK的 wss，步骤如下：

1）使用编辑器或文本工具打开 matchvs SDK

2）找到  IsWss:!1

3）将IsWss:!1 改成 IsWss: 1，即去掉 “！”，然后保存

4）回到CocosCreator 等待刷新，刷新成功后即可编译。

4. 微信小游戏

    模拟器调试 你打开微信开发者工具的调试选项.
    真机调试  开启微信的小程序调试模式.
    
    
> 发布时 客户端 init 参数platfrom 由 alpha 改为realse ,微信小游戏平台要启用wss选项: https://doc.matchvs.com/Release/platform ,

> egret发布成微信小游戏时,如果提示代码文件过大超过4096K. 则可以用matchvs.min.js替换微信小游戏工程中的matchvs.js文件. 
