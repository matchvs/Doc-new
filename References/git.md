/*
Title: git 使用
Sort: 58
*/
Git是一个开源的分布式版本控制系统，可以有效、高速的处理从很小到非常大的项目版本管理。Git 是 Linus Torvalds 为了帮助管理 Linux 内核开发而开发的一个开放源码的版本控制软件。

Matchvs 使用Git管理开发者提交的 gameServer 代码，并通过 Git 自动获取代码、进行编译发布，这里简单介绍下Git的安装和使用。

Git支持在Linux、Windows等系统上使用，这里主要对这两种系统平台进行说明，使用其它系统需自行网上查找。  

## Git安装
### Windows  

* 下载  

官网下载页面地址：https://git-scm.com/downloads  
点击下图红框中的按钮，两个都可以达到下载的目的。  

![image](http://imgs.matchvs.com/static/gitDownload.png)

* 安装   

双击下载的安装包，例如：Git-2.12.0-64-bit.exe  

  按下面步骤依次点击"Next"进行安装。  
  ![image](http://imgs.matchvs.com/static/gitSetup1.png)  
  ![image](http://imgs.matchvs.com/static/gitSetup2.png)  
  ![image](http://imgs.matchvs.com/static/gitSetup3.png)  
  ![image](http://imgs.matchvs.com/static/gitSetup4.png)  
  ![image](http://imgs.matchvs.com/static/gitSetup5.png)   
  ![image](http://imgs.matchvs.com/static/gitSetup6.png)  
  ![image](http://imgs.matchvs.com/static/gitSetup7.png)   
  ![image](http://imgs.matchvs.com/static/gitSetup8.png)   


 点击"Finish"即完成安装。  

  然后鼠标"右键"，如下图所示，点击 "Git Bash Here"便可打开Git命令行窗口  
 ![image](http://imgs.matchvs.com/static/gitBash.png)   


### Linux  
 很多新版的Linux系统都已经集成了git，先尝试执行 git；  
 如果出现如下的界面（不同系统可能稍有不同），表示已经安装，无需再重新安装。  

 ![image](http://imgs.matchvs.com/static/gitLinux.png)   

如果提示"command not found"，表示未安装。 
对于未安装的情况，CentOS系统可以通过`yum`命令安装，如下 ：  
`yum -y install git`  

如果你使用的是Debian 或 Ubuntu Linux，通过命令 `apt-get install git `就可以完成Git的安装   
（较老的系统，如果安装不成功，可以试试 `apt-get install git-core`）  

  

## clone远程仓库  
`git clone $remote.addr.git $local_project_name `  
如下图所示，将远程仓库clone到本地目录 cpserver；  
对于一个空的仓库，进入cpserver目录,其中会有个文件README.md表示clone成功  
![image](http://imgs.matchvs.com/static/gitClone.png)

## 连接远程仓库  
初始化本地仓库目录：`git init`   
连接远程仓库：`git remote add origin $remote`  
拉取远程仓库：`git pull origin master`  
如果远程仓库是裸仓库（空的），则拉取的目录中会有READE.md文件 

![image](http://imgs.matchvs.com/static/gitConn.png) 

## 查看远程仓库地址
`cd cpserver`
`git remote -v`    

![image](http://imgs.matchvs.com/static/gitRemote.png)   

## 提交代码 
代码文件放到仓库目录下后，执行如下步骤，便可以将本地仓库的文件提交到远程仓库，让其它连接该仓库的人员可见。  

添加文件：`git add 文件`  
提交仓库：`git commit -m "add main.go"`  
推送远程仓库：`git push`   

![image](http://imgs.matchvs.com/static/gitAdd.png) 

## 创建&推送分支    

![image](http://imgs.matchvs.com/static/gitBranch.png)   

#### 合并分支      

![image](http://imgs.matchvs.com/static/gitMerge.png)  

## 删除分支  
删除本地分支：`git branch -d $branch_name`  
删除远程分支：`git push origin --delete $branch_name`    

![image](http://imgs.matchvs.com/static/gitDelete.png)   

## 创建标签（打Tag）   

查看commit log : `git log`    

![image](http://imgs.matchvs.com/static/gitTag.png)    

创建Tag：`git tag version id`  
查看Tag：`git tag`  
删除Tag：`git tag -d version`    

change

![image](http://imgs.matchvs.com/static/gitTag1.png)   
