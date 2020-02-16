---
title: mac 电脑支持 NTFS
abbrlink: 2922130013
date: 2020-02-01 00:35:04
tags:
---

## 让 mac 快速支持 NTFS， 无需安装任何软件

<!-- more -->

### 开启流程简介

1. 挂载上你的 NTFS 硬盘，查看硬盘名称
2. 编辑/etc/fstab 文件，使其支持 NTFS 写入
3. 将/Volumes 中的 NTFS 磁盘快捷方式到 Finder

### 详细流程

1. 插上硬盘后，查看你的硬盘名称，这里假设名称是 AngleDisk，牢记之（你的可不是这个呀！！
2. 打开 Applications 的 Terminal, 你也可以直接 spotlight 输入 terminal 打开
3. 在终端输入 sudo nano /etc/fstab 敲击回车
4. 现在你看到了一个编辑界面，输入 LABEL=AngleDisk none ntfs rw,auto,nobrowse 后，敲击回车，再 Ctrl+X，再敲击 Y，再敲击回车
5. 此时，退出你的移动硬盘，再重新插入，你会发现磁盘没有显示在桌面或是 Finder 之前出现的地方，别慌
6. 打开 Finder，Command+Shift+G，输入框中输入/Volumes，回车，你就可以看到你的磁盘啦！是可以读写的
7. 方便起见，你可以直接把磁盘拖到 Finder 侧边栏中，这样下次使用就不用进入到/Volumes 目录打开了
