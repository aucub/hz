---
title: Grasscutter-2.8-Windows
tags:
  - Grasscutter
  - Windows
  - op2.8
keywords: Grasscutter,Windows,Genshin Impact
abbrlink: 60991
date: 2022-07-13 21:03:51
---
Grasscutter割草机在Windows上的部署-2.8
<!-- more -->
### 准备需要使用的文件和应用

1.[下载并解压适用于2.8的Grasscutter源代码（也可按需求选择其他版本）](https://codeload.github.com/Grasscutters/Grasscutter/zip/refs/heads/2.8)

2.[下载并解压适用于2.8的Grasscutter_Resources](https://codeload.github.com/Koko-boya/Grasscutter_Resources/zip/refs/heads/2.8)

3.下载并[安装MongoDB Community Server](https://fastdl.mongodb.org/windows/mongodb-windows-x86_64-5.0.9-signed.msi)，如无修改安装目录的需求一路点确认即可。

4.准备op2.8非大陆版本的客户端，建议登陆一次，下载需要更新的资源，大陆版本也可。本文以非大陆版本为例，~~GenshinImpact.exe~~路径为~~C:\Program Files\Genshin Impact\GenshinImpact.exe~~,路径问题请自行转换。

5.安装JDK17，并加入环境变量。

6.安装mitmdump，首先安装Python，在通过Python的pip包管理器安装mitmdump。

Python可选择Microsoft store安装，或者winget安装：cmd或终端输入：

```shell
winget install Python.Python.3
```

其他方法自行发挥。之后安装mitmdump，重新打开cmd或终端输入：

```shell
pip install mitmdump
```

7.下载global-metadata.dat文件(不再提供，可使用Grasscutters Cultivation)

## 制作

1.进入解压好的Grasscutter-2.8源代码文件夹，空白处右键选择在终端中打开，或者在文件路径栏输入cmd回车，输入以下命令（确保JDK版本正确不然会报错）：

```shell
.\gradlew.bat
.\gradlew jar
```

编译完成将生成jar文件，文件名如grasscutter-1.2.2-dev.jar，将其重命名为grasscutter.jar

可以使用编译好的jar文件，版本正确即可。

2.在Grasscutter-2.8源代码文件夹新建resources文件夹，将Grasscutter_Resources-2.8\Resources下的文件夹全部移动至Grasscutter-2.8\resources目录下。

### 开始部署

1.依然是Grasscutter-2.8文件夹，空白处右键选择在终端中打开，或者在文件路径栏输入cmd回车，输入以下命令（不要关闭窗口）：

```shell
mitmdump -s proxy.py -k
```

2.进入C:\Users\用户名\\.mitmproxy目录，双击打开mitmproxy-ca-cert.cer文件，依次选择安装证书-存储位置：本地计算机-将所有证书放入下列存储-浏览-受信任的根证书颁发机构-下一页-完成。

3.打开设置-网络和Internet-代理-使用代理服务器：设置-打开使用代理服务器开关，代理IP地址填入127.0.0.1，端口填入8080（这里需要填入mitmdump监听的端口，如果修改了监听端口请自行选择），之后选择保存。

4.回到Grasscutter-2.8文件夹，双击运行start.cmd，服务端启动完毕（不要关闭窗口）。如果提示选择语言，输入chs回车即可。如需关闭服务端则输入stop回车。

运行成功后你可以输入

 `account create 用户名` 

例如`account create 123456`
创建账户，密码在第一次登陆时直接输入设置即可，将会作为之后的登录密码。

5.检查Grasscutter-2.8文件夹，现在至少应该存在以下文件或目录。

```TEXT
Grasscutter-2.8
├── config.json
├── data
├── grasscutter.jar
├── keystore.p12
├── proto
├── proxy.py
├── proxy_config.py
├── resources
├── start.cmd
└── start_config.cmd
```

6.替换global-metadata.dat文件（针对于2.8版本，使用启动器请忽略），将下载的global-metadata.dat文件复制到~~C:\Program Files\Genshin Impact\GenshinImpact_Data\Managed\Metadata~~和~~C:\Program Files\Genshin Impact\GenshinImpact_Data\Native\Data\Metadata~~目录下，覆盖原文件，建议先备份原文件。

```TEXT
Genshin Impact
├── GenshinImpact.exe
├── GenshinImpact_Data
│   ├── Managed
│   │   └── Metadata
│   │       └── global-metadata.dat   
│   └── Native
│       └── Data
│           └── Metadata
│               └── global-metadata.dat 
├── UnityPlayer.dll
├── mhypbase.dll
├── mhyprot2.Sys
├── mhyprot2.Sys.bak
├── mhyprot3.Sys
├── mhyprot3.Sys.bak
└── pkg_version
```

### 启动

双击启动~~C:\Program Files\Genshin Impact\GenshinImpact.exe~~
游戏内可通过与Server聊天执行命令
如获取角色，武器等：
```shell
/give all c6 r5 lv90 x99999
```


### 部署失败问题

部署失败的很多，通常与jar文件是否可用，文件的对应版本是否一致或正确，客户端是否正确修改等等有关，如果遇到难以解决的问题除了向专业人士请教或使用搜索引擎外，您也可以尝试使用Linux服务器部署，您可以使用一键脚本或docker等工具部署，其部署方式简单且成功率高。