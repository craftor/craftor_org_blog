---
layout: post
title: Icarus Verilog 教程
comments: true
categories: FPGA
tags: FPGA Verilog
---

## 一、下载地址
[windows版](http://bleyer.org/icarus/iverilog-10.0-x86_setup.exe)

## 二、安装教程

### 2.1 按默认状态安装，一路next

> 注意：在win7以上系统，要右键“以管理员方式运行”。

### 2.2 安装到最后一页，注意两个√一定要打上。

![](http://qiniu.craftor.org/2016-34d3af8d.png-bottom.left)

### 2.3 检查一下是否安装正确

在桌面空白位置，按住Shift+鼠标右键，然后选择“在此处打开命令窗口”

![](http://qiniu.craftor.org/2016-c6c5282d.png-center)

然后输入iverlog，如果出现类似以下提示，说明iverilog安装成功。

![](http://qiniu.craftor.org/2016-d8f551ff.png-center)

再输入gtkwave，能弹出如下界面，说明查看波形的工具也安装成功。

![](http://qiniu.craftor.org/2016-cbd1286f.png-center)

## 三、使用教程

### 3.1 编写Verilog源文件

以下面的代码为例,保存为led.v文件。

```verilog
`timescale 1s/1s

module led (
	input   clk,
	input   rst_n,
	output  red,
	output  green,
	output  yellow
	);

reg[4:0] cnt;

always@(posedge clk or negedge rst_n)
begin
	if (!rst_n) begin
		cnt <= 8'h00;
	end else begin
		cnt <= cnt + 1'b1;
	end
end

parameter ST_IDLE = 3'b000;
parameter ST_R    = 3'b001;
parameter ST_G    = 3'b010;
parameter ST_Y    = 3'b100;
reg[2:0]  status;

always@(posedge clk or negedge rst_n)
begin
	if (!rst_n) begin
		status <= ST_IDLE;
	end else begin
		case (cnt)
		8'd00: begin status <= ST_G; end
		8'd15: begin status <= ST_Y; end
		8'd18: begin status <= ST_R; end
		endcase
	end
end

assign red    = (status==ST_R);
assign green  = (status==ST_G);
assign yellow = (status==ST_Y);

endmodule
```

### 3.2 编写TestBench文件

以下面代码为例，保存为tb.v，与led.v要存放在同一个文件夹下。

```verilog
module tb();

reg clk;
reg rst_n;
wire red,green,yellow;

led uut(
	.clk(clk),
	.rst_n(rst_n),
	.red(red),
	.green(green),
	.yellow(yellow)
	);

// generate clock
initial begin
	clk = 0;
	forever begin
		#10 clk = ~clk;
	end
end

// dump wave
initial begin
	$dumpfile("wave.vcd");
	$dumpvars(0,tb);
end

initial begin
	rst_n = 0;
	#25 rst_n = 1;
	#1000;
	$finish;
end

endmodule

```

### 3.3 编译

Shift+右键，打开命令行。输入如下命令：

```bash
iverilog -o led.out led.v tb.v
```

> led.out 是生成目标的文件名称，.v文件是所有必的源文件

回车运行完命令后，没有任何提示，则说明编译成功（如下图），否则要查看错误信息。

![](http://qiniu.craftor.org/2016-afba194d.png-center)

###  3.4 生成波形文件

在命令行中输入：

```bash
vvp led.out
```

> led.out 为上一步生成的目标文件。运行成功后，会生成.vcd的波形文件。

运行成功会提示如下：

![](http://qiniu.craftor.org/2016-2edb71c1.png-center)

### 3.5 查看波形

在命令行中输入：

```bash
gtkwave wave.vcd
```

> wave.vcd为上一步生成的波形文件

会弹出如下窗口，点击左侧tb旁边的+号，然后选择下一级uut。

![](http://qiniu.craftor.org/2016-8d48aca6.png-center)

然后按Shift全选如下信号，再占击Insert

![](http://qiniu.craftor.org/2016-eb119d84.png-top.left)

可以看到右侧黑框里出现了信号的波形：

![](http://qiniu.craftor.org/2016-87a839ff.png-center)

然后选择这里的放大、缩小按钮，就可以对波形进行缩放查看了：

![](http://qiniu.craftor.org/2016-bdbd0c02.png-center)

如果要修改代码，把gtkwave的窗口关闭，将以上过程（3.1-3.5）再来一遍即可。
