/*
Title: FaceBook小游戏
Sort: 39
*/

## 流程图

1. 创建小游戏->
1. 得到AccessToken和AppID->
1. 集成FaceBookSDK->
1. 打包zip上传至facebook托管并推送至生产环境->
1. 验证开发者企业身份->
1. 上传小游戏的logo等素材->
1. 配置隐私政策和用户协议->
1. 提交审核->
1. 上线成功

  

上传好的示例:https://apps-1926329300794066.apps.fbsbx.com/instant-bundle/2055219024536389/1620218768077583/index.html

## 集成SDK的方式

#### 方式一: 使用Egret/Cocos前端引擎集成facebook的SDK

> http://developer.egret.com/cn/github/egret-docs/Engine2D/facebookInstantGames/index.html
#### 方式二: FaceBook官方教程

> facebook官方教程较为详细,当使用方式一遇到问题时,可选用方式二



## 发布审核必要条件

- 1.公司验证

	- 公司注册证明 --营业执照
	- 公司地址或电话号码验证 --营业执照 
	- 您与公司的关联证明 --公司域名邮箱(如xxx@matchvs.com)

- 2.Apple Developer ID

>  https://www.facebook.com/business/help/2058515294227817

---







# FaceBook官方教程

> 以下内容来自facebook官方教程摘录(2018-11-15)

通过小游戏，用户能够十分轻松地在本地测试开发版本、自动化发布流程，以及与团队分享构建版本。本文档会详细说明以下步骤：

