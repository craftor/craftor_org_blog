---
layout: post
title: FIR滤波器设计(v2)——使用Matlab R2015和CCS 5.5
comments: true
categories: FPGA
tags: 滤波器 FIR Matlab CCS
---

# 一、写在前面的话

记得在2009的时候，还在读大三，上DSP这门课，后来写了一篇关于DSP仿真的教程----[《FIR 滤波器的设计----使用 Matlab 和CCS》](http://public.craftor.org/FIR%E6%BB%A4%E6%B3%A2%E5%99%A8%E7%9A%84%E8%AE%BE%E8%AE%A1%E2%80%94%E2%80%94%E4%BD%BF%E7%94%A8Matlab%E5%92%8CCCS.pdf)，很多人受到启发，加我QQ（我在文章最后留了邮箱和QQ号），问了我一些关于CCS仿真的问题。时隔这么多年，还是偶尔会有人加我QQ，问我关于DSP仿真的问题。

但近期我也发现，09年的时候，用的CCS还是3.3版本。而现在，TI的网站上已经没有3.3版本的下载了，所以，文中的一些操作和说明，已经无法跟上现在的CCS版本了。这也是很多朋友加我QQ询问的问题之一。

由于后来工作了比较忙，工作中也没再使用DSP，这方便的经验也就慢慢淡忘了。

直到今年，我到一所大学任教，系里安排我上DSP这门课，我才重新把DSP这门技术拾起来，也回想起来了曾经写过的这篇博文。我自己再想回顾时，也发现了很多地方由于CCS版本的问题，无法复现。所以，也就硬着头皮，决定把这方面的内容重新更新一下，适应新版的CCS。

# 二、准备工作

使用的软件版本如下：

1. Matlab R2015a

  > Matlab的版本影响不大，经过测试，6.5版的都可以适用。

2. CCS 5.5

  > 3.x~5.x版的CCS支持软件仿真，6.0以后就不支持仿真了。网上有人实现让6.x的版本也能进行软件仿真，具体可以查看----[CCSv6.0以上版本实现软件仿真](http://blog.csdn.net/lygoflying/article/details/51025724)

# 三、设计过程

## 3.1 设计流程

如下图所示：

![](http://qiniu.craftor.org/2016-1907c86c.png-top.right)

## 3.2 设计步骤

### Step 1

运行Matlab，在命令行中输入fdatool，启动滤波器设计器，界面如下：

![](http://qiniu.craftor.org/2016-8848f384.png-bottom.right)

然后按如下表格设计参数：

参数             | 值
-------------- | --------
Response Type  | Bandpass
Design Method  | FIR
Filter Order   | 80
Destiny Factor | 20
Units          | kHz
Fs             | 10
Fstop1         | 1
Fpass1         | 1.375
Fpass2         | 3.625
Fstop2         | 4
Wstop1         | 70
Wpass          | 0.5
Wstop2         | 70

设置完成后，点击最下面的Design Filter，结果如下图：

![](http://qiniu.craftor.org/2016-90d64b92.png-center)

再点击Target下面的-->Code Composer Studio

![](http://qiniu.craftor.org/2016-1818f520.png-center)

选择Export as: Signed 16-bit integer

![](http://qiniu.craftor.org/2016-fc3fe9de.png-center)

然后选择将fdacoefs.h文件存放到桌面上，有错误提示不管。

打开桌面上的fdacoefs.h文件，作一定修改，修改后如下。若没有Matlab软件，可以直接将下面内容复制另存为fdacoefs.h文件。

```c
const int BL = 81;
const int B[81] = {
      -16,      0,     -7,      0,    139,      0,   -223,      0,     17,
        0,    233,      0,    -14,      0,   -356,      0,     35,      0,
      532,      0,    -59,      0,   -790,      0,     85,      0,   1184,
        0,   -110,      0,  -1857,      0,    130,      0,   3334,      0,
     -143,      0, -10382,      0,  16531,      0, -10382,      0,   -143,
        0,   3334,      0,    130,      0,  -1857,      0,   -110,      0,
     1184,      0,     85,      0,   -790,      0,    -59,      0,    532,
        0,     35,      0,   -356,      0,    -14,      0,    233,      0,
       17,      0,   -223,      0,    139,      0,     -7,      0,    -16
};
```

### Step 2

关闭刚才打开的fdatool，保存自己设计好的滤波器。

在Matlab工作目录下新建一个m文件，然后输入下面的代码：

```matlab
f11=500; %/Hz
f12=3000; %/Hz
f13=8000; %/Hz
fs=10000; %/采样Hz
N=1000 %数据个数
T=1/fs; %采样周期
n=0:N;
x11=sin(2*pi*f11*n*T);
x12=0.7*sin(2*pi*f12*n*T);
x13=0.5*sin(2*pi*f13*n*T);
x_base=(x11+x12+x13);
%待滤波信号波形
figure(1)
plot(x_base)
%待滤波信号频谱
figure(2)
yff=abs(fft(x_base))
df=n*(fs/N)
plot(df,yff)
xout=x_base/max(x_base);%归一化
xto_ccs=round(32767*xout)
fid=fopen('input.dat','w');%打开文件
fprintf(fid,'1651 1 0 0 0\n');%输出文件头
fprintf(fid,'%d\n',xto_ccs);%输出
fclose(fid);
```

运行一下，应该出现如下两张图：

![](http://qiniu.craftor.org/2016-046c419e.png-bottom.left)

![](http://qiniu.craftor.org/2016-adec8dd4.png-bottom.left)

同时，在工作目录下也会多出来input.dat文件，把这个文件复制到桌面上。

然后关闭Matlab。

### Step 3

打开CCS软件，指定好工作目录。界面如下：

![](http://qiniu.craftor.org/2016-b19f9932.png-center)

点击Project->New CCS Project，设置如下：

![](http://qiniu.craftor.org/2016-60f9723c.png-bottom.left)

> 注意，在connection选项中，一定要选择TI Simulator才是软件仿真。有些器件不支持软件仿真，注意切换到支持仿真的器件上。

完了点击Run下面的Debug，弹出界面：

![](http://qiniu.craftor.org/2016-61f80dcb.png-center)

点击Run->Resume(F8)，可以看到在最下面Console中出现了"Hello World!"，说明可以进行软件仿真。

![](http://qiniu.craftor.org/2016-ff53555a.png-bottom.right)

然后点Stop，退出Debug模式。

### Step 4

在左侧的Project Explorer中，工程上面点击右键->Add Files，然后选择之前生成的fdacoefs.h文件，选择Copy Files选项，将文件复制到工程中。

再将原来main.c/hello.c文件中内容全部删除，用下面的内容替换：

```c
#include "stdio.h"
#include "fdacoefs.h"

#define N 81 //FIR滤波器的级数+1，本例中滤波器级数为80
#define LEN 200 //待滤波的数据长度

long yn;
int input[LEN]; //输入缓冲，在仿真时将从内存载入
int output[LEN]; //输出缓冲，直接存放在内存中

void main()
{
    int i,j;
    int *x;
    for(j=0;j<LEN-1;j++)
    {
        x=&input[j];
        yn = 0;
        for(i=0; i<N-1; i++)
            yn += B[i]*(*x++);
        output[j]=yn>>15;
    }
    while(1);
}
```

再次点击Run-Debug。然后点击Tools->Load Memory：

![](http://qiniu.craftor.org/2016-fb997a28.png-center)

文件选择之前生成的input.dat文件（应该是已经复制到了桌面上）.

![](http://qiniu.craftor.org/2016-00decc27.png-bottom.left)

Start Address设置为input，length设置为200，然后点击Finish。

再找到主文件中的 while(1); 这一行，然后右键->Run to Line。

可以看到光标指向了这一行，同时，右上的变量框里也变黄了，说明程序运行过了，变量发生了变化。

![](http://qiniu.craftor.org/2016-85dd6d94.png-bottom.left)

此时，滤波器仿真的运行过程已经完成了，下一步就是怎么查看波形了。

### Step 5

接上一步。选择Tools->Graph->Single Time，Buffer Size设置为200，Start Address 设置为input。点击OK，即可看到input数组中数据显示的波形。

![](http://qiniu.craftor.org/2016-e2a41c34.png-bottom.left)

同样，将Start Address设置为output，即可看到输出数据的波形。

再选择Tools->Graph->FFT Magnitude，同样设置为200和input，可以看到input数据的FFT，然后再看一下output的FFT，对比如下：

![](http://qiniu.craftor.org/2016-8a3f3b55.png-bottom.left)

![](http://qiniu.craftor.org/2016-aae9c22a.png-bottom.left)

可以明显看到，滤波后有一个频率成份被滤掉了。

到这里，就是简单的滤波器的设计和仿真的全部过程了。如果要深入研究和学习DSP，还需要读者自己再深入思考和学习了。这里只是抛砖引玉。

# 四、后记

这几年一直研究FPGA，现在的FPGA越来越强大了，DSP在某些场合也慢慢被FPGA取代了。所以，我建议如果有精力，多学学FPGA，也是有好处的。
