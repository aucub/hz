---
title: Gnome Wayland环境下设置OBS Studio缩放
keywords: Gnome,Wayland,OBS
abbrlink: dc187d12
banner_img: /img/post_bannerh1.png
date: 2023-08-05 03:28:28
updated: 2023-08-05 03:28:28
tags:
  - Linux
---

在GNOME Tweaks中，可以轻松地调整字体缩放比例，这个设置对于大部分应用程序都是有效的。但是OBS Studio是例外。OBS Studio是目前广泛使用的视频录制和流媒体平台之一，对其设置正确的字体缩放比例，可以提高观感体验。

<!-- more -->

然而，要正确设置OBS Studio的字体缩放比例并非易事。在Gnome Wayland环境中，OBS Studio是一个基于QT框架的应用程序，并且在/etc/environment中设置QT_FONT_DPI或者QT_SCALE_FACTOR对其来说是无效的。这个问题对用户来说带来不少困扰。

基于我们的深入研究，我们发现了一个解决方案，那就是设置systemd用户环境变量。这个解决方案非常简单，您只需要在 ~/.config/environment.d/ 目录中创建一个".conf"文件，并在其中写入QT_FONT_DPI或者QT_SCALE_FACTOR的内容即可。这个简单的操作将使您的OBS Studio字体缩放比例得以正确设置，让您的观看体验更加舒适。

值得注意的是，这个解决方案同样适用于其他基于QT框架的应用程序，因为QT_FONT_DPI或者QT_SCALE_FACTOR是一个全局的环境变量，可以影响整个系统中所有的QT应用程序，而不仅仅是OBS Studio。这个解决方案不仅为OBS Studio用户提供了帮助，也为其他基于QT的应用程序用户提供了解决方法。

第一步，您需要在主目录下找到.config目录，并在其中创建一个名为environment.d的新文件夹，即~/.config/environment.d/。

第二步，您需要在environment.d文件夹中创建一个新的文件，文件名可以任意命名，但是后缀必须为.conf，例如envvars.conf。

第三步，您需要在新创建的文件中输入以下内容：

```text
QT_FONT_DPI=120
QT_SCALE_FACTOR=1
```

其中，QT_FONT_DPI是一个全局的环境变量，其值代表OBS Studio字体缩放比例的大小。您可以根据自己的需要进行调整，但请注意不要设置过高，否则可能会影响到您的系统性能。

第四步，保存并关闭文件，并重新启动您的OBS Studio应用程序。此时，您会发现OBS Studio的字体缩放比例已经按照您的设置进行了调整。