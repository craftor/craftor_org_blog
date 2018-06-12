---
layout: post
title: Theano安装教程
comments: true
categories: DeepLearning
tags: pip python theano
---

Theano的安装比较麻烦，依赖的东西比较多，这里记录一下安装的过程。

### 一、软件安装

1. 安装
[Python2.7 64位](https://www.python.org/ftp/python/2.7.12/python-2.7.12.amd64.msi)
（安装到 C:\Python27 目录下）

2. 安装
[TDM-GCC(64bit)](http://101.96.10.41/nchc.dl.sourceforge.net/project/tdm-gcc/TDM-GCCInstaller/tdm64-gcc-5.1.0-2.exe)
（安装到 C:\TDM-GCC-64 目录下）

3. 安装
[CUDA 64bit](http://developer.download.nvidia.com/compute/cuda/5_5/rel/installers/cuda_5.5.20_winvista_win7_win8_general_64.exe)
（如果没有支持CUDA的显卡，可以不安装）

4. 安装
[Visual C++ Compiler for Python27](https://download.microsoft.com/download/7/9/6/796EF2E4-801B-4FC4-AB28-B59FBF6D907B/VCForPython27.msi)

### 二、配置

1. 配置环境变量，在系统环境变量里添加
```
C:\Python27;C:\Python27\Scripts;C:\TDM-GCC-64\bin;
```
2. 修改pip的源，见[这里](http://craftor.org/2016/10/09/Python_Pip/)

### 三、安装

在桌面任意位置，按住Shift+鼠标右键，然后选择“在此处打开命令窗口”。

然后输入如下命令，即可一路安装成功。

```
pip install theano
```

如果遇到scipy库自动下载安装不成功，直接手动下载[scipy 64bit](http://www.lfd.uci.edu/~gohlke/pythonlibs/dp2ng7en/scipy-0.18.1-cp27-cp27m-win_amd64.whl
)

然后通过```pip install scipy-0.18.1-cp27-cp27m-win_amd64.whl```手动安装，然后再运行```pip install theano```

如果还有其它库无法自动安装的，直接去[这里](http://www.lfd.uci.edu/~gohlke/pythonlibs/)下载，然后手动安装。

### 四、其它

1. 等我装了CUDA显卡再来补充GPU的配置

2. 编辑器推荐使用Atom，编辑器中的战斗机
