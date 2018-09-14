## 1.准备
开发者需要先在官网的控制台[创建游戏](http://www.matchvs.com/manage/addGame)并且[添加gameServer服务](http://www.matchvs.com/manage/gameServer)，[下载gameServer框架](http://www.matchvs.com/serviceDownload)并且完成开发，同时在控制台上传了SSH key,SSH key的获取方式参考[ssh key 手册](http://www.matchvs.com/service?page=SSHkey). 


## 2.环境
确保设备上已经安装git环境。  


## 3.获取git仓库 
在控制台的gameServer服务详情可以直接获取git仓库地址。

## 4.上传代码 
首次上传gameServer代码到Git，要先进行初始化，并关联远程仓库。  

4.1 初始化：在代码目录下执行`git init`即可。

4.2 关联远程仓库：`git remote add origin ssh://git@xxx.xxx.x.xx/xxx.git`  
`ssh://git@xxx.xxx.x.xx/xxx.git`是第3步获取的git仓库地址。 

4.3 添加代码：`git add --all`

4.4 提交代码：`git commit -m "对提交代码的说明"`  

4.5 上传远程仓库：`git push origin master` （效果如下图）

代码上传到远程仓库时，平台会对提交的代码进行编译，编译的结果如下图红框出的说明；**如果显示不是如图的结果，说明编译有异常，请检查代码，处理后再进行上传**。  

![image](http://imgs.matchvs.com/static/codeUpload.png)