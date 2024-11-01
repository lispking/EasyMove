# 4.轻松入门Move: 基础语法

本章将介绍Move的基础语法。

## 基本类型

Move语言是一个强调数据类型的语言，在声明任何变量时，必须将该变量定义为一种数据类型。Move中的数据类型包括基本数据类型和自定义类型（也就是结构体）。结构体不属于本章所讲述的内容，本章主要介绍数据的基本类型。Move程序中，总共有三种基本类型：其中包括整型、布尔型和地址。

### 整型

整型有分五种，分别是u8,u32,u64,u128,u256。u代表unsigned说明不支持负数,u后面的数字代表可以存储的位数，也可以根据这个位数，推算出能存储的最大值：

| 类型 | 最小值 | 最大值                    |
| ---- | ------ | ------------------------- |
| u8   | 0      | 255                       |
| u32  | 0      | 4294967295                |
| u64  | 0      | 18446744073709551615      |
| u128 | 0      | 3.4028236692093846346e+38 |
| u256 | 0      | 1.1579208923731619542e+77 |

注意：基本类型只支持正整数，负数将会导致编译报错。



两个不同类型的正整数，要比较大小怎么办呢？可以使用as做类型转换后在进行比较，如下：

```rust
public fun compare() :bool {
	let a:u8 = 10;
    let b:u32 = 30;

    (a as u32) >= b
}
```

### 布尔型

布尔类型使用bool表示，值有两个true和false。

### 地址

地址类型用于标识区块链中的地址，如果引入包的包地址、钱包地址或者发送方地址。

地址一般是0x开头，代表十六进制数。

## 注释

像大多数编程语言，注释分为单行注释和多行注释。

```rust
//单行注释
/*

多行注释

*/
```

## 变量

变量可以使用以下方式声明和初始化:

let <VARIABLE> : <TYPE>

`let <VARIABLE> = <EXPRESSION>`

let <VARIABLE> : <TYPE> = <EXPRESSION>

```rust
let a:u8;
let a=10;
let a:u8=10;
let a=true;
let a=0x0
```

如果直接对变量赋值正整数，不指定类型，默认类型是u64

```rust
let a=10;//a类型是u64
```

Move不允许申明变量之后不使用。我们可以使用_去标记该变量不使用：

```rust
public fun compare() :bool {
	let a = 10;
    let b = 10;
    let _ = returnNum(); //丢弃函数返回值
    a > b
}
public fun returnNum():u64 {
    13
}
```

### 变量作用域

变量的作用域用一句话就能描述清楚：变量只在声明它的代码块中生效，代码块结束变量就无效。代码块由花括号标记，模块的花括号代表一个代码块，函数的花括号也是一个代码块。

```rust
//参数a,b作用域：整个函数代码块
public fun varLifetimes(a:u8, b:u8) :bool {
	let c = a + b;//变量c作用域：整个函数代码块
    if (c > 10) {
        let d = 12; //变量d作用域：if{}内
        _ = d;
    } else {
        let e = 13;//变量e作用域：else{}内
        _ = e;
    };
    {
    	let f=1;//变量f作用域：{}内
        _ = f;
     };

     c > 100
}
```

## 常量

使用const关键字 申明一个常量，常量用大写字母和下划线组成。值可以是基本类型、数组和一部分表达式。

值得注意的是，常量只能模块内部访问！

```rust
const LEVEL:u64=10;
```

## 流程控制

任何一门语言都需要基本的流程控制语句，其思想也符合人类判断问题或做事的逻辑过程。什么是流程控制呢？流程就是做一件事情的顺序。在程序设计中，流程就是要完成一个功能，而流程控制则是指如何在程序设计中控制完成某种功能的顺序。

##### 条件语句

在现实生活中，经常听人说：如果中彩票了，我就...。其实这就是程序设计中所说的条件语句。例如“如果……就……”“否则……”

在Move中的if语法是：

```move
if (<bool_expression>) <expression> else <expression>;
```

if语句的几种形式：

```rust
let i = 10;
//单行不加花括号
if (i > 5) i = i + 1;
//多行要加花括号
if (i > 5) {
	i = i + 1;
};
//if...else
if (i > 5) {
	i = i + 1;
} else {
	i = i - 1;
};
```

注意: 跟大多数语言不同的是，这里最后一个表达式后加了分号代表结束。

### 循环语句

- while循环：有条件的循环

  ```rust
   //满足条件才会循环
   while (i < 5) {
       i = i + 1;
   };
  ```

- loop循环：无条件循环

  无条件循环如果内部没有使用break跳出循环，会一直循环下去，也就是所谓的死循环。

  ```rust
  //这个代码与while中的示例代码作用一样
  loop {
  	if (i >= 5) {
  		break;
  	};
  }
  ```

- continue

  continue代表跳过本次循环，进入下一个循环。break则是结束循环，这俩需要注意区分。

## 退出语句

如果程序满足某些条件就要停止运行，可以使用关键字abort

也可以使用封装好的内建函数assert

```rust
//这两行代码是等价的
if (i > 5) abort 0;
assert(i <= 5, 0);
```

## 函数

函数结构：

```rust
fun function_name(arg1: u64, arg2: bool): (u8, bool){
	//函数体
}
```

函数名由小写字母和下划线组成

### 函数返回值

使用return + 返回值，可以结束函数执行并返回数据，如果是最后一行返回则可以缺省return关键字。如下：

```rust
public fun compare(a: u64, b: u64): u8 { 
	if (a == b ) {
        return 0 //中断函数执行，直接返回
     };
     //最后一个表达式，可以无return返回  
     if (a > b) {
        1
      } else {
        2 
      }
}
```

值得注意的是，无论是何种返回方式，表达式最后都没有逗号



Move支持多个返回值，多个返回值的返回和使用，详见代码：

```rust
public fun call_return_nums(): u64{
        let (a,b) = return_nums();//接收多个函数返回值，必须有括号
        if (b) {
            a
        } else {
            0    
        }
    }
    public fun return_nums():(u64, bool) {//多个返回值的申明方法
        (19, true)//返回多个函数返回值，必须有括号
    }
```



了解更多Move内容：

- telegram: t.me/move_cn
- QQ群: 79489587



