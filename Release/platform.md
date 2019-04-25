/*
Title: 平台支持说明
Sort: 16
*/

## 支持平台

微信小程序、oppo小游戏、vivo小游戏、华为小游戏、百度小游戏、Facebook Instant Games、QQ玩一玩安卓、iOS/Mac、Windows、Web、H5小游戏

## 编译注意事项

1.使用CocosCreator 2.0.9 编译 **vivo 小游戏**时，不需要填写“**自定义npm文件夹路径**”，填写可能会引发错误。

2.目前Cocos 编译的**百度小游戏**仅支持 1.15.3 版本的百度开发者工具，详见Cocos官方文档说明

[Cocos官方文档](https://docs.cocos.com/creator/manual/zh/publish/publish-baidugame.html)

3.**百度小游戏**编译时，需要开启matchvsSDK的 wss，步骤如下：

1）使用编辑器或文本工具打开 matchvs SDK

2）找到  IsWss:!1

3）将IsWss:!1 改成 IsWss: 1，即去掉 “！”，然后保存

4）回到CocosCreator 等待刷新，刷新成功后即可编译。