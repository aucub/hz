---
title: Grasscutter-Windows
keywords: Grasscutter,Windows,Genshin Impact
abbrlink: 60991
date: 2022-09-13 21:03:51
updated: 2023-10-02 05:52:52
tags:
  - Grasscutter
  - Windows
---
Grasscutter在Windows上的安装
<!-- more -->
### 准备使用的文件和应用

1. [下载并解压 Grasscutter 源代码（根据需求选择版本）](https://github.com/Grasscutters/Grasscutter)
2. [下载相应版本的 Grasscutter_Resources](https://gitlab.com/YuukiPS/GC-Resources)
3. 下载并[安装 MongoDB Community Server](https://fastdl.mongodb.org/windows/mongodb-windows-x86_64-7.0.2-signed.msi)，如果不需要更改安装目录，一路点击“确认”即可。

4. 准备非大陆版本的 op 客户端，建议登录一次，下载所需更新的资源，大陆版本也可。本文以非大陆版本为例，路径为 `C:\Program Files\Genshin Impact\GenshinImpact.exe`。

5. 安装 JDK17，并将其添加到环境变量。

6. 安装 mitmdump，首先安装 Python，然后通过 Python 的 pip 包管理器安装 mitmdump。

   可以选择通过 Microsoft Store 安装 Python，或者使用 winget 安装。在命令提示符或终端中输入：

   ```shell
   winget install Python.Python.3
   ```

   安装完成后，重新打开命令提示符或终端，输入以下命令安装 mitmdump：

   ```shell
   pip install mitmdump
   ```

7. 下载 `global-metadata.dat` 或 `UserAssembly.dll` 文件（可以使用启动器）：[Grasscutters Cultivation](https://github.com/Grasscutters/Cultivation) | [YuukiPS Launcher](https://github.com/akbaryahya/YuukiPS-Launcher)

## 制作

1. 进入解压好的 Grasscutter 源代码文件夹，在空白处右键选择在终端中打开，或者在文件路径栏输入 cmd 回车，输入以下命令（确保 JDK 版本正确，否则可能报错）：

   ```shell
   .\gradlew.bat
   .\gradlew jar
   ```

   编译完成后会生成一个 JAR 文件，文件名类似 `grasscutter-1.7.2-dev.jar`，将其重命名为 `grasscutter.jar`。

   你可以使用正确版本的已编译 JAR 文件。[Grasscutter Releases](https://github.com/Grasscutters/Grasscutter/releases)

2. 在 Grasscutter 源代码文件夹中新建 `resources` 文件夹，将 `Grasscutter_Resources\Resources` 下的所有文件夹移动到 `Grasscutter\resources` 目录下。

### 开始安装

1. 仍然是 Grasscutter 文件夹，空白处右键选择在终端中打开，或者在文件路径栏输入 cmd 回车，输入以下命令（不要关闭窗口）：

   ```shell
   mitmdump -s proxy.py -k
   ```

2. 进入 `C:\Users\用户名\\.mitmproxy` 目录，双击打开 `mitmproxy-ca-cert.cer` 文件，按顺序选择安装证书，存储位置选择“本地计算机”，将所有证书放入“受信任的根证书颁发机构”，然后点击“完成”。

3. 打开设置 -> 网络和 Internet -> 代理 -> 使用代理服务器，设置中打开使用代理服务器开关，代理 IP 地址填入 `127.0.0.1`，端口填入 `8080`（如果更改了监听端口，请自行选择），之后选择保存。

4. 回到 Grasscutter 文件夹，双击运行 `start.cmd`，服务端启动完毕（不要关闭窗口）。如果提示选择语言，输入 `chs` 回车即可。如需关闭服务端，则输入 `stop` 回车。

   运行成功后，你可以输入

   ```shell
   account create 用户名
   ```

   例如

   ```shell
   account create 123456
   ```

   创建账户，密码在第一次登录时直接输入设置即可，将会作为之后的登录密码。

5. 检查 Grasscutter 文件夹，现在至少应该存在以下文件或目录。

   ```plaintext
   Grasscutter
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

6. 替换 `global-metadata.dat` 文件，将下载的 `global-metadata.dat` 文件复制到 `C:\Program Files\Genshin Impact\GenshinImpact_Data\Managed\Metadata` 和 `C:\Program Files\Genshin Impact\GenshinImpact_Data\Native\Data\Metadata` 目录下，覆盖原文件（建议先备份原文件）。或者替换 `UserAssembly.dll` 文件，将下载的 `UserAssembly.dll` 文件复制到 `C:\Program Files\Genshin Impact\GenshinImpact_Data\Native` 目录下，覆盖原文件（建议先备份原文件）。

   ```plaintext
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

双击启动 `C:\Program Files\Genshin Impact\GenshinImpact.exe` 游戏内可通过与 Server 聊天执行命令，如获取角色、武器等：

```shell
/give all c6 r5 lv90 x99999
```