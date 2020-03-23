/*
Title: 合作渠道说明
*/

# matchvs合作渠道说明

    matchvs在商务合作方面，与cocos，Egret以及阿里云达成了深度合作关系。为了方便区分渠道来源，在AppKey上增加了渠道标识。


## 渠道说明
 matchvs 的游戏区分：matchvs渠道， Cocos渠道， Egret渠道， 阿里云渠道
 区分标识为APPkey后两位 “#M”为matchvs渠道， “#C”为Cocos渠道， "#E"为Egret渠道， “#A”为阿里云渠道


 ## 渠道登录说明

 matchvs官网渠道，直接再matchvs注册登录即可

 Cocos渠道，需要再matchvs官网登录页面选择使用合作账号登录--点击COCOS，在跳转的页面输入cocos注册的账号密码登录即可。

 Egret渠道，需要再matchvs官网登录页面选择使用合作账号登录--点击Egret，在跳转的页面输入cocos注册的账号密码登录即可。

 阿里云渠道，需要再matchvs官网登录页面选择使用合作账号登录--点击Egret，然后输入在阿里云市场购买的matchvs服务生成的账号进行登录。

  ## 常见问题说明

### 1、使用Demo进行验证的时候，触发登录时提示：【游戏账户与渠道不匹配，请使用XXXX账号登录】

    原因：在CocosCreator，Egret Launcher直接创建的matchvs演示Demo，会自动集成对应渠道的SDK，如果使用非对应渠道的GameID进行使用，就会提示该信息

    解决方法：
    1、替换成对应渠道的SDK，如需要：Cocos渠道的可以在服务面板直接替换，Egret的可以在launcher里创建游戏
    matchvs渠道的可以直接在Matchvs官网进行下载

    2、更换对应渠道的Game ID，需要使用对应的渠道账号登录Matchvs官网进行获取，详见“渠道登录说明”

### 2、账户余额说明

    目前，Cosos登录的账户渠道，使用的是Cocos的账户系统，所有充值和余额管理都在Cocos的web控制面板完成，扣费流程为：matchvs进行结算，然后发起扣费请求到Cocos，由Cocos进行统一扣费。
    阿里云的同理。

### 3、触发登录时提示：【login or reconnect is fail. invaild sign】，ErrorCode402

    原因：应用校验失败，确认是否在未上线时用了release环境，并检查gameID、appKey 和 secret
    
    解决方法：
        1、填写对应的环境参数
        2、复制完整的渠道标识
