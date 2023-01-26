---
title: NixOS 安装教程
keywords: NixOS,Linux
abbrlink: b37fda7b
date: 2023-07-30 23:04:04
updated: 2023-10-02 23:04:04
tags:
  - Linux
  - NixOS
---

在当今快节奏的数字时代，操作系统已成为我们日常生活中不可或缺的一部分。尽管市面上有各种操作系统，像 NixOS 这样的发行版却显得极具独特性和前瞻性。NixOS 凭借其先进的技术和独特的设计风格，成为越来越多技术爱好者的首选。

然而，安装 NixOS 仍可能是一个具有挑战性的任务。因此，本文旨在为大家提供一份详细的 NixOS 安装教程，涵盖从基本的安装到高级配置，带领大家逐步深入 NixOS 的世界，探索其神奇之处，并最终成为熟练的使用者。

<!-- more -->

## 引言

NixOS 作为一种使用 Nix 包管理器的 Linux 发行版，具备独特的智慧系统。其设计哲学使其在 Linux 发行版中独树一帜。相比传统的 Linux 发行版，NixOS 具有声明式的配置管理、可复现的构建、原子升级和回滚等特点。

值得一提的是，Nix 包管理器作为一个纯函数式的包管理系统，是一个绝对完美的包管理器。它确保每个软件包都是独立、不可变、可复现、可追溯、可缓存、可共享、可部署和可回滚的。这意味着 Nix 避免了依赖地狱、版本冲突、配置漂移等常见问题，并提供了强大的沙盒、垃圾回收、分布式构建等功能。而 Nix 的设计哲学基于纯函数式编程，不仅避免了代码状态的不可预测性，还实现了代码状态的可预测性，传统 Linux 包管理器难以企及。

NixOS 将 Nix 包管理器应用到整个操作系统上，使得操作系统具备纯函数式的特性。在 NixOS 中，系统配置通过一个声明式的文件（/etc/nixos/configuration.nix）定义，描述了系统中需要安装的软件包、服务、用户、硬件驱动等。运行 nixos-rebuild 命令后，NixOS 将根据该文件自动构建新的系统版本，并能随时切换到任何历史版本。这实现了原子升级、回滚以及可复现和可迁移的系统状态。这种可复现性是 NixOS 的核心价值之一，在软件开发、测试、部署等方面都具有重要意义。

除此之外，NixOS 还有其他许多优点。它支持在不同系统之间共享包，使得您可以将一个应用程序打包在一个单独的 Nix 文件中，并在不同系统间复制，无需担心依赖性。此外，NixOS 还支持容器化和虚拟化，使其更加灵活和可扩展，几乎可以满足各种需求。

此外，NixOS 还有许多其他有趣的特性和功能。它支持各种文件系统，如 ext4、btrfs、zfs 等。还支持各种桌面环境，包括 GNOME、KDE、XFCE 等，并可根据用户喜好进行定制。对各种编程语言和开发框架也提供支持，如 Python、Ruby、Haskell 等，使其成为一个强大的开发和测试平台。

NixOS 提供了一些其他特性，显著提高了系统的可扩展性、灵活性、效率和安全性，同时提供更完善的用户体验。

首先，NixOS 的模块化设计是一大亮点。通过导入其他模块如 nixpkgs、home-manager、nixos-hardware 等，用户可以轻松扩展或修改系统配置，满足个性化需求。这种设计使得 NixOS 具有强大的可扩展性和灵活性，可以根据用户需求定制系统。例如，若需安装新的软件包，只需导入 nixpkgs 模块即可在系统中安装所需软件包。此外，NixOS 还支持模块的继承和复用，提高用户使用和管理模块的效率。

其次，NixOS 的自动化生成器是一项福音。通过生成器自动产生配置文件，如硬件检测，大大提高了用户效率和系统稳定性。例如，运行 NixOS 的硬件检测生成器，自动检测系统硬件并生成相应配置文件。

第三，NixOS 的虚拟化功能全面强大。通过工具如 nixos-container、nixops、lorri 等，用户能轻松创建和管理虚拟机或容器，提高系统可扩展性和可靠性。例如，通过 nixos-container 工具，用户可轻松创建和管理容器，将应用程序隔离，提高系统安全性和可靠性。此外，NixOS 还支持虚拟机和容器的快照、回滚、复制和迁移，方便管理虚拟化环境。

最后，NixOS 的安全机制是一道坚实的防线。包括沙盒、隔离、加密等机制，使得 NixOS 在网络安全、数据保护

等方面具有强大的实用性和可靠性。例如，支持沙盒化应用程序，有效防止应用程序对系统和其他应用程序的影响。此外，支持网络隔离、加密文件系统、SELinux 等功能，使 NixOS 成为一个安全可靠的操作系统。

