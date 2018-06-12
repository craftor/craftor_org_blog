---
layout: post
title: 使用Matlab生成COE文件
comments: true
categories: Matlab FPGA
tags: Matlab FPGA
---

在FPGA中做VGA显示时，经常需要把图片转成COE文件（Xilinx FPGA），存放到ROM中，供FPGA读取显示。
这里写了一个matlab脚本，可以读取图片，转成16进制的RGB数据。对应的ROM数据宽度为24bit，输出数据RGB888。

```m
clear;
clc;
img = imread('1.png');       %读取图片
fid = fopen('out.coe', 'w'); %创建COE文件

fprintf(fid, 'memory_initialization_radix=16;\n');
fprintf(fid, 'memory_initialization_vector=\n');

m = size(img);  %获取图片尺寸，m(1)为高，m(2)为宽
for i = 1:m(1)
    for j = 1:m(2)
        % 将RGB数据写在一起
    	fprintf(fid, '%02X%02X%02X,\n', img(i,j,1), ...  % R
                                        img(i,j,2), ...  % G
                                        img(i,j,3));     % B
    end
end

fseek(fid, -2, 1); % 将最后一个逗号用分号覆盖
fprintf(fid, ';');

fclose(fid); %关闭文件
```
