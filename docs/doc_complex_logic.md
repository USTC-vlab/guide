---
title: 复杂组合逻辑电路
sidebar: mydoc_sidebar
permalink: doc_complex_logic.html
folder: mydoc
---
# 复杂组合逻辑电路

## 编码器，译码器和存储器介绍

布尔表达式可以用以输出多变量布尔函数值，像assign这样的数据流构造可以用来模拟这样的布尔函数。存在多输入和多输出的电路，在这个实验中要求你参照关于如何使用Vivado工具创建工程和验证数字电路的教程，设计编码器，译码器和只读存储器。

## 实验目标

- 利用行为建模设计多输出译码器  
- 利用行为建模设计编码器  
- 利用Verilog提供的reg数据类型和$readmemb系统函数设计只读存储器  

## 设计一个3-8译码器。以SW2-SW0为输入，LED7-LED0为输出；使用数据流建模结构

### 多输出译码电路

译码器是拥有多个输出的组合逻辑电路。它们被广泛用于存储芯片中，用来选择输入地址所寻址的一个字。例如一个8字宽的内存会拥有3位地址输入。译码器解码3位的输入地址产生一个选择信号，从8个字中选择地址相应的字。3-8译码器的符号以及真值表如下：  

![](images/complex_logic/1.png)

这样的电路也称为二进制译码器，并且由于每个输出都有唯一的输入组合，所以可以使用数据流语句建模。

### 实验步骤

1. 打开Vivado并创建一个空工程并命名为lab3_1。

2. 创建并添加一个Verilog模块，命名为decoder.3to8.dataflow.v,然后定义译码器的三位输入x和8位输出y。使用数据流建模。

3. 编写仿真文件来验证代码的正确

4. 在工程中添加适当的管脚约束的XDC文件，并加入相关联的管脚，将x分配给SW2-
SW0，将y分配给LED7-LED0。注意对于一个给定的输入组合，只有一个LED会亮。

5. 综合，实现设计。

6. 生成比特流文件，下载到Nexys4开发板上，验证功能。

### 参考代码

 module decoder_3to8_dataflow(
     input [2:0]x,
     output reg [7:0]y
     );
     always@(*)
         case(x)
         3'b000: y=8'b0000_0001;
         3'b001: y=8'b0000_0010;
         3'b010: y=8'b0000_0100;
         3'b011: y=8'b0000_1000;
         3'b100: y=8'b0001_0000;
         3'b101: y=8'b0010_0000;
         3'b110: y=8'b0100_0000;
         3'b111: y=8'b1000_0000;
     endcase
 endmodule

## 设计实现一个流行IC 74138，使用数据流建模和你在1-1中使用的译码器

### 74138译码器

集成三线—八线译码器74138除了3线到8线的基本译码输入输出端外，为便于扩展成更多位的译码电路和实现数据分配功能，74138还有三个输入使能端 EN1， EN2A和EN2B 。74138真值表和内部逻辑图如下图：

![](images/complex_logic/2.jpg)

![](images/complex_logic/3.jpg)

所示符号图中，输入输出低电平有效用极性指示符表示，同时极性指示符又标明了信号方向。74138的三个输入使能(又称选通ST)信号之间是与逻辑关系， EN1高电平有效，EN2A和EN2B低电平有效。只有在所有使能端都为有效电平(EN1EN2AEN2B=100)时，74138才对输入进行译码，相应输出端为低电平，即输出信号为低电平有效。在EN1EN2AEN2B ≠100时，译码器停止译码，输出无效电平(高电平)。  
这和你在1中创建的非常相似，它只是增加了控制（使能）信号G1，/G2A，/G2B。这使得在有些系统中的解码更加简单。

### 实验步骤

1. 打开Vivado创建一个空工程，命名为lab3_2.

2. 创建并添加Verilog模块，命名为decoder_74138_dataflow，实例化你在1-1中开发的模块。添加额外的逻辑，使用数据流建模结构建模所设计的功能。

3. 编写仿真文件来验证代码的正确

4. 将你在1中使用的XDC文件添加到工程。修改XDC文件，将g1分配给SW7，g2a_n分配给SW6，g2b_n分配给SW5。

5. 综合实现你的设计。

6. 生成比特流文件，下载到Nexys4开发板上，验证功能。

### 参考代码和分析

首先，修改lab1的decoder_3to8_dataflow模块，在其中加入enable信号

 module decoder_3to8_dataflow(
     input enable,
     input [2:0]x,
     output reg [7:0]y
     );
     always@(*)
     if(enable)
         case(x)
         3'b000: y=8'b0000_0001;
         3'b001: y=8'b0000_0010;
         3'b010: y=8'b0000_0100;
         3'b011: y=8'b0000_1000;
         3'b100: y=8'b0001_0000;
         3'b101: y=8'b0010_0000;
         3'b110: y=8'b0100_0000;
         3'b111: y=8'b1000_0000;
     endcase
     else y=8'b0000_0000;
 endmodule

