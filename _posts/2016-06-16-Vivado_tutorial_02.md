---
layout: post
title: Vivado Turtorial 02 —— 使用vivado中波形仿真
comments: true
categories: FPGA
tags: FPGA Vivado
---

1.编写如下源代码

```verilog
`timescale 1ns / 1ps

module top(
    input        clk,
    input        rst,
    output       test_clk,
    input  [1:0] switch,

    output [3:0] r,g,b,
    output       hs,vs

    );

wire clk40M, clk25M;
// rst = 0
assign test_clk = (rst) ? clk40M : clk25M;

// 100MHz    x10  --> 1000MHz
// 800~1600,  
  PLLE2_BASE #(
      .BANDWIDTH("OPTIMIZED"),  // OPTIMIZED, HIGH, LOW
      .CLKFBOUT_MULT(10),        // Multiply value for all CLKOUT, (2-64)
      .CLKFBOUT_PHASE(0.0),     // Phase offset in degrees of CLKFB, (-360.000-360.000).
      .CLKIN1_PERIOD(10.0),      // Input clock period in ns to ps resolution (i.e. 33.333 is 30 MHz).
      // CLKOUT0_DIVIDE - CLKOUT5_DIVIDE: Divide amount for each CLKOUT (1-128)
      .CLKOUT0_DIVIDE(25), // 40M
      .CLKOUT1_DIVIDE(40), // 25M
      .CLKOUT2_DIVIDE(1),
      .CLKOUT3_DIVIDE(1),
      .CLKOUT4_DIVIDE(1),
      .CLKOUT5_DIVIDE(1),
      // CLKOUT0_DUTY_CYCLE - CLKOUT5_DUTY_CYCLE: Duty cycle for each CLKOUT (0.001-0.999).
      .CLKOUT0_DUTY_CYCLE(0.5),
      .CLKOUT1_DUTY_CYCLE(0.5),
      .CLKOUT2_DUTY_CYCLE(0.5),
      .CLKOUT3_DUTY_CYCLE(0.5),
      .CLKOUT4_DUTY_CYCLE(0.5),
      .CLKOUT5_DUTY_CYCLE(0.5),
      // CLKOUT0_PHASE - CLKOUT5_PHASE: Phase offset for each CLKOUT (-360.000-360.000).
      .CLKOUT0_PHASE(0.0),
      .CLKOUT1_PHASE(0.0),
      .CLKOUT2_PHASE(0.0),
      .CLKOUT3_PHASE(0.0),
      .CLKOUT4_PHASE(0.0),
      .CLKOUT5_PHASE(0.0),
      .DIVCLK_DIVIDE(1),        // Master division value, (1-56)
      .REF_JITTER1(0.0),        // Reference input jitter in UI, (0.000-0.999).
      .STARTUP_WAIT("FALSE")    // Delay DONE until PLL Locks, ("TRUE"/"FALSE")
   )
   PLLE2_BASE_inst (
      // Clock Outputs: 1-bit (each) output: User configurable clock outputs
      .CLKOUT0(clk40M),   // 1-bit output: CLKOUT0
      .CLKOUT1(clk25M),   // 1-bit output: CLKOUT1
      .CLKOUT2(CLKOUT2),   // 1-bit output: CLKOUT2
      .CLKOUT3(CLKOUT3),   // 1-bit output: CLKOUT3
      .CLKOUT4(CLKOUT4),   // 1-bit output: CLKOUT4
      .CLKOUT5(CLKOUT5),   // 1-bit output: CLKOUT5
      // Feedback Clocks: 1-bit (each) output: Clock feedback ports
      .CLKFBOUT(clkfb), // 1-bit output: Feedback clock
      .LOCKED(LOCKED),     // 1-bit output: LOCK
      .CLKIN1(clk),     // 1-bit input: Input clock              100M
      // Control Ports: 1-bit (each) input: PLL control ports
      .PWRDWN(1'b0),     // 1-bit input: Power-down
      .RST(1'b0),           // 1-bit input: Reset
      // Feedback Clocks: 1-bit (each) input: Clock feedback ports
      .CLKFBIN(clkfb)    // 1-bit input: Feedback clock
   );

wire vga_clk = clk40M;

//800x600
parameter H_END = 10'd800;
parameter V_END = 10'd600;
reg [9:0] h_cnt = 0;
reg [9:0] v_cnt = 0;

always@(posedge vga_clk or posedge rst) begin
    if (rst) begin
        h_cnt <= 10'b0;
        v_cnt <= 10'b0;
    end
    else if (h_cnt==H_END) begin
        h_cnt <= 10'b0;
        if(v_cnt == V_END)
            v_cnt <= 10'b0;
        else
            v_cnt <= v_cnt + 1'b1;
    end
    else
        h_cnt <= h_cnt + 1'b1;
end
assign hs = (h_cnt==H_END);
assign vs = (v_cnt==V_END);

endmodule
```

2.添加testbench。选择Add soruces -> Add or create simulation sources

![picture](http://craftor.qiniudn.com/image/blog/20160616/31.png-center)

3.Create File，然后输入tb，点OK

![picture](http://craftor.qiniudn.com/image/blog/20160616/32.png-center)

4.编写testbench代码，如下：

```verilog
`timescale 1ns / 1ps

module tb();

reg clk100M;
reg rst;
wire test_clk;
wire hs,vs;

top uut(
.clk(clk100M),
.rst(rst),
.test_clk(test_clk),
.switch(),
.r(),
.g(),
.b(),
.hs(hs),
.vs(vs)
);

initial begin
    clk100M = 0;
    forever begin
    #5 clk100M=~clk100M;
    end
end

initial begin
    rst = 1;
    #100;
    rst = 0;
    #100000 $finish;
end

endmodule

```

5.选择Simulation-> Run Behavioral Simulation

![picture](http://craftor.qiniudn.com/image/blog/20160616/33.png-center)

完了之后，可以看到类似下面的界面：

![picture](http://craftor.qiniudn.com/image/blog/20160616/34.png-center)

6.添加要观察的信号到波形中。选中uut，可以看到uut中的信号都列出来了

![picture](http://craftor.qiniudn.com/image/blog/20160616/35.png-center)

选中要观察的信号，按shift或ctrl可以多选。右键->Add to Wave，即可。如图，添加了v_cnt和h_cnt两个信号。

![picture](http://craftor.qiniudn.com/image/blog/20160616/36.png-center)

7.点击图中的Run All，则运行仿真，直到弹出来仿真停止的位置

![picture](http://craftor.qiniudn.com/image/blog/20160616/37.png-center)

![picture](http://craftor.qiniudn.com/image/blog/20160616/38.png-center)

8.如果要继续仿真，再次点击Run All。要停止，按暂停

![picture](http://craftor.qiniudn.com/image/blog/20160616/39.png-center)

9.按过暂停后，可以修改top.v代码或tb.v代码。修改完后，要再次仿真，直接按Relaunch Simulation

![picture](http://craftor.qiniudn.com/image/blog/20160616/40.png-center)
