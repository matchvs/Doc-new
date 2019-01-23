/*
Title: GameServer发布
Sort: 34
*/

本文演示了 gameServer 开发完成后正式上线的流程。  



## 推送代码

gameServer 本地开发调试完成后需要将代码推送到 Matchvs 远程仓库，这里以 Node.js 版 gameServer 为例演示使用 git 命令推送代码的流程，其它语言的 gameServer 与此流程一致。如果你使用的是 git GUI 工具，请参考相应的工具使用说明。

1. 查看 git 仓库当前状态

   ```shell
   $ git status
   On branch master
   
   No commits yet
   
   Untracked files:
     (use "git add <file>..." to include in what will be committed)
   
   	Dockerfile
   	Makefile
   	README.md
   	conf/
   	gsmeta
   	main.js
   	package.json
   	src/
   
   nothing added to commit but untracked files present (use "git add" to track)
   ```

2. 添加到暂存区

   ```shell
   $ git add -A
   ```

   该命令将所有修改的文件添加到暂存区。如果只想添加指定的文件可以使用：

   ```shell
   $ git add [file]
   ```

3. 提交到本地仓库

   ```shell
   $ git commit -m 'init code'
   [master (root-commit) 473e2ae] init code
    8 files changed, 360 insertions(+)
    create mode 100755 Dockerfile
    create mode 100755 Makefile
    create mode 100755 README.md
    create mode 100755 conf/config.json
    create mode 100755 gsmeta
    create mode 100755 main.js
    create mode 100755 package.json
    create mode 100755 src/app.js
   ```

4. 上传本地代码到远程仓库

   ```shell
   $ git push
   Enumerating objects: 12, done.
   Counting objects: 100% (12/12), done.
   Delta compression using up to 8 threads.
   Compressing objects: 100% (9/9), done.
   Writing objects: 100% (12/12), 4.08 KiB | 2.04 MiB/s, done.
   Total 12 (delta 0), reused 0 (delta 0)
   remote: =============== Begin build App ========================
   remote:   % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
   remote:                                  Dload  Upload   Total   Spent    Left  Speed
   remote:   0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--remote:   0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--remote: 100    53    0     0  100    53      0     49  0:00:01  0:00:01 --:--:--remote: 100    53    0     0  100    53      0     25  0:00:02  0:00:02 --:--:--remote: 100    53    0     0  100    53      0     17  0:00:03  0:00:03 --:--:--remote: 100    53    0     0  100    53      0     13  0:00:04  0:00:04 --:--:--remote: 100    53    0     0  100    53      0     10  0:00:05  0:00:05 --:--:--remote: 100    53    0     0  100    53      0      8  0:00:06  0:00:06 --:--:--remote: 100    87  100    34  100    53      5      8  0:00:06  0:00:06 --:--:--     0
   remote: build status:  0
   remote: Build Successful !!
   remote: =============== End build App ===========================
   To ssh://git.matchvs.com:3879/1424769556baec5362f5b1513f7e1167.git
    * [new branch]      master -> master
   ```

   推送结果显示：`remote: Build Successful !!`表示代码已经成功推送到了 Matchvs 远程仓库，并且构建成功。  
   如果推送失败则会显示相应的错误原因。

## 发布上线

### 发布游戏

**发布 gameServer 的前提是游戏已经发布上线，否则 gameServer将会发布失败。**

前往 Matchvs 官网控制台：

![](http://imgs.matchvs.com/static/Doc-img/gamePub/GameServerImg/unpublicgame&gameserver.png)

点击”我的游戏“进入我的游戏列表，选择要发布的游戏点击”发布上线“，游戏状态变更为“已商用”，即已发布上线。

![](http://imgs.matchvs.com/static/Doc-img/gamePub/GameServerImg/publicgame&gameserver.png)



### 发布 gameServer

**gameServer 发布上线之前，请确保：**

**1. 游戏已经发布上线**；

**2. 本地调试通过，并且已经将代码推送到 Matchvs 远程仓库。**


**如果修改了 gameServer 代码，则在 git 更新代码后，须先发布，然后重启 gameServer，之后才会运行更新后的 gameServer。**

前往 Matchvs 官网控制台：

![](http://imgs.matchvs.com/static/Doc-img/gamePub/GameServerImg/unpublicgameserver.png)

点击”gameServer“进入我的gameServer列表，选择要发布的 gameServer 点击“发布”，发布成功之后“启动”按钮变成可点击状态，点击“启动”即可启动 gameServer。

![](http://imgs.matchvs.com/static/Doc-img/gamePub/GameServerImg/publishedgameserver.png)



## 常见问题

### 发布失败是什么原因？

发布失败的可能原因有以下几种：

- 无效的 gameServer 仓库：从 git 仓库拉取代码失败，请检查是否创建了 gameServer

- 未上传 gameServer 代码：从 git 仓库拉取代码成功，但是仓库未上传任何文件

- 未找到 gsmeta 文件：gsmeta 是 gameServer 描述文件，位于 gmeServer 框架的根目录下。发布时报该错误时请检查：

  1. 代码根目录是否包含 gsmeta 文件

  2. 代码目录层次是否正确，是否多嵌套了一层。标准目录结构参考各语言“新手上路”文档：

    Node.js：https://doc.matchvs.com/QuickStart/GameServer-JavaScript

    C#：https://doc.matchvs.com/QuickStart/QuickStart-CSharp

    Java：https://doc.matchvs.com/QuickStart/GameServer-Java

- 未找到 Makfile 文件：Makefile 是 gameServer 编译脚本，位于 gmeServer 框架的根目录下。发布时报该错误时请检查：

  1. 代码根目录是否包含 Makefile 文件
  2. 代码目录层次是否正确，是否多嵌套了一层。标准目录结构参考各语言“新手上路”文档：

    Node.js：https://doc.matchvs.com/QuickStart/GameServer-JavaScript

    C#：https://doc.matchvs.com/QuickStart/QuickStart-CSharp

    Java：https://doc.matchvs.com/QuickStart/GameServer-Java

- 生成 Docker 镜像失败，请检查代码编译：

  1. 编译未通过，在本地编译通过后再重新上传代码
  2. 本地编译通过但是发布失败，请检查代码上传是否有遗漏，是否上传了所有的依赖资源，例如私有 `.so`、`.lib`文件。
  1. 可使用 `docker build`命令在本地测试是否能构建 docker 镜像
  2. 使用了私有第三方依赖资源，例如私有 npm 包。目前 Matchvs 不支持这种类型的私有包。

- 未知错误：服务器错误，提交工单或者在QQ群反馈问题获得进一步帮助



### 哪些文件需要提交？

基本目录结构必须保证和各语言框架一致，参考各语言“新手上路”文档。

有些文件是不需要提交的，比如操作系统自动生成的文件、编译生成的中间文件和可执行文件、本地调试产生的日志文件、包含敏感信息的文件等等。将需要忽略的文件名添加到`.gitignore`文件里，这样在提交代码时 git 就会自动忽略这些文件。另外各语言有一些特定的需要忽略的文件名，参考 github 提供的 `.gitignore` 模板：https://github.com/github/gitignore