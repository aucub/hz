---
title: 一次移动分区的尝试
tags:
  - Btrfs
  - partition
keywords: Btrfs,partition
date: 2023-03-31 21:03:51
updated: 2023-03-31 21:03:51
---
<!-- more -->

一开始是在存在Windows的硬盘安装的Arch,后来删除了Windows,由于Arch处在硬盘末尾不能向右扩展了，意味着原来Windows的空间无法使用。

![partition](/images/partition1.png)

Arch分区格式是Btrfs,如果要使用这些空间，可以新建一个分区，组成raid0,这样比较容易，但是还是两个分区。只需要一个分区的话，我们可以在左边新建一个分区(新分区不能小于原分区)，把数据移动过去。

关机，进入Arch的启动盘，把Arch分区挂载到/mnt下。

```bash
mount /dev/nvme0n1p3 /mnt
```

移动数据前我们可以先减小btrfs的大小（可以先把快照删掉）

```bash
resize2fs /dev/nvme0n1p3
```

把数据移动到左边的分区

```bash
btrfs replace start  /dev/nvme0n1p3  /dev/nvme0n1p2   /mnt
```

然后重新挂载分区，生成fstab,引导的启动分区也要修改

为新分区设置标签：

```bash
btrfs filesystem label /dev/nvme0n1p2 arch_os
```

修改arch.conf

```bash
options root="LABEL=arch_os" rw
```

重新启动

分区大小不正确

```bash
btrfs rescue fix-device-size /dev/nvme0n1p2
```