---
title: 一次移动分区的尝试
keywords: Btrfs,partition
abbrlink: 8da451c
date: 2023-03-31 21:03:51
updated: 2023-03-31 21:03:51
tags:
  - Linux
  - Btrfs
  - partition
---
<!-- more -->

一开始是在安装了 Arch 的硬盘上存在 Windows，后来删除了 Windows。由于 Arch 位于硬盘末尾，不能向右扩展，这意味着原来 Windows 占用的空间无法使用。

![partition](/images/partition1.png)

Arch 的分区格式是 Btrfs。如果要使用这些空间，可以新建一个分区组成 RAID 0，这样比较容易，但最终仍然会有两个分区。如果只需要一个分区，可以在左侧新建一个分区（新分区不能小于原分区），然后将数据移动过去。

关机，进入 Arch 的启动盘，将 Arch 分区挂载到 `/mnt` 下。

```bash
mount /dev/nvme0n1p3 /mnt
```

在移动数据之前，可以先减小 Btrfs 的大小（可以先删除快照）。

```bash
resize2fs /dev/nvme0n1p3
```

将数据移动到左侧的分区。

```bash
btrfs replace start /dev/nvme0n1p3 /dev/nvme0n1p2 /mnt
```

然后重新挂载分区，生成 fstab。同时，引导的启动分区也需要修改。

为新分区设置标签：

```bash
btrfs filesystem label /dev/nvme0n1p2 arch_os
```

修改 arch.conf：

```bash
options root="LABEL=arch_os" rw
```

重新启动。

如果分区大小不正确：

```bash
btrfs rescue fix-device-size /dev/nvme0n1p2
```