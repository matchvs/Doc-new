# CocosCreator新手上路

Creator开发者可以通过本文档开始Matchvs之旅。

## IDE的下载

如果你的电脑还没有安装cocosCreator可以前往cocos官网进行下载[cocos下载地址](http://www.cocos.com/download)

![](creatorDownload.png)

选取对应的版本进行下载和使用。

## IDE的安装

下载成功以后得到一个zip文件，如下图所示

![](creatorZip.jpg)

打开zip后双击CocosCreator_setup.exe,选择对应的安装路径进行安装，此过程需要几分钟。

## SDK的安装

CreatorIDE安装成功后，双击打开CreatorIDE，选择新建项目选择一个空白工程，如下图所示

![](creatorNewProject.jpg)

选择好自己的工程路径后，点击新建项目,新的工程就创建好了。

## SDK的集成

工程打开以后，点击CreatorIDE上方的扩展按钮，如图所示

![](creatorExtend.png)

点击扩展商店，点击Creator插件，选择Matchvs游戏云-联网服务插件，如下图所示

![](creatorStore.png)

进入插件页面后，点击下载完成以后点击安装按钮,选择安装到项目目录

![](creatorMatchvsInstall.png)

安装成功后，重启CreatorIDE，如下图所示

![](creatorInstallSuccess.png)

## 第一行代码

 1：在assets文件夹下创建Script和Scene两个文件夹，在Script文件夹下创建HelloWorld.js文件，在Scene文件夹下创建一个名字叫做HelloWorld的场景文件.

 2：双击场景文件创建一个Button和Label。目录结构如下

![](creator_9.png)

 3：在代码中定义login,info两个对象，代码如下。

```javascript
cc.Class({
    extends: cc.Component,

    properties: {
        login:cc.Button,
        info:cc.Label
    },


    onLoad () {
    },

    start () {

    },

});
```
 4：回到IDE中点击场景文件点击添加组件按钮，添加用户脚本文件

![](creator_10.png)

 5：将创建的对象与对应的精灵绑定，将1拖拽到2，3拖拽到4进行对象与精灵的绑定。

![](creator_11.png)

绑定成功如下图所示

![](creator_12.png)

 6：在代码中翱翔
	
 - 第一步初始化 init

```javascript
    MatchvsInit() {
        this.engine.init(this.rsp,'Matchvs','release',this.gameID);
    },

    initResponse :function (status) {
        if (status === 200) {
            this.labelLog('初始化成功，开始注册');
            this.engine.registerUser();
        } else {
            this.labelLog('初始化失败，错误码：'+status);
        }
    },

```

 - 第二步注册 registerUser

 **注意** 所有的response回调是异步的，下一步的操作一定要在response回调成功后在进行操作

```javascript
this.engine.registerUser();

registerUserResponse:function(userInfo) {
	if (userInfo.status ===0) {
		this.labelLog('注册成功，开始登录，登录ID是：'+userInfo.id+'token是'+userInfo.token);
		this.Login(userInfo.id,userInfo.token);
	} else {
		this.labelLog('注册失败错误状态码为：'+userInfo.status);
	}
},

```
- 第三步登录 login

```javascript
this.Login(userInfo.id,userInfo.token);

Login(userID,token) {
	var appkey = '4fd4a67c10e84e259a2c3c417b9114f4';
	var secret = 'bd00c3953f6a447eaaa1e36f19684764';
	var DeviceID = 'abcdef';
	var gatewayID = 0;
	var gameVersion = 1;
	this.engine.login(userID,token,this.gameID,gameVersion,
		appkey,secret,DeviceID,gatewayID)
},

loginResponse:function (MsLoginRsp) {
	if (MsLoginRsp.status === 200) {
		this.labelLog('恭喜你登录成功，来到Matchvs的世界，你已经成功的迈出了第一步，Hello World');
	} else {
		this.labelLog('登录失败');
	}
},
```

下面是完整的代码片段

```javascript
var mvs = require('matchvs.all')
cc.Class({
    extends: cc.Component,

    properties: {
        login:cc.Button,
        info:cc.Label,
        engine:null,
        rsp:null,
        gameID:200978,
    },

    onLoad () {
        this.login.node.on('click',this.MatchvsInit,this);
        this.engine = new mvs.MatchvsEngine();
        this.rsp = new  mvs.MatchvsResponse();
        this.rsp.initResponse = this.initResponse.bind(this);
        this.rsp.registerUserResponse = this.registerUserResponse.bind(this);
        this.rsp.loginResponse = this.loginResponse.bind(this);
    },

    MatchvsInit() {
        this.engine.init(this.rsp,'Matchvs','release',this.gameID);
    },

    initResponse :function (status) {
        if (status === 200) {
            this.labelLog('初始化成功，开始注册');
            this.engine.registerUser();
        } else {
            this.labelLog('初始化失败，错误码：'+status);
        }
    },

    registerUserResponse:function(userInfo) {
        if (userInfo.status ===0) {
            this.labelLog('注册成功，开始登录，登录ID是：'+userInfo.id+'token是'+userInfo.token);
            this.Login(userInfo.id,userInfo.token);
        } else {
            this.labelLog('注册失败错误状态码为：'+userInfo.status);
        }
    },
    Login(userID,token) {
        var appkey = '4fd4a67c10e84e259a2c3c417b9114f4';
        var secret = 'bd00c3953f6a447eaaa1e36f19684764';
        var DeviceID = 'abcdef';
        var gatewayID = 0;
        var gameVersion = 1;
        this.engine.login(userID,token,this.gameID,gameVersion,
            appkey,secret,DeviceID,gatewayID)
    },

    loginResponse:function (MsLoginRsp) {
        if (MsLoginRsp.status === 200) {
            this.labelLog('恭喜你登录成功，来到Matchvs的世界，你已经成功的迈出了第一步，Hello World');
        } else {
            this.labelLog('登录失败');
        }
    },

    /**
     * 页面log打印
     * @param info
     */
    labelLog: function (info) {
        this.info.string += '\n[LOG]: ' + info;
    },

});

​```javascript



```