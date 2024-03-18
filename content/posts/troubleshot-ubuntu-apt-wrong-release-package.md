---
title: "解决 ubuntu 安装错误(更新)版本包引起的问题"
date: 2024-03-18T16:33:37+08:00
summary: 解决在 ubuntu xenial 误安装 focal 版本 docker-ce 版本引起 apt 无法使用的问题
tags:
- troubleshot
- ubuntu
- apt
- dpkg
categories:
draft: false
---

## 源头

更新一个比较有年头的服务的系统上的 `docker-compose` 使用了一个旧的脚本

```sh
sudo tee /etc/apt/sources.list.d/docker.list <<EOF
deb [arch=amd64] http://mirrors.com/docker-ce/linux/ubuntu focal stable
# deb-src [arch=amd64] http://mirrors.com/docker-ce/linux/ubuntu focal stable
EOF
```

由此导致 docker-ce 安装失败

```
# apt remove -y docker-ce
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following packages were automatically installed and are no longer required:
  aufs-tools cgroupfs-mount docker-ce-rootless-extras pigz
Use 'sudo apt autoremove' to remove them.
The following packages will be REMOVED:
  docker-ce
0 upgraded, 0 newly installed, 1 to remove and 0 not upgraded.
1 not fully installed or removed.
After this operation, 104 MB disk space will be freed.
(Reading database ... 107327 files and directories currently installed.)
Removing docker-ce (5:25.0.4-1~ubuntu.20.04~focal) ...
invoke-rc.d: syntax error: unknown option "--skip-systemd-native"
dpkg: error processing package docker-ce (--remove):
 subprocess installed pre-removal script returned error exit status 1
dpkg: error while cleaning up:
 subprocess installed post-installation script returned error exit status 1
Errors were encountered while processing:
 docker-ce
E: Sub-process /usr/bin/dpkg returned an error code (1)
```

找一些方案，也进行了些尝试, 比如

```sh
dpkg --purge --force-all docker-ce

apt install --reinstall init-system-helpers
```

仔细看了下报错信息

```
invoke-rc.d: syntax error: unknown option "--skip-systemd-native"
```

分析是新版本的 docker 卸载脚本中使用了一当前版本 `invoke-rc.d` 不支持的参数 `"--skip-systemd-native"`，
于是尝试 `grep -r skip-systemd-native /` 一直到问题解决都没出结果。

再寻找 `apt` `post scripts` 的位置， 从[这里](https://unix.stackexchange.com/questions/118587/subprocess-installed-pre-removal-script-returned-error-exit-status-1) 找到这个目录 `/var/lib/dpkg/info/`

在这个目录找到与 docker 有关的文件

```
docker-buildx-plugin.list
docker-buildx-plugin.md5sums
docker-ce-cli.list
docker-ce-cli.md5sums
docker-ce.conffiles
docker-ce.list
docker-ce.md5sums
docker-ce.postinst
docker-ce.postrm
docker-ce.preinst
docker-ce.prerm
docker-ce-rootless-extras.list
docker-ce-rootless-extras.md5sums
```

先看了 `docker-ce.postrm` 没有找到这个参数，继续看 `docker-ce.prerm`，找到这个参数，修改这个文件，备份该行，删除 `"--skip-systemd-native"`
执行

```sh
apt remove -y docker-ce
```
成功删除