1. [通过本地服务器测试游戏](https://developers.facebook.com/docs/games/instant-games/test-publish-share#localhost)
2. [上传构建版本](https://developers.facebook.com/docs/games/instant-games/test-publish-share#upload)
3. [测试发布的构建版本](https://developers.facebook.com/docs/games/instant-games/test-publish-share#testing)
4. [提交游戏以供应用审核](https://developers.facebook.com/docs/games/instant-games/test-publish-share#appreview)
## 上传构建版本

### 将游戏打包为一个 .zip 文件

小游戏内容在 Facebook 基础设施上托管，因此，无需自行托管游戏内容或使用第三方服务。在准备好游戏进行测试后，将所有游戏文件打包为一个 .zip 文件。请注意，`index.html` 文件应位于此存档的根文件夹中，而不应位于任何子文件夹中。可通过两种方法上传捆绑包：

#### 1.通过开发者网站上传 .zip 文件

要上传 .zip 文件，请点击应用面板中**小游戏**产品的**虚拟主机**选项卡。在此处点击**上传版本**，即可将 .zip 文件上传到 Facebook 的托管服务中。

![img](facebook.Assets/42989098_551332648637369_4465527134588239872_n.png)

之后，构建版本会处理文件，仅需数秒时间。如果状态更改为“待命”，则表示应用已经准备好并可以推送到生产环境！

![img](facebook.Assets/42821515_2139131776366724_5215256947900547072_n.png)

#### 2.通过图谱 API 上传存档

您也可以通过图谱 API 调用上传捆绑包。如果有自动化部署系统，这会很有用。要执行此操作，需要通过**虚拟主机**版块请求一个上传口令，方法是单击顶部的**生成素材上传访问口令**按钮。

借助对话框中的口令，可向图谱 API 提交以下调用以提交 .zip 文件。请注意，我们特意使用视频子域，因为该网址配置为接收大型上传文件。

```
curl -X POST https://graph-video.facebook.com/{App ID}/assets 
  -F 'access_token={ASSET UPLOAD ACCESS TOKEN}' 
  -F 'type=BUNDLE' 
  -F 'asset=@./{YOUR GAME}.zip' 
  -F 'comment=Graph API upload'
```

之后，游戏会在已上传捆绑包列表中正常显示。可通过此调用与现有构建系统集成。

### 托管限制

请记住，Facebook 托管存在多项限制，其中最重要的是：

- 不支持服务器端逻辑（例如：php）。
- 上传文件的总大小不超过 200MB。
- 每次应用程序上传的文件数量不超过 500 个。

详情请参阅[虚拟主机参考文档](https://developers.facebook.com/docs/games/services/contenthosting)。

## 测试上传的构建版本

### 将构建版本放入生产环境

要在生产环境中测试上传的构建版本，必须先拥有已处于生产阶段的构建版本。点击**“★”**按钮将构建版本推送到生产环境。

![img](facebook.Assets/16686534_113502745838345_8364033545752018944_n.png)

请注意：

处于生产阶段的构建版本指的是可以提供给每位游戏玩家的构建版本。要在生产环境中测试更新，同时又不影响现有游戏人群，您可以构建一个用于测试的版本。这样，系统可以只向开发者和测试者提供此版本。

![img](facebook.Assets/42940923_336449060456398_3004121429307293696_n.png)

拥有处于“生产”阶段的构建版本后，您需要测试该构建版本，而不是测试在当前本地服务器中运行的构建版本。您可通过以下两种方式中的任意一种完成测试。

### 1.在 Facebook 中运行游戏

要在动态消息中分享游戏，单击**分享你的游戏**部分的**分享**按钮。此操作可让您在动态消息中分享游戏，并通过任何平台执行测试（桌面、iOS 或 Android）。

### 2.在 Messenger 中运行游戏

在 Messenger 的小游戏列表中，您和您的团队（在应用中分配了“管理员”、“开发者”或“测试者”身份的用户）应能够看到处于开发阶段的所有游戏的列表。此列表的标题为**开发中**。即使游戏尚未发布，这仍可帮助您在 Messenger 中测试游戏。

### 3.分享游戏链接

如果已将主页与游戏关联，那么您也可以生成可分享的链接。如果您设置了智能助手，用户点击此链接后，会在 Messenger 中打开与智能助手的对话，并自动打开游戏。如果未设置智能助手，用户点击链接后将前往您的 Facebook 主页，并自动从主页打开游戏。无论是那种方式，点击链接的任何用户都能以适当的方式开始玩游戏。

## 提交游戏以供应用审核

对发布的版本感到满意后，您需要在“应用审核”版块提交游戏供审核，以便我们的团队能评估其质量，同时评估其是否符合我们的[开放平台政策](https://developers.facebook.com/policy/#games)。
请务必在提交游戏前查看我们的[发布检查表](https://developers.facebook.com/docs/games/instant-games/getting-started/launch-checklist)，确保游戏符合规定的所有条件。该指南还包含在游戏通过审核后发布游戏的方法说明。

## 后续步骤

现在，您已了解如何测试和发布游戏，请在提交游戏前查看发布检查表：[小游戏发布检查表](https://developers.facebook.com/docs/games/instant-games/getting-started/launch-checklist)。另请参阅我们的[最佳实践](https://developers.facebook.com/docs/games/instant-games/best-practices)板块，了解游戏设计与更新建议。

## 游戏检查表：

### 游戏应该：

与原生游戏的外观效果一样，即不应该像网页一样滚动、缩放或移动

在“应用审核”功能选项中将可见性设置为**公开**

在“设置”功能选项下为游戏指定**命名空间**。

按照[游戏设置](https://developers.facebook.com/docs/games/instant-games/getting-started/game-setup#assets)版块的详细说明上传**所有素材**

初始下载包的大小不超过 3MB（对于[轻量级游戏](https://developers.facebook.com/docs/games/instant-games/guides/lightweight)而言，不超过 1MB）

通过 `FBInstant.setLoadingProgress` 提供**实际的**加载进程

响应移动设备的静音开关。对此，我们建议使用 WebAudio API。

使用 **SDK 4.0 或更高版本**，并通过模板发送所有自定义更新

确保订阅 `FBInstant.onPause`，以**妥善处理中断情况**，如在游戏过程中接电话或收到通知。

遵守所有已发布的 [Facebook 开发者政策](https://developers.facebook.com/policy/)

**关联至**公司以供应用审核。如要发布游戏，此公司必须[经过验证](https://www.facebook.com/business/help/2058515294227817)

### 游戏不应该：

与其他任何已发布应用程序（如 Facebook 网页游戏）**共用一个应用编号**

**链接至外部**的其他任何网站或应用。唯一的例外情况是链接至隐私政策页面。

**在应用名称中添加品牌信息**。例如，您的游戏不能取名为“Instant Gems”，也不能取名为“Gems for Messenger”，因为“Instant”和“Messenger”都是 Facebook 保留的关键词。

针对每个环境的每个会话发送**多个更新**。

请求小游戏 SDK 未提供的任何**用户信息**（包括使用 **Facebook 开放平台 Javascript SDK**）

展示第三方**广告**

在不支持支付的开放平台上展示任何**支付功能**

内嵌**小游戏 SDK**，或使用 connect.facebook.com 所提供版本以外的版本

在解析 `startGameAsync` 之前调用除下列以外的方法：

- `FBInstant.getSDKVersion()`
- `FBInstant.initializeAsync()`
- `FBInstant.getPlatform()`
- `FBInstant.setLoadingProgress()`
- `FBInstant.getSupportedAPIs()`
- `FBInstant.quit()`
- `FBInstant.onPause()`
	 `FBInstant.player.getID()`	



> 引用: 
>
> 测试发布:https://developers.facebook.com/docs/games/instant-games/test-publish-share
>
> 自检:https://developers.facebook.com/docs/games/instant-games/getting-started/launch-checklist
