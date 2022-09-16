---
title: Grasscutter-2.8.5-Windows
tags:
  - Grasscutter
  - Windows
  - op2.8.5
keywords: Grasscutter,Windows,Genshin Impact
abbrlink: 51338
date: 2022-07-15 12:04:28
---
Grasscutter割草机在Windows上的部署-2.8.x
<!-- more -->
### 准备需要使用的文件和应用

1.[下载并解压适用于~~2.8.5~~的Grasscutter源代码（也可按需求选择其他版本）](https://github.com/Frontrooms/3.0-GC)

2.[下载并解压适用于~~2.8.5~~的Grasscutter_Resources](https://github.com/Frontrooms/3.0-Resources)

3.下载并[安装MongoDB Community Server](https://fastdl.mongodb.org/windows/mongodb-windows-x86_64-5.0.9-signed.msi)，如无修改安装目录的需求一路点确认即可。

4.准备~~op2.8.5~~[大陆](https://autopatchcn.yuanshen.com/client_app/download/beta_pc/20220708103922_J7gB70oC8LbfoVse/YuanShen_2.8.50_beta.zip)/[非大陆](https://autopatchhkbeta.yuanshen.ca/client_app/download/beta_pc/20220516111633_WUVnBEHDnBI6cKmM/GenshinImpact_2.8.50_beta.zip)版本的客户端，本文以非大陆版本为例，~~GenshinImpact.exe~~路径为~~C:\Program Files\Genshin Impact\GenshinImpact.exe~~,建议使用大陆版本，路径问题请自行转换。

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

7.下载global-metadata.dat文件(可使用Grasscutters Cultivation)

## 制作

1.进入解压好的~~Grasscutters2.8.5~~源代码文件夹，空白处右键选择在终端中打开，或者在文件路径栏输入cmd回车，输入以下命令（确保JDK版本正确不然会报错）：

```shell
.\gradlew.bat
.\gradlew jar
```

编译完成将生成jar文件，文件名如grasscutter-1.2.2-dev.jar，将其重命名为grasscutter.jar

可以使用编译好的[jar文件](https://github.com/Frontrooms/3.0-GC/releases)

2.在~~Grasscutters2.8.5~~源代码文件夹新建resources文件夹，将~~Genshin-Impact-resources~~下的文件夹全部移动至~~Grasscutters2.8.5\resources~~目录下。。

### 开始部署

1.依然是~~Grasscutters2.8.5~~文件夹，空白处右键选择在终端中打开，或者在文件路径栏输入cmd回车，输入以下命令（不要关闭窗口）：

```shell
mitmdump -s proxy.py -k
```

2.进入C:\Users\用户名\\.mitmproxy目录，双击打开mitmproxy-ca-cert.cer文件，依次选择安装证书-存储位置：本地计算机-将所有证书放入下列存储-浏览-受信任的根证书颁发机构-下一页-完成。

3.打开设置-网络和Internet-代理-使用代理服务器：设置-打开使用代理服务器开关，代理IP地址填入127.0.0.1，端口填入8080（这里需要填入mitmdump监听的端口，如果修改了监听端口请自行选择），之后选择保存。

4.回到~~Grasscutters2.8.5~~文件夹，双击运行start.cmd，服务端启动完毕（不要关闭窗口）。如需关闭服务端则输入stop回车。

运行成功后你可以输入

`account create 用户名`

例如`account create 123456` 创建账户，密码在第一次登陆时直接输入设置即可，将会作为之后的登录密码。

5.检查~~Grasscutters2.8.5~~文件夹，现在至少应该存在以下文件或目录。

```TEXT
Grasscutters2.8.5
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

6.替换global-metadata.dat文件(使用启动器请忽略)，将下载的global-metadata.dat文件复制到~~C:\Program Files\Genshin Impact\GenshinImpact_Data\Managed\Metadata~~和~~C:\Program Files\Genshin Impact\GenshinImpact_Data\Native\Data\Metadata~~目录下，覆盖原文件，建议先备份原文件。

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

双击启动~~C:\Program Files\Genshin Impact\GenshinImpact.exe,~~游戏内可通过与Server聊天执行命令 如获取角色，武器等：

```shell
/give all c6 r5 lv90 x99999
```
