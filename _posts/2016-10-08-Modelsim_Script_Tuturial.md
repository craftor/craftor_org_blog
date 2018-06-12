---
layout: post
title: Modelsim脚本仿真教程
comments: true
categories: FPGA
tags: FPGA Modelsim
---

## 一、Modelsim的安装

这里不再赘述，但要注意一点，要将modelsim目录下的win64或win32目录加到环境变量PATH。

检测有没有设置成功的办法：在桌面任意位置，按住Shift+鼠标右键，然后选择“在此处打开命令窗口”。

输入vsim，如果能运行modelsim，则说明配置成功。

## 二、脚本编写

以[IcarusVerilog教程](http://craftor.org/2016/03/15/Icarus_Verilog/)
里的led.v和tb.v代码为例。

在同一个文件夹下，新建一个sim.tcl脚本文件，内容如下：

```tcl
vlib work
vmap work
vlog *.v
vsim -L work work.tb -t 1ns
add wave *
run -all
```

解释如下：

脚本|解释|
:----------|:------
vlib work| 新建一个名为work的库,modelsim默认要求
vmap work| 将work指定为编译的目标库
vlog \*.v | vlog是verilog编译工具，就是将目录下所有.v文件都编译一遍；也可以分开多行，将verilog文件一个一个按顺序编译
vsim -L work work.tb -t 1ns | vsim表示启动modelsim程序，-L work是指定包含work库(默认)；如果使用厂家的库，比如altera的CycloneIV器件，再增加 -L cycloneiv即可； work.tb 表示指定work库中的tb为顶层模块进行仿真；-t 1ns，表示指定时间单位为1ns
add wave * | 默认表示将top module下的信号全部添加到波形中显示；如果要再添加下一层的信号：add wave uut/* 这样写即可；add wave 可以写多行，分多次添加。
run -all | 表示运行脚本，直到遇到$finish之类的指令才停止，否则需要手动在modelsim里按暂停按钮。

编写好之后，然后在文件夹下，Shift+右键，打开命令行：

```
vsim -do sim.tcl
```

会弹出modelsim界面，并自动运行。弹出是否Finish，选择否。

![](http://qiniu.craftor.org/2016-0638ac7e.png-top.left)

然后在Modelsim中，应该停留在如下界面：

![](http://qiniu.craftor.org/2016-86b1bf3f.png-center)

同时，波形文件也弹出来了：

![](http://qiniu.craftor.org/2016-493f85b4.png-center)

### 三、其它情况

1. 如果不能启动Modelsim，请检查modelsim/win64目录是否在环境变量PATH中。

2. 如果能启动Modelsim，但是编译有错误，请检查verilog文件或tcl脚本。修改后，直接在modelsim中，输入do sim.tcl（或按下键盘上的↑键）即可。

3. 若全部正确，需要修改verilog代码，直接改完后，在modelsim再输入do sim.tcl即可。