之后，decoder_74138_dataflow模块的具体实现：

 module decoder_74138_dataflow(
     input g1,
     input g2a_n,
     input g2b_n,
     input [2:0]x,
     output [7:0]y
     );
     reg enable;
     always@(*)
     begin
         if(g1==1&&g2a_n==0&&g2b_n==0)
         enable=1;
         else enable=0;
     end
     decoder_3to8_dataflow A(enable,x,y);
 endmodule

## 设计一个8-3编码器  

### 多输出编码电路

编码器电路是出于对标准化，速度，保密性，安全性或者通过缩小尺寸来节省空间的考虑，将信息从一种形式（编码）转换为另一种形式（编码）的电路。在数字电路中，编码信息可以减小信息存储所用的空间，确定功能的优先级。广泛使用的编码电路的例子有，优先编码器，哈弗曼编码器等

### 8-3编码器真值表

![](images/complex_logic/4.png)

### 实验步骤

1. 打开Vivado创建一个名为lab3_3的空工程。

2. 创建并添加以v和enable_in_n为输入，以y，enable_out和gs为输出的Verilog模块。v是一个8位的输入（表中标为0到7），输入en_in只有一位（E1），输出y为3位（A2，A1，A0），en_out为一位（E0），gs为一位（GS）。

3. 在工程中添加适当的管脚约束的XDC文件，并加入相关联的管脚约束。将输入v分配给SW7-SW0，enable.in.n分配给SW15，y分配给LED2-LED0，enable_out分配给LED7，gs分配给LED6。

4. 综合实现此设计。

5. 生成比特流文件，下载到Nexys4开发板上，验证功能。

### 参考代码和分析

 module lab3_3(
     input enable_in_n,
     input [7:0]v,
     output reg [2:0]y,
     output reg enable_out,
     output reg gs
     );
     always@(*)
     begin
         if(enable_in_n)
         {y[2:0],gs,enable_out}=5'b11111;
         else if(v[7]==0)
         {y[2:0],gs,enable_out}=5'b00001;
         else if(v[6]==0)
         {y[2:0],gs,enable_out}=5'b00101;
         else if(v[5]==0)
         {y[2:0],gs,enable_out}=5'b01001;
         else if(v[4]==0)
         {y[2:0],gs,enable_out}=5'b01101;
         else if(v[3]==0)
         {y[2:0],gs,enable_out}=5'b10001;
         else if(v[2]==0)
         {y[2:0],gs,enable_out}=5'b10101;
         else if(v[1]==0)
         {y[2:0],gs,enable_out}=5'b11001;
         else if(v[0]==0)
         {y[2:0],gs,enable_out}=5'b11101;
         else
         {y[2:0],gs,enable_out}=5'b11110;
     end
 endmodule

## 设计一个2位比较器，用于比较两个2-bit的数字，并输出字A的十进制值大于、小于或者等于B。你要模拟ROM并会用到$readmemb函数

### 只读存储器

只读存储器（ROM）由互联阵列组成，由于存储二进制信息数组。它一旦存储了二进制信息，可以随时读取，但不能更改。大型的ROM通常用于存储不能被系统中其他电路更改的程序或者数据，小型的ROM可以用来实现组合电路。ROM使用类似于1-1中的译码器来寻址特定的存储位置。  
一个拥有m个地址输入引脚和n个数据输出引脚的ROM可以存储个字，每个字为n位。当给出一个地址访问这一存储器时，地址相应位置的字通过输出引脚读出。  
在Verilog语言中，存储器可以使用reg数据类型的二维数组定义，如下：  

 reg [3:0] MY_ROM [ 15:0];  

上面代码中reg是数据类型，MY_ROM是一个16×4的内存，拥有16个地址，每个地址的宽度为4bit。如果满足下面两个条件，这块内存就是只读的：（i）这块内存只能被读，不能被写入；（ii）内存应该以期望的数据初始化。Verilog语言提供一个系统函数$readmemb可以使用特定内容初始化内存。下面是定义并使用一个4×2的ROM的例子。

 module ROM_4x2 (ROM_data, ROM_addr);
 output [1:0] ROM_data;
 input [1:0] ROM_addr;
 reg [1:0] ROM [3:0]; // defining 4x2 ROM
 assign ROM_data = ROM[ROM_addr];
 // reading ROM content at the address ROM_addr
 initial $readmemb (“ROM_data.txt”, ROM, 0, 3);
 // load ROM content from ROM_data.txt file
 endmodule

在此例中，ROM_data.txt文件应该与verilog模块放在同一目录下（因为引用时没有使用绝对路径），并且文件中可以有8行或者更少，比如：

 10
 0x
 11
 00

注意，如果行数少于ROM的大小，则未指定的位置将会用0初始化，另外还有另一个系统函数$readmembh允许数据文件使用十六进制编写。

### verilog中$readmemb和$readmemh的使用

