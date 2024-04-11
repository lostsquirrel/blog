---
title: "记一次在 ubuntu 升级 nvidia 驱动翻车事件"
date: 2024-04-10T17:48:55+08:00
summary: ubuntu 22.04 升级 nvidia 驱动时出错，导致桌面无法正常启动，并且网络故障
tags:

- ubuntu
- nvidia
- driver
categories:
- troubleshot
- ops
draft: false
---

## 起经

- 第一阶段是受够了 `gnome-shell` 进程吃了大量内存，还时不时导致切换窗口卡顿，禁用所有插件，包括系统插件之后改善不明显，所以将桌面切换到 `Wayland`
- 第二阶段切换之后，卡顿问题是得到改善了，但 `vscode` 会时不时出现黑屏，闪烁的情况，网上找了些资料猜测可能是驱动问题，就决定升级驱动

## 经过

```bash
sudo ubuntu-drivers list
sudo ubuntu-drivers install

 
```

安装过程提示编译过程中找不到 `6.5.0-27` 版本的 `header`(具体记不得了), 这就是问题的来源，由于之前有过系统升级，内核版本从 `6.5.0-26` 升级到了
`6.5.0-27` 但因为没有重启所以 `uname -r` 看到的版本还是 `6.5.0-26`， 而驱动安装的则是 `6.5.0-27` 导致编译失败。

重启，无法进入桌面，进入命令行

开始修复

```bash
apt reinstall nvidia-dkms-550  # 
apt reinstall nvidia-driver-550
apt reinstall nvidia-kernel-common-550
apt reinstall nvidia-kernel-source-550
apt reinstall nvidia-utils-550
apt remove nvidia-driver-550 nvidia-dkms-550
apt remove nvidia-compute-utils-550 nvidia-firmware-550-550.67 nvidia-kernel-common-550 nvidia-kernel-source-550 nvidia-utils-550


```
跟 vboxdrv 的日志提示这个包有问题,所以重新安装
```sh
apt reinstall linux-headers-6.5.0-27-generic
```

安装后成功进入系统，但网卡找不到，无法上网，再次网上找资料，这次比较顺序，找到说要安装这个包，
```sh
apt install linux-modules-extra-6.5.0-27-generic
```
安装完成，网络正常。

## 结果

1. 黑屏和闪烁问题还要进一步观察
2. 内核升级要重启