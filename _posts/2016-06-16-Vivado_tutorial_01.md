---
layout: post
title: Vivado Turtorial 01 —— 使用vivado中debug功能（类似ISE中ChipScope）
comments: true
categories: FPGA
tags: FPGA Vivado
---

1.基于BASYS3板子，有如下代码：

```verilog
module top(
    input        clk,
    input        rst,
    output       test_clk   
    );

parameter DIV_CNT = 2;

reg clk25M;
reg [31:0] cnt = 0;
always@(posedge clk)begin
    if (cnt==DIV_CNT-1)
        begin
            clk25M <= ~clk25M;
            cnt <= 0;
        end
    else
        begin
            cnt <= cnt + 1'b1;
        end
end
assign  test_clk = rst ? 1'b0 : clk25M;
```

管脚配置XDC文件内容如下：

```
set_property PACKAGE_PIN W5 [get_ports clk]
set_property IOSTANDARD LVCMOS33 [get_ports clk]
set_property PACKAGE_PIN V17 [get_ports rst]
set_property IOSTANDARD LVCMOS33 [get_ports rst]
set_property PACKAGE_PIN L1 [get_ports test_clk]
set_property IOSTANDARD LVCMOS33 [get_ports test_clk]
```

2.占击左侧Run Synthesis，综合

![Picture](http://craftor.qiniudn.com/image/blog/20160616/1.png-top.right)

3.完成之后，再点击 Open Synthesized Design，打开之后，点Tools下的Set Up Debug...

![Picture](http://craftor.qiniudn.com/image/blog/20160616/2.png-bottom.right)

如下

![Picture](http://craftor.qiniudn.com/image/blog/20160616/3.png-bottom.left)

4.选择Find Nets to Add...

![Picture](http://craftor.qiniudn.com/image/blog/20160616/4.png-center)

5.点OK

![Picture](http://craftor.qiniudn.com/image/blog/20160616/5.png-center)

6.选中想要观察的信号，点OK

![Picture](http://craftor.qiniudn.com/image/blog/20160616/6.png-center)

7.出现红色，在红色地方右键。

![Picture](http://craftor.qiniudn.com/image/blog/20160616/7.png-center)

8.选择Select Clock Domain
![Picture](http://craftor.qiniudn.com/image/blog/20160616/8.png-bottom.left)

9.选择ALL_CLOCK，然后选择clk_IBUF或clk_IBUF_BUFG

![Picture](http://craftor.qiniudn.com/image/blog/20160616/9.png-center)

10.选择合适的采集深度，1024通常够用

![Picture](http://craftor.qiniudn.com/image/blog/20160616/10.png-center)

11.Finish

![Picture](http://craftor.qiniudn.com/image/blog/20160616/11.png-center)

12.点击左侧的Generate Bitstream

![Picture](http://craftor.qiniudn.com/image/blog/20160616/12.png-bottom.middle)

13.完成后，点Open Hardware Manager

![Picture](http://craftor.qiniudn.com/image/blog/20160616/13.png-top.right)

14.将板子连接到电脑上，然后Open Target -> Auto Connect

![Picture](http://craftor.qiniudn.com/image/blog/20160616/14.png-bottom.left)

15.在xc7a35t上面，右键->Program Device...

![Picture](http://craftor.qiniudn.com/image/blog/20160616/15.png-bottom.right)

16.Program

![Picture](http://craftor.qiniudn.com/image/blog/20160616/16.png-bottom.middle)

17.点击Trigger

![Picture](http://craftor.qiniudn.com/image/blog/20160616/17.png-bottom.right)

18.会自动弹出波形

![Picture](http://craftor.qiniudn.com/image/blog/20160616/18.png-bottom.left)

19.此时，只有test_clk信号，没有rst信号。在Debug Probes区域中，rst_IBUF上右键

![Picture](http://craftor.qiniudn.com/image/blog/20160616/19.png-bottom.right)

20.Add Probes to Wave Form

![Picture](http://craftor.qiniudn.com/image/blog/20160616/20.png-bottom.left)

21.即可看到rst也在波形中了，添加其它信号类似

![Picture](http://craftor.qiniudn.com/image/blog/20160616/21.png-bottom.left)

22.鼠标点住rst_IBUF，拖拽到图中区域放开鼠标

![Picture](http://craftor.qiniudn.com/image/blog/20160616/22.png-bottom.left)

23.rst_IBUF信号会出现在框中

![Picture](http://craftor.qiniudn.com/image/blog/20160616/23.png-center)

24.点开Compare Value下拉菜单，设置如下，点击OK

![Picture](http://craftor.qiniudn.com/image/blog/20160616/24.png-bottom.right)

25.把BASYS3板子上的SW0，拔到上面。点击Run Trigger按钮

![Picture](http://craftor.qiniudn.com/image/blog/20160616/25.png-bottom.left)

26.注意到这里应该显示Wait...

![Picture](http://craftor.qiniudn.com/image/blog/20160616/26.png-bottom.left)

27.此时，在板子上，把SW0拔下来。注意到，这里会一闪而过Full，然后又显示Idle。如果没观察到，可以从25步骤再重来

![Picture](http://craftor.qiniudn.com/image/blog/20160616/27.png-bottom.left)

28.再打开波形，如下图

![Picture](http://craftor.qiniudn.com/image/blog/20160616/28.png-bottom.left)

29.回到这个界面，将Trigger Position 设置为500。再重复24-28步骤。然后再观察波形中，第500个周期，波形前后数据的变化

![Picture](http://craftor.qiniudn.com/image/blog/20160616/29.png-bottom.left)