因为之前自己学习verilog中编写readme的文件，不是很懂是怎么写的，所以菜菜的我还去查了查用法，方便大家学习。（请你们不要嘲笑菜菜的助教啦）
在Verilog语法中，一共有以下六种用法：  

1. $readmemb("<数据文件名>",<存储器名>);  
2. $readmemb("<数据文件名>",<存储器名>,<起始地址>);  
3. $readmemb("<数据文件名>",<存储器名>,<起始地址>,<终止地址>);
4. $readmemh("<数据文件名>",<存储器名>);  
5. $readmemh("<数据文件名>",<存储器名>,<起始地址>);  
6. $readmemh("<数据文件名>",<存储器名>,<起始地址>,<终止地址>);  
示例：  
$readmemb的使用先在Verilog代码目录下准备一个文件file1.txt,存入数据：1111 1010 0101 1x1z 1_1 1111    或者11111010 0101 1x1z 1_1_1_111存在一行每个用空格隔开，跟分行存，输出结果是一样的，但是若在一行中不用空格隔开会出错，编译器会试图把一整行数据存在一个四位的存储单元中。  
这些在你们之后初始化rom或者ram的memory都是很常用的啦（可能要到cod的时候吧）

### 实验步骤

1. 打开Vivado并创建一个名为lab3_4的工程。

2. 使用ROM和$readmemb系统函数创建并添加一个Verilog模块，该模块拥有两个输入（a，b）和三个输出（lt，gt和eq）。

3. 在工程中添加适当的管脚约束的XDC文件，并加入相关联的管脚约束。将a分配给SW3到SW2，b分配给SW1到SW0，lt分配给LED2，gt分配给LED1，eq分配给LED0。

4. 创建并添加描述设计输出的文本文件

上表列出了b与a比较前两项，继续这样的比较直到b和a都达到11为止。

|a|b|lt|gt|eq|
|:----:|:----:|:----:|:----:|
|00|00|0|0|1|
|00|01|1|0|0|
|00|10|1|0|0|
|00|11|1|0|0|
|01|00|0|1|0|
|01|01|0|0|1|
|01|10|1|0|0|
|01|11|1|0|0|
|10|00|0|1|0|
|10|01|0|1|0|
|10|10|0|0|1|
|10|11|1|0|0|
|11|00|0|1|0|
|11|01|0|1|0|
|11|10|0|1|0|
|11|11|0|0|1|

​
将上表的比较结果保存在一个.txt文件中，然后点击位于Flow Navigator下的Add Sources按钮。选择Add or create design sources并点击next。点击绿色的plus按钮然后点击add file。添加你所创建的.txt文件然后点击finish.

5. 综合实现此设计。

6. 生成比特流文件，下载到Nexys4开发板上，验证功能。

### 参考代码和分析

 module lab3_4(
     input [1:0]a,
     input [1:0]b,
     output lt,
     output gt,
     output eq
     );
     reg [2:0] rom [15:0];

     initial $readmemb ("compare.mem", rom);
     assign {lt,gt,eq}=rom[{a,b}];

 endmodule

可以采用相对路径或者绝对路径的方式写入readme txt文件，但是之前文档介绍的add source的方法vivado不支持txt格式，所以我们在这里采用添加mem文件的方法加入文件，这样的方法更加简单一点。  
comare.mem文件具体内容：  

 001
 100
 100
 100
 010
 001
 100
 100
 010
 010
 001
 100
 010
 010
 010
 001

## 扩展实验内容

### 使用ROM实现一个2位乘2位的乘法器，将结果以二进制形式输出到四个LED灯上

#### 具体要求

1. 打开Vivado并创建一个名为lab3_kuozhan1的空工程。

2. 使用ROM和$readmemb系统函数创建并添加一个拥有两个两位输入（a，b）和一个四位输出（product）的Verilog模块。

3. 在工程中添加适当的管脚约束的XDC文件，并加入相关联的管脚约束。将a分配给SW3-SW2，b分配给SW1-SW0，product分配给LED3-LED0。

4. 创建并添加描述设计输出的文本文件。

5. 综合实现此设计。

6. 生成比特流文件，下载到Nexys4开发板上验证功能。

### 使用在两个lab1中设计的3-8译码器扩展成一个4-16的译码器

1. 打开Vivado并创建一个空工程并命名为lab3_kuozhan2。

2. 创建并添加Verilog模块，定义译码器的四位输入x和16位输出y.

3. 编写仿真文件来验证代码的正确

4. 在工程中添加适当的管脚约束的XDC文件，并加入相关联的管脚，将x分配给SW3-
SW0，将y分配给LED15-LED0。注意对于一个给定的输入组合，只有一个LED会亮。

5. 综合，实现设计。

6. 生成比特流文件，下载到Nexys4开发板上，验证功能。

#### 具体要求

## 总结

在本次试验中，你学到了如何对多输出电路如译码器、编码器和ROM建模；你也学到了如何使用系统函数$readmemb对ROM进行初始化。Verilog还支持很多系统函数，你将在下一个实验中学习另外一些。
