---
layout: post
title: Install ROS on Intel Edison
comments: true
categories: ROS
tags: ROS Edison 
---

## 准备工作
1. 下载Edison驱动，直接从官网下载：[https://software.intel.com/zh-cn/iot/hardware/edison/downloads](https://software.intel.com/zh-cn/iot/hardware/edison/downloads)
2. 下载ublinux镜像。[http://www.emutexlabs.com/files/ubilinux/ubilinux-edison-150309.tar.gz](http://www.emutexlabs.com/files/ubilinux/ubilinux-edison-150309.tar.gz "ublinux")
3. 下载dfu-til工具。[http://dfu-util.sourceforge.net/releases/dfu-util-0.9-win64.zip](http://dfu-util.sourceforge.net/releases/dfu-util-0.9-win64.zip)

## 烧写镜像
1. 解压dfu-util工具，加入到环境变量PATH中。
2. 解压ublinux镜像，目录名是toFlash。运行目录中的flashall.sh。如果没提示找不到dfu-util，则会烧写正常。大概10分钟左右，耐心等待。

## 登录
烧写完成后，会自动重启。然后用串口连接Edison。默认是115100/8/N/1。

* 管理员是root，密码是edison。
* 普通用户是edison，密码也是edison。

## 连接WiFi
vim /etc/network/interfaces

![Picture](http://craftor.qiniudn.com/image/blog/change_interfaces_file_2.png)

修改内容如上图，填入你自己WiFi的信息。保存之后，reboot一下。应该可以看到，Edison已经自动连上你家的WiFi了。

这时候就可以apt-get了。

## 安装ROS

* 先安装一些工具

```bash
apt-get -y install git
apt-get -y install sudo less
pip install -U wstool
```

* 添加一个用户

```bash
adduser craftor
usermod -aG sudo craftor
usermod -aG dialout craftor
```

* 从root退出，再用自己的用户名(craftor)登录

```bash
git clone https://github.com/UAVenture/ros-setups
cd ros-setups/intel-edison/
./install_ros.sh
```
