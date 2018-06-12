---
layout: post
title: Modelsim中编译Lattice库
comments: true
categories: FPGA
tags: Modelsim FPGA Lattice
---

### Step 1
1. 安装好modelsim，并将modelsim的目录添加到系统PATH中。
（确认方法：在任意位置同时按下Shift+鼠标右键，在出来的菜单里选择“在此处打开命令窗口”，然后输入vsim，如果能启动modelsim，说明设置正确。否则，要在系统的环境变量里添加modelsim目录下的win32或win64文件夹。）
2. 找到Modelsim安装目录，例如我电脑上目录是D:\modeltech64_10.2c
3. 找到Lattice Diamond目录，例如我电脑上是D:\Issac
4. 在modelsim目录下，新建一个文件夹，比如lattice，并进入此文件夹
5. 新建一个名为sim.tcl的文本文件，输入如下脚本：

```bash
set DIAMOND_PATH D:/lscc/diamond/3.7_x64/cae_library/simulation/verilog

vlib work
vlib ec
vlib ecp
vlib ecp2
vlib ecp3
vlib ecp5u
vlib lptm
vlib lptm2
vlib machxo
vlib machxo2
vlib machxo3l
vlib pmi
vlib sc
vlib scm
vlib xp
vlib xp2

vlog -work ec       $DIAMOND_PATH/ec/*.v
vlog -work ecp      $DIAMOND_PATH/ecp/*.v
vlog -work ecp2     $DIAMOND_PATH/ecp2/*.v
vlog -work ecp3     $DIAMOND_PATH/ecp3/*.v
vlog -work ecp5u    $DIAMOND_PATH/ecp5u/*.v
vlog -work lptm     $DIAMOND_PATH/lptm/*.v
vlog -work lptm2    $DIAMOND_PATH/lptm2/*.v
vlog -work machxo   $DIAMOND_PATH/machxo/*.v
vlog -work machxo2  $DIAMOND_PATH/machxo2/*.v
vlog -work machxo3l $DIAMOND_PATH/machxo3l/*.v
vlog -work pmi      $DIAMOND_PATH/pmi/*.v
vlog -work sc       $DIAMOND_PATH/sc/*.v
vlog -work scm      $DIAMOND_PATH/scm/*.v
vlog -work xp       $DIAMOND_PATH/xp/*.v
vlog -work xp2      $DIAMOND_PATH/xp2/*.v
```

注意
> Diamond目录要与自己电脑上的目录一致。
vlib后面的名称，除了work是额外的，其它与D:/lscc/diamond/3.7_x64/cae_library/simulation/verilog目录下的相对应

然后在lattice文件夹下，Shift+右键，选择“在此处打开命令窗口”，然后输入“vsim -do sim.tcl”
![](http://qiniu.craftor.org/2016-1c7ea621.png-blog)

如果没有错误，会看到modelsim一直在编译，直到结束。这时候，会在modelsim里看到lattice的库：
![](http://qiniu.craftor.org/2016-7cc59856.png-blog)

但是别急，还没有结束。

回到modesim主目录：D:\modeltech64_10.2c，找到modelsim.ini文件，右键->将“只读”属性去掉。
![](http://qiniu.craftor.org/2016-539f3946.png-blog)

然后再打开modelsim.ini，在[Library]这个标签后，添加如下（本人是添加在61行之后）：

```bash
ec       = $MODEL_TECH/../lattice/ec       
ecp      = $MODEL_TECH/../lattice/ecp      
ecp2     = $MODEL_TECH/../lattice/ecp2     
ecp3     = $MODEL_TECH/../lattice/ecp3     
ecp5u    = $MODEL_TECH/../lattice/ecp5u    
lptm     = $MODEL_TECH/../lattice/lptm     
lptm2    = $MODEL_TECH/../lattice/lptm2    
machxo   = $MODEL_TECH/../lattice/machxo   
machxo2  = $MODEL_TECH/../lattice/machxo2  
machxo3l = $MODEL_TECH/../lattice/machxo3l
pmi      = $MODEL_TECH/../lattice/pmi      
sc       = $MODEL_TECH/../lattice/sc       
scm      = $MODEL_TECH/../lattice/scm      
xp       = $MODEL_TECH/../lattice/xp       
xp2      = $MODEL_TECH/../lattice/xp2
```

保存modelsim.ini，然后关闭刚才打开的modelsim。从桌面快捷方式，运行modelsim，看到库的情况如下：

![](http://qiniu.craftor.org/2016-dbad423b.png-blog)

则说明，lattice库已经添加modelsim中。

>补充:在modelsim.ini里，搜索VoptFlow，将其值从1改为0。这样在仿真的时候，modelsim不会随便优化掉一些有用的信号。
