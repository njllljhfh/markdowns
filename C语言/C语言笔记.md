# C语言笔记



## 一、简介

1.面向对象、面向过程，是一种编程思想。

2.机器码---汇编语言---高级语言

3.ALGOL--CPL--BCPL--B--C--K&R C--C89-C99-C11 

4.unix的第一个版本是用B语言开发的，后来才是C语言开发的。linux是C语言开发的。macos基于unix，ios基于unix。



## 二、开发环境

### 2.1、IDE

Visual Studio

Xcode

Eclipse+DCT

IntelliJ IDEA

CLion



### 2.2、 编译器

gcc ：  GNU开发的编译器

cl：微软开发的编译器

clang：苹果公司开发的编译器



## 三、C语言基础知识

### 1、定义一个整形常量

```c
int 一般是 4个字节
// 推荐使用(定义一个常量)
const int MY_AGE1 = 10000;

```



### 2、整形数据类型 

```c
long 类型
如果系统是32位的，那么lang是4个字节
如果系统是64位的，那么long是8个字节

long long 
不管在多少位的系统下，都是8个字节

int b = 10;
int c = -200;
long long d = 9999999999;
printf("b=%d,c=%d,d=%ld\n", b, c, d);

```



#### 2.1、在代码中写不同进制的 整形数字

```c
int e = 0b100; // 二进制
printf("e=%d \n", e);

int f = 0xB; // 十六进制
printf("f=%d \n", f);

int g = 010; // 八进制
printf("g=%d \n", g);
```



#### 2.2、定义无符号类型的 整形数字

```c
unsigned int h = 12;  // 无符号整形
printf("h=%d \n",h );

unsigned long long i = 999999999999999999;  // 无符号长整形
printf("i=%ld \n",i);
```



#### 2.3、#include <stdint.h> 中的整形数据（各个平台通用）

```c
    int32_t j = 10;
    int8_t k = 127;  // 取值范围  [-128,127]
    uint8_t l = 255;  // 无符号整形
    int64_t m = 999999999999999999;  // #include <stdint.h> 中的对应的 long long类型

    printf("k=%d \n", k);
    printf("l=%d \n", l);
    printf("m=%ld \n", m);
```



### 3、实数型数据（浮点型）

浮点型数据在计算的时候会有误差。

银行存款不能用浮点型，只能用整形数据。1元要存成100分（即存成整形）

```c
#include <stdio.h>

// 实数数据类型
int main() {
    float a = 3.1; // 单精度,4字节，32位
    double b = 3.2; //双精度,8字节，64位
    long double c = 3.1; //长双精度,16字节，128位
    printf("a=%f, b=%f, c=%Lf", a, b, c);

    return 0;
}
```



### 4、字符数据类型

```c
#include <stdio.h>
// 字符数据类型

int main() {
    printf("Hello,\nWorld!\n"); //换行符

    printf("nihao\nHello\rWorld\r123"); //回车符,会把指针指到 \r 所在行的行首，并输出其后面的内容

    printf("Hello你\bWorld"); //退格符,删除其前面的一个字符

    printf("Hello\tWorld\nHaha\tbupt"); //制表符

    printf("Hello\fWorld\n"); //换页符

    printf("\\"); //输出反斜杠

    printf("\"dragon\"\n"); //输出双引号

    printf("\'dragon\'\n"); //输出双引号

    printf("length of char : %d\n", sizeof(char)); //char占一个字节（即8位）

    char ch = 'a';
    printf("%d\n", ch); //输出字符的askii编码
    printf("%c\n", ch - 32); //输出字符

    return 0;
}
```



### 5、自定义数据类型

```c
#include <stdio.h>
#include <stdint.h>
// 自定义数据类型

typedef int64_t mt_long; //自定义一个新的数据类型mt_long，该类型为 int64_t
typedef char mt_char;
typedef uint8_t mt_char1; //char是占一个字节，可以用uint8_t 来自定义一个char类型
int main() {
    mt_long a = 20;
    printf("%lld\n", a);

    mt_char b = 'A';
    printf("%c\n", b);

    mt_char1 c = 'C';
    printf("%c\n", c);

    return 0;
}
```

