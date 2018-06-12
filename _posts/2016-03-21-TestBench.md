---
layout: post
title: 实例及Testbench的写法举例
comments: true
categories: FPGA
tags: FPGA Testbench 加法器 触发器 译码器
---

### 加法器

* 源代码

```verilog
module adder(
	input a,b,
	output q
);

assign q = a + b;

/*
a  b  q
0  0  0
0  1  1
1  0  1
1  1  0

*/

endmodule
```

* TestBench

```verilog
`timescale 1ns/1ps

module tb();

reg a1,b1;
wire q1;

adder uut(
	.a(a1),
	.b(b1),
	.q(q1)
	);

// dump wave
initial begin
	$dumpfile("wave.vcd");
	$dumpvars(0,tb);
end

initial begin
	a1 = 0;
	b1 = 0;
	# 10;
	a1 = 0;
	b1 = 1;
	# 10;
	a1 = 1;
	b1 = 0;
	# 10;
	a1 = 1;
	b1 = 1;
	# 10;
end

endmodule
```

### D触发器

* 源代码

```verilog
module dff(

	input      d,
	input      clk,
	output reg q

);

always@(posedge clk)
	q <= d;

endmodule
```

* TesbBench

```verilog
`timescale 1ns/1ps

module tb();

reg  d,clk;
wire q;

dff uut(
	.d  (d),
	.clk(clk),
	.q(q)
	);

// dump wave
initial begin
	$dumpfile("wave.vcd");
	$dumpvars(0,tb);
end

// generate clock
initial begin
	clk = 0;
	forever begin
		#10 clk = ~clk;
	end
end

initial begin
	d = 0;  // 0~20
	#20;
	d = 1;  // 20~40
	#20;
	d = 0;  // 40~90
	#50;
	$finish;
end

endmodule
```

### 计数器
带复位和清零

* 源代码

```verilog
module counter(
	input        clk,
	input        rst_n,
	output reg [7:0] cnt
);

always@(posedge clk or negedge rst_n) begin
	if (!rst_n)
		cnt <= 0;
	else if (cnt==8'd59)
		cnt <= 0;
	else
		cnt <= cnt + 1'b1;
end

endmodule
```

* TestBench

```verilog
`timescale 1ns/1ps

module tb();

reg        clk,rst_n;
wire [7:0] cnt;

counter uut(
	.clk(clk),
	.rst_n(rst_n),
	.cnt(cnt)
	);

// dump wave
initial begin
	$dumpfile("wave.vcd");
	$dumpvars(0,tb);
end

// generate clock
initial begin
	clk = 0;
	forever begin
		#10 clk = ~clk;
	end
end

//
initial begin
	rst_n = 0;
	# 15;
	rst_n = 1;
	# 500;
	$finish;
end

endmodule
```

### 3-8译码器

* 源代码

```verilog
module decoder3to8(

	input      [2:0] sel,
	output reg [7:0] y

);

always@(*) begin
	case(sel)
	3'b000: y = 8'h01;
	3'b001: y = 8'h02;
	3'b010: y = 8'h04;
	3'b011: y = 8'h08;
	3'b100: y = 8'h10;
	3'b101: y = 8'h20;
	3'b110: y = 8'h40;
	3'b111: y = 8'h80;
	default: y = 8'h00;
	endcase
end

endmodule
```

* TestBench

```verilog
`timescale 1ns/1ps

module tb();

reg  [2:0] sel;
wire [7:0] y;

decoder3to8 uut(
	.sel(sel),
	.y(y)
	);

// dump wave
initial begin
	$dumpfile("wave.vcd");
	$dumpvars(0,tb);
end

//
initial begin
	# 10 sel = 3'b000;
	# 10 sel = 3'b001;
	# 10 sel = 3'b010;
	# 10 sel = 3'b011;
	# 10 sel = 3'b100;
	# 10 sel = 3'b101;
	# 10 sel = 3'b110;
	# 10 sel = 3'b111;
	# 20 sel = 3'bxxx;
	# 20;
	$finish;
end

endmodule
```
