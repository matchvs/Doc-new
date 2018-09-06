# Matchvs 集成项目发布
[TOC]

Unity平台的发布步骤 , 完全遵循Unity的工作流.

### 1.1 构建设置

对目标构建平台进行配置,需要注意的是:

1. Android平台的NDK推荐使用`android-ndk-r10e`,勾选arm-v7a
2. Window平台使用x64位的Unity(Unity5.x以上的版本默认为64位),否则可能会找不到Matchvs的函数,同时增大文件体积
### 1.2 发布

`build` 导出可执行二进制文件

### 延伸阅读

1.Unity可发布到手机,TV,桌面平台,各平台在操控方式(手柄/键盘/触屏),文件系统均有一定差异,.详情可见 :

 https://docs.unity3d.com/Manual/PlatformSpecific.html

2.Unity官方项目各平台(Android/iOS/桌面/FaceBook/WebGL/)构建设置,发布指南:

https://docs.unity3d.com/Manual/30_search.html?q=publishing