综上所述，NixOS 以卓越性能和高质量在操作系统领域占据重要地位。无论是个人电脑、服务器还是嵌入式设备，NixOS 具有广泛适用性和应用价值。其模块化设计、自动化生成器、虚拟化功能和安全机制为用户提供强大、灵活、高效和安全的系统管理方式，轻松管理系统、提高工作效率、保护数据安全。对于追求高效、可靠、安全操作系统的用户来说，NixOS 是一个值得考虑的选择。

## 第一步：获取 NixOS ISO

### 下载 NixOS ISO

获取 NixOS ISO 镜像文件的方式繁多，从 NixOS 官方网站获取是一种简单而高效的方法。NixOS 提供多种版本，包括不同架构的版本如 x86_64 和 aarch64，以及适用于不同运行环境的版本，如 Linux、Windows（WSL2）和 Docker。同时，还提供了 VirtualBox 镜像文件和适用于 Amazon EC2 部署的预生成 AMI 镜像文件。下载前，可根据硬件配置和需求选择合适的版本和架构。

由于网络限制或其他原因，下载速度可能较慢。考虑从 [mirrorz](https://mirrorz.org/os/NixOS) 获取 NixOS ISO 镜像文件。

### 验证 NixOS ISO

下载完成后，为确保 NixOS ISO 文件完整性，需进行验证，以避免下载过程中的错误或损坏。验证 ISO 的完整性可通过比较下载的 ISO 文件的哈希值与官方提供的哈希值。在 NixOS 官网下载页面上，可找到每个版本对应的哈希值。使用 sha256sum 工具计算 ISO 文件的哈希值，命令如下：

```bash
echo "6285cc9161b02b093849f27c50bdea06a495bcb1d61881ba977bd11625972ad6  nixos-plasma5-23.05.2403.d57e8c535d4-x86_64-linux.iso" | sha256sum --check
```

其中 "6285cc9161b02b093849f27c50bdea06a495bcb1d61881ba977bd11625972ad6" 是官方提供 ISO 文件的哈希值，"nixos-plasma5-23.05.2403.d57e8c535d4-x86_64-linux.iso" 是 ISO 文件的文件名。比较计算出的哈希值与官方提供的哈希值，若一致，说明 ISO 文件完整无误。

以上是获取 NixOS ISO 镜像文件的方式以及验证 ISO 文件完整性的步骤。在下载和验证 ISO 文件时，需注意选择合适的版本和架构，并确保哈希值一致，以保证安装过程的顺利进行。

## 第二步：制作安装介质

制作安装介质有多种方式，选择取决于使用的操作系统和硬件设备。一般而言，可以使用光盘、U盘或网络启动等方式安装 NixOS。以下将详细介绍使用光盘、U盘刻录的具体步骤，以确保在安装过程中体验顺畅。

### 刻录光盘

使用光盘进行安装是一种常见而古老的方式。如果有可刻录的光盘和光驱，是一种适合的选择。首先，获取并下载 ISO 文件，随后将其刻录到光盘上。将光盘插入目标机器的光驱，重启机器并从光盘启动。

光盘刻录方法有很多，不同操作系统和软件可能有不同的界面和操作。通常，选择刻录软件如 Nero、ImgBurn、Brasero 等，选择刻录模式为“写入映像文件”或“写入 ISO 文件”，将下载的 ISO 文件作为源文件，选择空白的光盘作为目标设备，然后开始刻录。

刻录完成后，在目标机器上进行测试，确保光盘能够正常启动。如果不能启动，可能是光盘质量、刻录速度或刻录软件设置等原因。可尝试更换光盘或调整刻录参数重新刻录，确保安装过程顺利进行。

### 刻录U盘

与光盘相比，使用U盘进行安装是更现代的方式。如果你有一个空闲的U盘，将下载的ISO文件写入U盘非常适合。你需要准备一个写入工具，选择下载的ISO文件作为源文件，选择空闲的U盘作为目标设备，然后开始写入。

不同平台的写入工具有所不同：

#### 多平台

- **Ventoy**
  - Ventoy是一个开源工具，支持多种操作系统，包括Windows和Linux。它提供许多功能，如Linux持久性支持、Windows自动安装支持、变量扩展支持等。Ventoy支持多种架构，包括x86 Legacy BIOS、IA32 UEFI、x86_64 UEFI、ARM64 UEFI和MIPS64EL UEFI。经过严格测试，支持超过1100个ISO文件和Linux vDisk启动解决方案，并支持在ListView和TreeView模式之间动态切换。

- **USBImager**
  - USBImager是一个简单易用的GUI应用程序，用于将压缩的磁盘映像写入USB驱动器并创建备份。支持Windows、MacOS和Linux平台，无需安装，只需解压存档即可使用。界面简单，只包含必要的功能，不依赖于其他软件。

- **balenaEtcher**
  - balenaEtcher是一款免费开源的图像刻录器，支持Windows、macOS和GNU/Linux。它能轻松快速地刻录USB和SD卡图像，提供直观的3步流程，不需要任何命令行。具有确认闪存完成、默认隐藏系统驱动器等实用功能，建立在开源工具上，包括JS、HTML、node.js和Electron。

#### Windows

- **Rufus**
  - Rufus是一款功能丰富、操作简便的软件，可帮助用户快速创建可引导的USB启动盘。支持Windows、Linux、UEFI等操作系统的ISO文件创建USB安装媒体。提供支持多种语言、操作系统、文件格式、启动模式和分区方案等功能。简单易用的界面和高效的功能使用户能够在短时间内完成启动盘的创建。

#### Linux

- **Impression**
  - [Impression](https://gitlab.com/adhami3310/Impression)是一个现代、简约的应用程序，用于创建可启动驱动器。通过Impression，您可以轻松将磁盘映像刷入您的驱动器，创建一个全新的引导系统。Impression性能卓越，采用先进的数据转换技术，确保映像的完整性和准确性。在[Flathub](https://flathub.org/apps/io.gitlab.adhami3310.Impression)上，您可以找到更多关于Impression的信息和使用技巧。

![Impression](/images/Impression.png)

写入完成后，请在目标机器上进行测试，确保U盘能够正常启动。如果无法启动，可能是U盘格式不支持或写入软件设置不正确等原因。尝试格式化U盘或调整写入参数重新写入，确保安装过程顺利。

为确保安装过程顺利，还需确保使用的介质质量良好，无损坏或错误。在实践过程中，多加留意，确保安装过程流畅。

## 第三步：设置网络

在NixOS的安装过程中，网络连接是至关重要的，因为需要下载大量软件包。在安装之前，请确保电脑成功连接到互联网。为保持网络连接稳定，优化网络设置可使安装过程更顺畅。

若网络环境不理想，使用局域网网络代理是不错的选择。通过`export all_proxy`命令设置代理，如`export all_proxy=http://127.0.0.1:7890`。代理服务器地址和端口号根据实际环境设置。

对于更详细的网络代理设置，可使用proxychains工具。它支持配置自定义代理设置，提供多种代理类型支持，如SOCKS4、SOCKS5、HTTP等。通过proxychains，可实现网络流量的分流和转发，保护网络安全和隐私。

在进行NixOS安装前，确保成功连接互联网，并根据需要进行网络设置。通过上述网络设置方法，相信您能更灵活、高效地进行网络连接，并在NixOS使用中得心应手。

## 第四步：分区

在即将开始讨论NixOS的安装之前，请耐心听我详细叙述分区的重要性。分区在计算机存储管理中扮演不可替代的关键角色，将硬盘分成若干独立而协调的部分，确保每个部分能够独立管理和利用。这一步骤的重要性不可小觑，因为它能更出色地管理和保护您的珍贵数据。

对于熟悉Linux的用户，分区是操作系统安装中迫切的步骤。我将介绍四种不同的分区工具：fdisk、cfdisk、parted和gparted。这些工具各自擅长基础分区操作，但它们的使用方式各有千秋。在安装NixOS之前，我强烈建议您先学习这些工具的使用方法，以便在硬盘事务中游刃有余。

对于对Btrfs文件系统充满兴趣的用户，特别推荐查看[Btrfs](https://nixos.wiki/wiki/Btrfs)的Wiki介绍。Btrfs作为现代文件系统，具有诸多吸引人的特性，如快照、可靠的数据校验和动态分区。这种新型文件系统不仅提供更强大而灵活的数据管理能力，还像一把坚盾，保护您的数据免受突如其来的破坏。在这个启迪性的教程中，您将学到如何使用Btrfs进行分区和子卷设置，深刻理解和充分运用Btrfs的精髓。

以上是一部分分区操作介绍，希望您在学习这些操作时能深刻理解分区的重要性，并在未来的数据管理中得心应手。

## 第五步：生成初始配置

完成NixOS分区后，将分区挂载到/mnt目录下并生成初始配置文件至关重要。这个过程直接影响系统的运行和配置。使用`nixos-generate-config --root /mnt`命令可以轻松生成两个文件：`hardware-configuration.nix`和`configuration.nix`。

`hardware-configuration.nix`由系统自动生成，包含与硬件相关的配置，如分区、文件系统、内核模块等。通常情况下，您无需修改此文件，因为NixOS已经默认配置好。

而`configuration.nix`是NixOS的主要配置文件，定义了系统的运行方式。这个文件非常重要，包含了NixOS的所有配置和管理选项。通过深入研究和理解这个文件，您可以更好地掌握NixOS的配置和管理技巧。在这个文件中，您可以设置诸如时区、语言、用户、密码等选项。

以下是一些配置示例：

```Nix
time = {
  timeZone = "Asia/Shanghai";
  hardwareClockInLocalTime = true;
};

i18n = {
  defaultLocale = "zh_CN.UTF-8";
  supportedLocales = [ "zh_CN.UTF-8/UTF-8" "en_US.UTF-8/UTF-8" ];
  inputMethod = {
    enabled = "fcitx5";
    fcitx5.addons = with pkgs; [fcitx5-with-addons fcitx5-chinese-addons];
  };
};

users.users.<用户名> = {
  isNormalUser = true;
  initialPassword = "<密码>";
};

programs = {
  zsh = {
    enable = true;
    enableCompletion = true;
    autosuggestions = {
      enable = true;
      strategy = [ "history" ];
    };
  };
};

boot = {
  kernelPackages = pkgs.linuxPackages_zen;
  loader = {
    systemd-boot = {
      enable = true;
      configurationLimit = 5;
    };
    efi = {
      canTouchEfiVariables = true;
      efiSysMountPoint = "/boot";
    };
  };
  kernelParams = [
    "amd_pstate=passive"
    "radeon.dpm=0"
    "nvidia-drm.modeset=1"
    "NVreg_PreserveVideoMemoryAllocations=1"
  ];
  consoleLogLevel = 3;
  initrd.kernelModules = ["btrfs"];
  kernelModules = [
    "v4l2loopback"
    "amdgpu"
    "nvidia"
    "nvidia_drm"
    "nvidia_uvm"
    "nvidia_modeset"
  ];
  extraModulePackages = [
    pkgs.linuxKernel.packages.linux_zen.v4l2loopback
  ];
  extraModprobeConfig = ''
    options v4l2loopback exclusive_caps=1 video_nr=9 card_label="obs"
  '';
};
```

这些代码只是示例，需根据实际情况进行修改。

在NixOS的配置文件中，还可以设置其他选项，如：

- `boot.loader.grub.timeout`：设置GRUB启动菜单超时时间。
- `networking.hostName`：设置主机名。
- `services.xserver.enable`：启用X Window System服务。

通过这些选项，您可以更好地掌握NixOS的配置和管理技巧。

## 第六步：安装

接下来，了解`nixos-install --flake`命令的作用和语法。该命令根据`configuration.nix`文件自动下载和安装所需软件包，并安装引导程序。这个过程可能需要时间，因为NixOS需要从网络上下载大量软件包。然而，这个过程的好处是可以方便快捷地从网络上下载并安装所需的软件包。若想深入了解`nixos-install`命令，可以使用`--show-trace`选项查看运行过程，以对命令的每个细节有更深入的了解和研究。

最后，注意一些实用技巧和注意

事项。在安装过程中，保持耐心和专注，不断尝试和实践，以更好地掌握和应用。如果遇到问题，可以使用`nixos-rebuild switch --rollback`命令回滚到之前的系统状态，以更好地排除问题。此外，可以使用`nix-shell`命令创建沙箱环境，更好地测试和运行代码。

总的来说，在探索NixOS的过程中，需要保持耐心和专注，不断尝试和实践，以更好地掌握和应用。在软件开发或系统管理方面，NixOS是一个非常有用而强大的工具，因此需要深入了解，以更好地应用于职业生涯中。

## 第七步：重启

安装NixOS完成后，紧接着的重启过程至关重要。拔出安装介质后，需要重启电脑以让新的操作系统生效。通过这一步骤，可以立即体验NixOS的卓越表现，并享受其出色的性能、稳定性和安全性。

需要注意，重启过程并非一成不变。根据个人需求，可以选择不同的重启方式，以达到最佳效果。例如，如果希望更高效地管理系统资源，可以使用命令行工具进行重启。

在重启过程中，务必注意保护个人数据和隐私，以免不必要的损失。建议在重启前备份重要文件，以便在意外发生时能够快速恢复数据。此外，可以使用加密技术保护数据，确保个人信息得到最大限度的安全保护，提高数据安全性。

总的来说，重启是NixOS使用过程中不可或缺的一步。通过选择适合自己的重启方式，可以更好地管理电脑系统，更好地享受NixOS的卓越表现。