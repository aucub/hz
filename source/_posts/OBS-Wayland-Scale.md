---
title: Gnome Wayland环境下设置OBS Studio缩放
keywords: Gnome,Wayland,OBS
abbrlink: dc187d12
date: 2023-08-05 03:28:28
updated: 2023-08-05 03:28:28
tags:
  - Linux
---

在 GNOME Tweaks 中，可以轻松地调整字体缩放比例，对大多数应用程序都有效。然而，OBS Studio 是个例外。OBS Studio 是目前广泛使用的视频录制和流媒体平台之一，要正确设置其字体缩放比例以提高观感体验并不容易。

<!-- more -->

在 Gnome Wayland 环境中，OBS Studio 是一个基于 QT 框架的应用程序。在 `/etc/environment` 中设置 `QT_FONT_DPI` 或者 `QT_SCALE_FACTOR` 对其来说是无效的。这个问题给用户带来了不少困扰。

通过深入研究，我们找到了一个解决方案，即设置 systemd 用户环境变量。这个解决方案非常简单，只需在 `~/.config/environment.d/` 目录中创建一个 `.conf` 文件，并在其中写入 `QT_FONT_DPI` 或者 `QT_SCALE_FACTOR` 的内容。这个简单的操作将使 OBS Studio 的字体缩放比例得以正确设置，提高观看体验的舒适度。

值得注意的是，这个解决方案同样适用于其他基于 QT 框架的应用程序。因为 `QT_FONT_DPI` 或者 `QT_SCALE_FACTOR` 是一个全局的环境变量，可以影响整个系统中所有的 QT 应用程序，而不仅仅是 OBS Studio。这个解决方案不仅为 OBS Studio 用户提供了帮助，也为其他基于 QT 的应用程序用户提供了解决方法。

**操作步骤：**

1. 在主目录下找到 `.config` 目录，并在其中创建一个名为 `environment.d` 的新文件夹，即 `~/.config/environment.d/`。
2. 在 `environment.d` 文件夹中创建一个新的文件，文件名可以任意命名，但后缀必须为 `.conf`，例如 `envvars.conf`。
3. 在新创建的文件中输入以下内容：

```text
QT_FONT_DPI=120
QT_SCALE_FACTOR=1
```

其中，`QT_FONT_DPI` 是一个全局的环境变量，其值代表 OBS Studio 字体缩放比例的大小。您可以根据需要进行调整，但请注意不要设置过高，以免影响系统性能。
4. 保存并关闭文件，然后重新启动 OBS Studio 应用程序。此时，您会发现 OBS Studio 的字体缩放比例已按照您的设置进行了调整。