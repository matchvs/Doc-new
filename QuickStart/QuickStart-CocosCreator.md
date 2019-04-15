/*
Title: 快速入门-CocosCreator
Sort: 3
*/


## IDE的下载

如果你的电脑还没有安装cocosCreator可以前往cocos官网进行下载[cocos下载地址](http://www.cocos.com/download) 

![](http://imgs.matchvs.com/static/Doc-img/new-start/CocosCreatorImg/creatorDownload.jpg)

选取对应的版本进行下载和使用。

## IDE的安装

下载成功以后得到一个zip文件，如下图所示

![](http://imgs.matchvs.com/static/Doc-img/new-start/CocosCreatorImg/creatorZip.jpg)

打开zip后双击CocosCreator_setup.exe,选择对应的安装路径进行安装，此过程需要几分钟。

## 项目创建

CreatorIDE安装成功后，双击打开CreatorIDE，选择新建项目选择一个空白工程，如下图所示

![](http://imgs.matchvs.com/static/Doc-img/new-start/CocosCreatorImg/creatorNewProject.jpg)

**注意** Matchvs的联网游戏案例已经集成进如了Cocos Creator v2.0.7版本中，建议新使用的玩家可以先建立一个联网游戏案例进行体验。

选择好自己的工程路径后，点击新建项目,新的工程就创建好了。

## SDK的集成

#### Creator V2.0.7

在CreatorV2.0.7 版本之后推出了新的服务模块。同时在服务模块中也集成了Matchvs，使大家集成更加方便 `服务模块在面板选项中`。


服务模块如下图:

![CocosServer](QuickStart-CocosCreator.assets/CocosServer.png)

**注意** 服务模块加载SDK为Cocos渠道SDK，必须使用CreatorIDE登录的账号,Cocos渠道账户必须绑定公司才可以使用。(个人开发者可先在Cocos控制台添加一个公司信息，无需认证，即可使用)

 - 服务模块加载SDK路径为project/assets/scripts/matchvs/matchvs.all.js。

```javascript
let engine;
let response = {};
try {
    engine =  new window.MatchvsEngine();
    response = new window.MatchvsResponse();
	} catch (e) {
	console.warn("load matchvs fail,"+e.message);
}
module.exports = {
    engine: engine,
    response: response,
};
```
 服务模块加载SDK参考以上代码。

#### Creator V2.0.7以外的其他版本推荐大家升级到2.0.7以上版本使用

## 第一行代码

 1：在assets文件夹下创建Script和Scene两个文件夹，在Script文件夹下创建HelloWorld.js文件，在Scene文件夹下创建一个名字叫做HelloWorld的场景文件.

 2：双击场景文件创建一个Button和Label。目录结构如下

![](http://imgs.matchvs.com/static/Doc-img/new-start/CocosCreatorImg/creator_9.png)

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

![](http://imgs.matchvs.com/static/Doc-img/new-start/CocosCreatorImg/creator_10.png)

 5：将创建的对象与对应的精灵绑定，将1拖拽到2，3拖拽到4进行对象与精灵的绑定。

![](http://imgs.matchvs.com/static/Doc-img/new-start/CocosCreatorImg/creator_11.png)

绑定成功如下图所示

![](http://imgs.matchvs.com/static/Doc-img/new-start/CocosCreatorImg/creator_12.png)

 6：Matchvs SDK 接口使用
​	
 - 第一步初始化 init

```javascript
    MatchvsInit() {
		var appkey = '4fd4a67c10e84e259a2c3c417b9114f4';
		var gameVersion = 1;
        this.engine.init(this.rsp,'Matchvs','alpha',this.gameID,appkey,gameVersion);
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
	var DeviceID = 'abcdef';
	this.engine.login(userID,token,DeviceID)
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
        this.engine = new window.MatchvsEngine();
        this.rsp = new window.MatchvsResponse();
        this.rsp.initResponse = this.initResponse.bind(this);
        this.rsp.registerUserResponse = this.registerUserResponse.bind(this);
        this.rsp.loginResponse = this.loginResponse.bind(this);
    },

    MatchvsInit() {
		var appkey = '4fd4a67c10e84e259a2c3c417b9114f4';
		var gameID = 123456;
		var gameVersion = 1;
        this.engine.init(this.rsp,'Matchvs','alpha',gameID,appkey,gameVersion);
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
		var DeviceID = 'abcdef';
		this.engine.login(userID,token,DeviceID)
    },

    loginResponse:function (loginRsp) {
        if (loginRsp.status === 200) {
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


```

## 在Cocos Creator中使用Ts

当前项目支持Ts后，按照上面步骤成功导入JSSDK后，在官网的[下载页面](http://matchvs.com/serviceDownload)下载TypeScript，将其中的matchvs.d.ts拷贝到JSSDK的同路径下即可使用。

下面是一段TS的演示代码，实现的主要功能有初始化，注册，登录。[工程地址](https://github.com/matchvs/demo-creator)

```javascript
const {ccclass, property} = cc._decorator;


@ccclass
export default class NewClass extends cc.Component {

    @property(cc.EditBox)
    gameIdInput: cc.EditBox = null;

    @property(cc.EditBox)
    appKeyInput: cc.EditBox = null;

    @property(cc.Node)
    confirm: cc.Node = null;

    @property(cc.Node)
    clear: cc.Node = null;

    @property(cc.Node)
    independent: cc.Node = null;

    @property(cc.Node)
    labelInfo: cc.Label = null;
    private Engine:MatchvsEngine;
    private Response:MatchvsResponse;
    private GameID:number = 200978;
    private AppKey:string = "4fd4a67c10e84e259a2c3c417b9114f4";

    start () {
        let self = this;
        self.Engine = new MatchvsEngine();
        self.Response = new MatchvsResponse();
        this.confirm.on(cc.Node.EventType.TOUCH_END, function () {
            if (Number(self.gameIdInput.string) === 0 && Number(self.gameIdInput.placeholder) === 0  && self.appKeyInput.string === "" && self.appKeyInput.placeholder === ""){
                return;
            }
            if (self.appKeyInput.string === "") {
                self.AppKey = self.appKeyInput.placeholder;
		    } else {
                self.AppKey = self.appKeyInput.string;
            }
            if (Number(self.gameIdInput.string) === 0) {
                self.Engine.init(self.Response,"Matchvs","alpha",Number(self.gameIdInput.placeholder));
            }else {
                self.Engine.init(self.Response,"Matchvs","alpha",Number(self.gameIdInput.string));
            }
		});
        self.initEvent();
    }

    /**
     * 注册对应的事件监听和把自己的原型传递进入，用于发送事件使用
     */
    private initEvent () {
        this.Response.initResponse = this.initRsp.bind(this);
        this.Response.registerUserResponse = this.registerUserRsp.bind(this);
        this.Response.loginResponse = this.loginRsp.bind(this);
    }

    /**
     * 初始化回调
     * @param status
     */
    initRsp(status) {
        if (status === 200) {
            console.log("Ts版本 初始化成功");
            this.Engine.registerUser();
        } else {
            console.log("Ts版本 初始化失败");
        }
    }

    /**
     * 注册回调
     * @param userInfo
     */
    registerUserRsp(userInfo) {
        if  (userInfo.status === 0) {
            this.login(userInfo.userID,userInfo.token);
            console.log("Ts版本 注册成功");
        } else {
            console.log("Ts版本 注册失败");
        }
    }

    /**
     * 登录回调
     * @param loginRsp
     */
    loginRsp(loginRsp) {
        if  (loginRsp.status === 200) {
            console.log("Ts版本 登录成功");
            if (loginRsp.roomID !== null && loginRsp.roomID !== '0') {
                this.Engine.leaveRoom("");
                cc.director.loadScene("Lobby");
            } else {
                cc.director.loadScene("Lobby");
            }

        } else {
            console.log("Ts版本 登录失败");
        }

    }

    /**
     * 登录
     * @param id
     * @param token
     */
    login (id, token) {
        // this.labelLog('开始登录...用户ID:' + id + " gameID " + "200978");
        this.Engine.login(id,token,"abcdef");
    }

    /**
     * 页面log打印
     * @param info
     */
    labelLog (info) {
        this.labelInfo.string += '\n[LOG]: ' + info;
    }

}
```

[更多Matchvs文档查看基础功能文档](../APIBasic/JavaScriptBase)
