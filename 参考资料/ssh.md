/*
Title: 添加 ssh
Sort: 59
*/
## 一.环境
确保设备上已经安装git环境，git安装请参考 [git手册](http://www.matchvs.com/service?page=git)。    

## 二. windows 生成sshKey   


### 2.1 打开git bash   
单击鼠标右键，选择Git Bash here## 2.2 进入.ssh目录：  
在bash窗口执行`cd C:\Users\Administrator\.ssh`   

如果提示 “ No such file or directory”，你可以手动的创建一个 .ssh文件夹即可: `mkdir .ssh`


### 2.3  配置全局的name和email  
这里是你github的name和email  

`git config --global user.name "mvs" `  
`git config --global user.email "mvs@163.com"`


### 2.4 生成sshKey   :    
`ssh-keygen -t rsa -C "mvs@163.com"`    

 

连续按三次回车，这里设置的密码就为空了，并且创建了key。 此时查看目录`C:\Users\Administrator\.ssh `,会发现已经生成了id_rsa.pub文件。  将id_rsa.pub文件里的内容复制到控制台的新增ssh输入框里，点击添加即可。


tips : 如果提示 “ssh-keygen 不是内部或外部命令...” 可以按以下步骤操作解决：

*a. 找到git/usr/bin目录下的ssh-keygen.exe(如果找不到，可以在计算机全局搜索)*

*b. 属性-->高级系统设置-->环境变量-->系统变量,找到Path变量，进行编辑，End到最后，输入分号，粘贴复制的ssh-keygen所在的路径，保存；*

*c. 重启 git bash。*  

## 三. Linux 生成sshKey  

### 3.1 进入.ssh目录：  
执行`cd ~/.ssh`   

如果提示 “ No such file or directory”，你可以手动的创建一个 .ssh文件夹即可: `mkdir ~/.ssh`


### 3.2  配置全局的name和email  
这里是你github的name和email  

`git config --global user.name "mvs" `  
`git config --global user.email "mvs@163.com"`


### 3.3 生成sshKey   :    
`ssh-keygen -t rsa -C "mvs@163.com"`    

 

连续按三次回车，这里设置的密码就为空了，并且创建了key。 此时查看目录`~/.ssh `,会发现已经生成了id_rsa.pub文件。  将id_rsa.pub文件里的内容复制到控制台的新增ssh输入框里，点击添加即可。
