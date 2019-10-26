---
title: 代码风格规范
sidebar: mydoc_sidebar
permalink: coding_convention.html
folder: mydoc
---

## 空行

```verilog
module mux4_to_1(out, in0, in1, in2, in3, s1, s0);
    input [1:0] in0, in1, in2, in3;
    input s1, s0;
    output reg [1:0] out;

//变量声明后空行
always @(*)
begin
    case ({s1,s0})
        2'b00: out = in0;
        2'b01: out = in1;
        2'b10: out = in2;
        default: out = in3;
    endcase
end
endmodule

//两模块间空行
module mux2_to_1(/*...*/);
    /**/
endmodule
```

## 空格

```verilog
module mux4_to_1(out, in0, in1, in2, in3, s1, s0); //模块名后不留空格
    input [1:0] in0, in1, in2, in3;
    input s1, s0;
    output reg [1:0] out;

    always @(*) //关键字后空格（always后加空格）
    begin
      	//关键字后空格（case后加空格）
        case ({s1, s0})
            2'b00: out = in0; //分号向前紧跟
            2'b01: out = in1;
            //赋值运算符、关系运算符、算术运算符、逻辑运算符、位运算符等双目运算符前后都要加空格
            2'b10: out = in2;
            default: out = in3;
        endcase
    end
endmodule
//单目运算符前后不加空格
```

## 缩进

```verilog
module mux4_to_1(out, in0, in1, in2, in3, s1, s0);
    input [1:0] in0, in1, in2, in3;
    input s1, s0;
    output reg [1:0] out;

    always @(*)
    begin
      case ({s1,s0}) //case在always里，要缩进
        2'b00: out = in0; //case的的每一条在case里，也要缩进
        2'b01: out = in1;
        2'b10: out = in2;
        default: out = in3;
    endcase
end
endmodule
```

缩进原则：如果地位相等，则不需要缩进；如果属于某一个代码的内部代码就需要缩进

## 对齐

```verilog
module mux4_to_1(out, in0, in1, in2, in3, s1, s0);
    input [1:0] in0, in1, in2, in3;
    input s1, s0;
    output reg [1:0] out;

    always @(*)
    begin //begin和end要独占一行，且不缩进
    case ({s1,s0})
        2'b00: out = in0;
        2'b01: out = in1;
        2'b10: out = in2;
        default: out = in3;
    endcase
end
endmodule
```

## 代码行

```verilog
module mux4_to_1(out, in0, in1, in2, in3, s1, s0);
    input [1:0] in0, in1, in2, in3;
    input s1, s0;
    output reg [1:0] out;

    always @(*) //always、if、else等独占一行
    begin
        case ({s1,s0})
            2'b00: out = in0; //一行代码只做一件事情，如只写一条语句
            2'b01: out = in1;
            2'b10: out = in2;
            default: out = in3;
        endcase
    end
endmodule
```

## 注释

```verilog
module mux4_to_1(out, in0, in1, in2, in3, s1, s0); //注释开始符号与代码之间有空格
    input [1:0] in0, in1, in2, in3;//4个待选输入
    input s1, s0; //选择信号
    output reg [1:0] out; //存放被选择出的数据

    always @(*)
    begin
        case ({s1,s0})
            2'b00: out = in0; //in0的值赋给out <- 这是一条多余注释
            2'b01: out = in1;
            2'b10: out = in2;
            default: out = in3;
        endcase
    end
endmodule
```

原则：

1. 注释是对代码的“提示”，而不是文档。程序中的注释不可喧宾夺主，注释太多会让人眼花缭乱。
2. 边写代码边注释，修改代码的同时要修改相应的注释，以保证注释与代码的一致性，不再有用的注释要删除。
3. 如果代码本来就是清楚的，则不必加注释。
