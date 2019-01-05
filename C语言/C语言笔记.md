# C语言笔记



## 一、简介

### 1.1、C语言简介

1.面向对象、面向过程，是一种编程思想。

2.机器码---汇编语言---高级语言

3.ALGOL--CPL--BCPL--B--C--K&R C--C89-C99-C11 

4.unix的第一个版本是用B语言开发的，后来才是C语言开发的。linux是C语言开发的。macos基于unix，ios基于unix。


asdf
### 1.2、CPU架构简介

- 20% 的指令为常用指令，在一个程序执行的时候，会调用的比例达到 80%
- 80% 的指令为不常用指令，在一个程序被执行的时候，会调用比例为20%

#### 1.2.1、RISC 与 CISC

##### 1.2.1.1、RISC（Reduced Instruction-Set Computer）

- 精简指令集
- 终端设备、嵌入式设备、大型服务器（如unix服务器），一般都是精简指令集的CPU



##### 1.2.1.2、CISC（complex instruction set computer）

- 复杂指令集
- PC、笔记本电脑





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

CPU的最小单元是寄存器

内存以字节(BYTE)为单位，不是以比特(bit)为单位

32位的操作系统，最大能保存的整数为 `2^32 - 1`

64位的操作系统，最大能保存的整数为 `2^64 - 1`



### 3.1、初识C

#### 3.1.1、include

- 告诉编译器，包含一个头文件
- 在 `C语言 `中，任何 `库函数` 调用都需要提前包含头文件
- <头文件>，代表让C语言编译器去系统目录下寻找相关的头文件
- ''头文件''，代表让C语言编译器去用户当前目录下寻找相关头文件



#### 3.1.2、main函数

- `main()` 函数是 C 语言中的主函数
- 一个 C 语言程序必须有一个主函数，也只能有一个主函数
- C 语言中，所有的函数的代码都是在`{}`里包着的



#### 3.1.3、注释

- 单行注释：`//` ，C++语言的注释
- 多行注释：`/**/` ，标准 C 语言的注释



#### 3.1.4、声明

- `变量名` 可以自定义，变量名的第一个字符必须是 `字母 ` 或 `下划线`
- `变量名` 区分大小写
- 不能用 C 语言的 `关键字` 作为 `变量名`
- `变量名` 长度 尽量不要超过 256个字符 

```c
int i; //声明了一个变量，名字叫i，类型是int
```



#### 3.1.5、printf

- 向标准输出设备，输出字符串

```c
#include <stdio.h>  // 使用printf库函数，要导入这个头文件

int main() {

    printf("hello world!");
    return 0;
}
```



#### 3.1.6、return

- 一个函数遇到 `return` 语句就终止了，return 是 C语言 的关键字
- 主函数 `return 0` 一般代表成功的意思，`return -1` 代表失败的意思



#### 3.1.7、System系统调用

- System库函数的功能是执行操作系统的命令或者运行指定的程序

```c
#include <stdio.h>
#include <stdlib.h>  //system库函数要导入这个头文件

int main() {
    system("ls -lha");
    return 0;
}
```

#### 3.1.8、POSIX

- 一种标准，规范



### 3.2、C语言编译过程

`.c 文件` → `预编译` → `编译` → `连接` → `可执行程序`

![C语言编译过程](./images/C语言编译过程.png)

#### 3.2.1、gcc

##### 3.2.1.1、预编译（-E）

`gcc -E -o a.i a.c`

- 预编译 a.c 文件，生成的目标文件名为 a.i
- 预编译把 #include 头文件添加到代码中 
- 预编译去掉代码中的 注释



##### 3.2.1.2、编译（-S）

`gcc -S -o a.s a.i` 

- 得到汇编语言的文本文件



##### 3.2.1.3、汇编（-c）

 `gcc -c -o -a.o a.s`

- 将代码便以为二进制的机器指令，得到二进制文件



##### 3.2.1.4、连接（没有参数）

`gcc -o a a.o`

- 把库函数和其他目标代码，连接到代码中，得到二进制文件



### 3.3、64位与32位操作系统

#### 3.3.1、计算机内存

##### 3.3.1.1、不同操作系统的差别

1. `32位操作系统`，最大内存为4G，linux系统大概要占用1G的内存，剩下的3G是用户区域
2. 32位的操作系统，即使装在64位的CPU上，仍然是32位的操作系统（CUP对系统是向下兼容的）
3. 32位的CPU，不可以运行64位的操作系统



##### 3.3.1.1、内存区域

计算机内存分为两个区域

- 用户区域

- 内核区域



##### 3.3.1.2、用户区域

1. 普通程序运行在用户区域



##### 3.3.1.3、内核区域

1. 操作系统运行在内核区域
2. 设备驱动程序运行在内核区域

#### 3.3.2、CPU

##### 3.3.2.1、CPU组成

- 控制器

- 运算器

- 寄存器：由多个二极管构成。`CPU的位数`指的是CPU中寄存器的位数
  - 一个8位寄存器：a
  - 一个16位寄存器：ax
  - 一个32位寄存器：eax
  - 一个64位寄存器：reax



### 3.4、数据类型
#### 3.1.1、整形数据

- int 就是32位的一个二进制整数，在内存中占据4个字节的空间
- 只有 `十进制` 的数字才有正负之分
- %d ：有符号输出 `十进制` 整数 
- %u ：无符号输出 `十进制` 整数
- %x ：输出 `十六进制`（小写字母）
- %X ：输出 `十六进制`（大写字母）
- %o ：输出 `八进制` 
- %hd：短整型
- %hu：无符号短整型
- %e/E：科学计数法表示数字
- C语言中，两个整数相除的结果，自动转化为一个整数，结果只保留整数位



```c
int 一般是 4个字节

const int MY_AGE1 = 10000; // 推荐使用(定义一个常量)

#define MAX 10  //定义一个宏常量，值为0

const 常量 与 宏常量是有区别的(c++中区别很大，后续讨论)
    
 
 //#define 类型的常量，c语言的习惯是，用大写
 
 //const类型的常量、变量，c语言的习惯是，用小写结合大写
 
    
long 类型
如果系统是32位的，那么lang是4个字节
如果系统是64位的，那么long是8个字节

long long 
不管在多少位的系统下，都是8个字节，64位

int b = 10;
int c = -200;
long long d = 9999999999;
printf("b=%d,c=%d,d=%ld\n", b, c, d);


double aa = 3 / 2; // C语言中，两个整数相除的结果，自动转化为一个整数，结果只保留整数位
printf("aa = %f\n", aa); // ---> 1.000000
```

##### 3.1.1.1、在代码中写不同进制的 整形数字

```c
int e = 0b100; // 二进制 --> 4
printf("e=%d \n", e);

int f = 0xB; // 十六进制  --> 11
printf("f=%d \n", f);

int g = 010; // 八进制 --> 8
printf("g=%d \n", g);
```



##### 3.1.1.2、定义无符号类型的 整形数字

```c
unsigned int h = 12;  // 无符号整形
printf("h=%d \n",h );

unsigned long long i = 999999999999999999;  // 无符号长整形
printf("i=%ld \n",i);
```



##### 3.1.1.3、#include <stdint.h> 中的整形数据（各个平台通用）

```c
    int32_t j = 10;
    int8_t k = 127;  // 取值范围  [-128,127]
    uint8_t l = 255;  // 无符号整形
    int64_t m = 999999999999999999;  // #include <stdint.h> 中的对应的 long long类型

    printf("k=%d \n", k);
    printf("l=%d \n", l);
    printf("m=%ld \n", m);
```

##### 3.1.1.4、整数溢出

- 计算一个整数的时候超过整数能够容纳的最大单位后，整数会溢出，语出的结果是高位舍弃。

```c
#include <stdio.h>

int main() {

    unsigned short int c = 0xffff;
    c = c + 1;
    printf("c=%d\n", c); //整数溢出，  -->  0

    return 0;
}
```



##### 3.1.1.5、大端对齐与小端对齐

- 内存都是以字节为最小单位
- 对于 Intel 这种 x86 架构的复杂指令CPU，整数在内存中是倒着存放的，内存的低地址 存放数据的低位，内存的高地址 存放 数据的高位。这种叫做 `小端对齐`
- 我们电脑平时用的系统一般都是`小端对齐`
- `arm`也是小端对齐
- 但对于unix服务器的CPU，更多是采用 `大端对齐` 的方式存放整数


![查看内存地址中的数据](./images/查看内存地址中的数据.png)



#### 3.1.2、二进制，八进制，十六进制

##### 3.1.2.1、二进制

1. 一个 `位` 只能表示0，或者1两种状态，简称`bit`，一个 `位` 是一个bit

2. 一个 `字节` 为8个二进制位，称为8位，简称`BYTE`，8个比特是一个 `字节` 

3. 一个字为2个字节，简称`WORD`，16位

4. 两个字为双字，简称`DWORD`，32位



- `二进制` 转 `八进制` ，从右边开始分组，每3位一组来切分二进制数，最左边的一组不足3位时，在其左侧补0
- `二进制` 转 `十六进制 `，从右边开始分组，每4位一组来切分二进制数，最左边的一组不足4位时，在其左侧补0



##### 3.1.2.2、 八进制

- C语言中，0开头表示八进制，例如：0666

- `十进制` 转化为 `八进制` ，用 十进制数 作为被除数，8作为除数，取商数和余数，直到商数为0的时候，将余数倒过来就是转化后的结果



##### 3.1.2.3、十六进制

- C语言中，0x开头表示十六进制，例如：0x19f

- `十进制` 转化为 `十六进制` ，用 十进制数 作为被除数，16作为除数，取商数和余数，直到商数为0的时候，将余数倒过来就是转化后的结果



#### 3.1.3、原码反码补码与无符号数

电脑里保存的都是 `二进制` ，电脑中保存的是 `补码`

##### 3.1.3.1、原码

- 一个字节的取值范围 `-127~127`
- 将最高位作为符号位(0代表正，1代表负)，其余各位代表数值本身的绝对值
  - `+7` 的原码是    00000111
  - `-7` 的原码是    10000111
  - `+0` 的原码是    00000000
  - `-0` 的原码是    10000000



##### 3.1.3.2、反码

- 反码：原码的符号位不变，其余位求反（1变0，0变1）

- 一个字节的取值范围 `-127~127`
- 如果一个数为正，那么他的反码和原码相同
  - `+7` 的反码是    00000111
- 如果一个数为负，那么符号位为1，其他各位与原码相反
  - `-7` 的反码是    11111000
  - `-0` 的反码是    11111111



##### 3.1.3.3、补码

- 补码：在反码的基础上加 1

- 一个字节的取值范围 `-128~127`
- `-128` 的反码是：10000000
- 原码和反码都不利于计算机的运算，如：原码表示的 7 和 -7 相加，还需要判断符号位。
- 正数：原码、反码、补码都相同
  - `+7` 的补码是    00000111
- 负数；最高位为1，其余各位原码取反，最后对整个数 +1
  - `-7` 的原码是    10000111
  - `-7` 的反码是    11111000
  - `-7` 的补码是    11111001

- `补码` 转 `原码` 
  - 方法1：补码的符号位不变，其余位求反，然后加1
  - 方法2：补码减1，得到反码，然后用得到的反码；然后反码的符号位不变，其余位求反得到原码




#### 3.1.3、实数型数据（浮点型）

- 在 `32位系统` 下：flot（4个字节）、double（8个字节）、long double；
- cpu用fpu单元算浮点数，效率很低。如果只是整数计算，应该避免使用浮点型；
- 浮点型数据在计算的时候会有误差；

- 银行存款不能用浮点型，只能用整形数据。算钱要以分为单位，1元要存成100分（即存成整形）；

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



#### 3.1.4、字符数据类型

##### 3.1.4.1、字符

- `char c` ：定义一个字符变量c
- 'c'：字符常量
- 格式化输出字符：%c
- char：占一个 字节
- `char` 的本质就是一个整数，一个只有 `一个字节大小` 的整数
- `char a = 'a'` ：定义一个`有符号的char`
- `unsigned char b = 'b'` ：定义一个`无符号的char`
- `putchar()` ：显示一个字符的函数
- `scanf()` ：获取用户输入
- `getchar()` ：得到用户按一个按键对应的字符 的 ASCII码（该函数没有参数）



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
    printf("%d\n", ch); //%d输出字符的askii编码
    printf("%c\n", ch - 32); //%c输出字符

    int a = 0;
    int b = 0;
    scanf("%d", &a);
    scanf("%d", &b);  //获取用户输入
    printf("a+b=%d", a + b);
    //scanf()在微软的vs下会有错误，解决方法,有两种
    // 1、定义宏：_CRT_SECURE_NO_WARNINGS
    // 2、#pragma warning(disble:4996)
    
    return 0;
}
```

##### 3.1.4.2、字符串

- 字符串是内存中一段连续的char空间，以 `\0` 结尾
- %s：格式化输出字符串



#### 3.1.5、自定义数据类型

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

### 3.5、流程控制与循环

#### 3.2.1、if - else if - else

```c
#include <stdio.h>

int main() {

    int32_t score = 90;
    if (score > 80) {
        printf("Fine\n");
    } else if (score > 60) {
        printf("OK\n");
    } else {
        printf("Fail\n");
    }

    return 0;
}
```



#### 3.2.2、switch-case语句

```c
#include <stdio.h>

//判断枚举类型的条件比较方便

#define UP 1
#define DOWN 2
#define LEFT 3
#define RIGHT 4

int main() {
//    int32_t dir = UP;
    int32_t dir = DOWN;

    switch (dir) {
        case UP:
            printf("Go UP\n");
            break;
        case DOWN:
            printf("Go DOWN\n");
            break; //如果没有break那么后面的case语句会继续执行
        case LEFT:
            printf("Go LEFT\n");
            break;
        case RIGHT:
            printf("Go RIGHT\n");
            break;
        default:  //dir没有符合上述条件的情况
            printf("dir unknown\n");
    }

    return 0;
}
```



#### 3.2.3、 goto语句

```c
#include <stdio.h>

int main() {

    int i = 0;

    label:
    printf("i=%d\n", i);
    printf("100\n");
    printf("101\n");
    printf("102\n");

    i++;

    if (i <= 100) {
        goto label;
    }

    return 0;
}
```



#### 3.2.4、 for语句、break、continue

```c
#include <stdio.h>

int main() {

//    for (int i = 0; i < 100; i++) {
//
//        printf("Number : %d\n", i);
//
//        for (int j = 0; j < 10; j++) {
//            printf("%d,", j);
//        }
//
//        printf("\n");
//
//        if (i > 50) {
//            break;
//        }
//    }

// -------------------

//    // 乘法表
//    for (int i = 1; i <= 9; i++) {
//        for (int j = 1; j <= i; j++) {
//            printf("%d*%d=%d\t", j, i, i * j);
//
//            if (j >= 5) {  //控制每行最多有5列输出
//                break; //break 跳出当前这个循环
//            }
//
//            if (i * j >= 24) {
//                printf("\n- - - - - - - - - -");
//                printf("\ni=%d,j=%d,i*j=%d", i, j, i * j);
//                goto end;
//            }
//        }
//        printf("\n");
//    }
//
//    end:
//    printf("\nend");

// -------------------
    for (int i = 0; i < 100; i++) {
        printf("Iter %d\n", i);

        if (i == 50) {
            continue; //Iter 50会输出；Number 50不会输出
        }

        printf("Number %d\n", i);
    }

    return 0;
}
```



#### 3.2.5、while循环，do - while循环

```c
#include <stdio.h>

int main() {
// while ------ 先判断，再执行
//    int i = 0;
//    while (i < 100) {
//        if (i % 2) {  // %取余数
//            printf("%d\n", i);
//        }
//
//        i++;
//    }


// do-while ------ 先执行，再判断
    int i = 1;

    do {
        printf("%d\n", i);
        i++;
    } while (i < 100);

    return 0;
}
```



### 3.6、运算符

#### 3.6.1、数学运算符

- `i++` ：先计算表达式的值，然后再++
- `++i` ：先++，然后再计算表达式的值
- `,` ：逗号表达式，先求逗号左边的值，然后求右边的值，整个语句的值是逗号右边的值

```c
#include <stdio.h>
#include <math.h>

int main() {
    int32_t a = (10 + 2 - 8) * 9;

    printf("%d\n", a);

    printf("%f\n", sin(M_PI)); //M_PI 是弧度，不是角度
    return 0;
}
```

#### 3.6.2、复合语句

- ` {}` ： 代码块



#### 3.6.3、空语句

- `;` ：只有一个 分号 的语句就是空语句，空语句在C语言中是合法的，并且是在某些场合必用的



#### 3.6.4、类型转化

- `(类型)` ：强制转换类型，此处的 `小括号` 叫 强转运算符

```c
double bb = (double) 3 / 2; //(double)3，意思是将整数3强制转化为double型
printf("bb = %f\n", bb); // ---> 1.500000
```





#### 3.6.5、逻辑运算符

```c
#include <stdio.h>
#include <stdint.h>

#define MALE 1
#define FMALE 2

int main() {

    //&& || !
    //与 或 非

    int32_t score = 80;
    if (score >= 60 && score <= 100) {
        printf("OK\n");
    } else {
        printf("Fail or invalid score\n");
    }

    score = 70;
    if (score < 60 || score > 100) {
        printf("Fail or invalid score\n");
    } else {
        printf("OK\n");
    }

    int sex = 1;
    if (sex != MALE) {
        printf("The person is famle\n");
    } else {
        printf("The person is male\n");
    }


    return 0;
}
```



#### 3.3.3、位运算



##### 3.3.3.1、基础

```c
#include <stdio.h>
#include <stdint.h>

int main() {

    /**
     * & 位与  只有都是1才是1，否则是0
     * | 位或  只有都是0才是0，否则是1
     * ~ 位非  无符号数字（1变0，0变1）； 有符号数字（以该数据的取值范围的中间为中轴，镜像取反。）
     * ^ 异或  相同则0，不同则1
     * >> 右移位  替换掉最大权值位(最大权值位的位数为1)右侧的位数，并将原先的最大权值位的位数变为零
     * << 左移位  末尾补0
     */

    int a = 0b10; //二进制
    int b = 0b01; //二进制

    printf("%d\n", a & b); //或
    printf("%d\n", a | b); //非
    printf("%d\n", a ^ b); //非

    uint8_t c = 1; //1在内存中保存的是 0b00000001, 求反之后变成 0b11111110
    uint8_t d = 0b11111110;
    c = ~c;
    printf("%d\n", c); // 位非 --> 254
    printf("%d\n", d); // --> 254

    // 无符号数字的 位非
    int8_t e = 1;
    e = ~e;
    printf("%d\n", e); // --> -2

    // 移位运算
    int f = 8; //0b1000
    printf("f=%d\n", f >> 1); //0b100    --> 4
    printf("f=%d\n", f << 1); //0b10000  --> 16
    //位运算速度最快

    int8_t g = 127; //0b111 1111
    printf("g=%d\n", g >> 1); //0b11 1111    --> 63
    printf("g=%d\n", g << 2); //0b1 1111 1100  --> 508

    return 0;
}
```




### 3.7、数组

#### 3.7.1、定义一个数组

```c
#include <stdio.h>

int main() {
    int array_1[10];//定义一个 名字为array_1 的一维数组，一共有10个元素，每个元素都是int类型的

    array_1[0] = 10;
    array_1[1] = 20;
    array_1[2] = 30;

    printf("%d\n", array_1[1]);

    return 0;
}
```

#### 3.7.2、数组在内存的存储方式

- 数组在内存中就是一段连续的空间

- 每个元素的数据类型是一样的
- C语言编译器不会帮你检查数组的下标是否有效
- 数组维数越多，代码可读性越差



### 3.8、字符串

- `字符串` 是在内存中 `以 0 结尾` 的一个 `char数组`
- 如果将一个字符串当做有符号的char处理，那么标准的ASKII字符一定是一个正数，汉字的第一个字节一定是负数
- rand()：伪随机数生成器
- srand()：随机数种子发生器 与 rand()配合使用
- `scanf("%s",str1)` ：
  - 当输入的字符个数，大于str1的长度，会报 `越界` 错误
  - scanf 将 `回车` 和 `空格` 都当做 字符串结束的标志
  - windows下，需要   `#define _CRT_SECURE_NO_WARNINGS`
- gets()

  - gets()函数，认为 `回车` 是字符串结束标志，`空格` 不是字符串结束标志
  - 与scanf()一样，存在缓冲区溢出的问题
  - gets()函数 不能用 ”%s“或者”%d“之类的字符 进行转义，只能接受字符串的输入
- atoi()：将 `字符串` 转化为 `整数`
- fgets()：

  - 调用方式：`fgets(str1, 5, stdin)`
  - 只要 该函数的第二个参数的值小于 str1字符数组的实际长度，就能避免缓冲区溢出的问题。
- puts()：
  - 只能输出 `字符串`，末尾自动加 `'\n'`
- fputs()：
  - 末尾不自动加  `'\n'`

#### 3.8.2、操作 字符串 的函数

**strlen(a)**

返回 字符串 占用的 字节数

```C
char str1[100] = "hello world";
int len_str1 = strlen(str1);
printf("len_str1 = %d\n", len_str1); //返回 字节数
```



**strcat(a,b)**

windows下，需要   `#define _CRT_SECURE_NO_WARNINGS`

将字符串b 合并到字符串a的后面

```c
char str1[100] = "hello world";
int len_str1 = strlen(str1);
printf("len_str1 = %d\n", len_str1); //返回 字节数

char str2[100] = "你好世界";
strcat(str2, str1);
printf("str2 = %s\n", str2);
```



**strncat(a,b,num)**

可以限制 追加的时候加多少个字符

```c
char str1[100] = "hello world";
int len_str1 = strlen(str1);
printf("len_str1 = %d\n", len_str1); //返回 字节数

char str3[100] = "多少个";
strncat(str3, str1, 2);
printf("str3 = %s\n", str3);
return 0;
```



**strcmp(s1,s2)**

s1的 ASCII 码大于s2 返回1，小于返回-1，等于返回0

```c
char s1[100] = "hello";
char s2[100] = "world";
int a = strcmp(s1, s2);
printf("a = %d\n", a);

if (strcmp(s1, s2)) {  //返回非0表示 两个字符串 不相同
    printf("s1 和 s2 不相同");
} else {               //返回 0 表示两个 字符串相同
    printf("s1 和 s2 相同");
}
```



**strncmp(s1,s2,num)**

比较前 mun个字符是否相同

```c
char s1[100] = "abc123";
char s2[100] = "abc456";

if (strncmp(s1, s2, 3)) { // 比较前3个字符 是否相同
    printf("s1 和 s2 不相同\n");
} else {               //返回 0 表示两个 字符串 前3个字符相同
    printf("s1 和 s2 相同\n");
}
```



**strcpy(s1,s2)**

```c
char s1[100] = "123456";
char s2[100] = "abcdef";

strcpy(s1, s2);
printf("s1 = %s\n", s1);  // -->s1 = abcdef
```



**strncpy(s1,s2,num)**

把s2的前num个字符，复制到s1的前num个字符

```c
char s1[100] = "123456";
char s2[100] = "abcdef";

strcpy(s1, s2);
printf("s1 = %s\n", s1);  // -->s1 = ab3456
```



**sprintf()**

将格式化后的字符串 输出到第一个参数指定的字符串中

```c
int i = 200;
char s1[100] = {0};
sprintf(s1,"i = %d",i); //将格式化后的字符串 输出到第一个参数指定的字符串中

printf("%s\n",s1);
```



**sscanf()**

```c
char s1[100] = "11+12=";

int a = 0;
int b = 0;
sscanf(s1, "%d+%d", &a, &b); //从第一个参数指定的字符串中，挖取数据
printf("a + b = %d\n", a + b);// --> a + b = 23
```



**strchr(str,char)**

在str这个字符串中查找char这个字符，如果查到了，返回 char后的全部字符串。如果查不到char，返回(null)。

```c
char str1[100]="hello world";
const char *buf = strchr(str1,'o');
printf("%s\n",buf);
```



**strstr(str1,str2)**

```c
char str1[100] = "hello world";
char const *buf = strstr(str1, "ll");
printf("%s\n", buf);
```



**strtok(str1,str2)**

用str1中的 str2来对str1进行分割

```c
    char str1[100] = "123_asd_qlwer_nb9_48";
    char const *buf = strtok(str1, "_");
	//printf("%s\n", buf); // --> 123

    while (buf) {
        printf("%s\n", buf);
        buf = strtok(NULL, "_"); //如果strtok没有找到指定的分割符号，那么返回NULL
    } //除了第一次，每个下一次对同一个字符串进行分割，strtok()的第一个参数传NULL
```



**atof()**

转化为float

```c
char str1[100] = "123";
long num1 = atol(str1);
printf("%ld\n", num1); // -->123
```



**atol()**

转化为long

```c
char str1[100] = "123";
float num2 = atof(str1);
printf("%.8f\n", num2);  // --->123.00000000
```



### 3.9、函数

#### 3.7.1、声明函数

```c

```



### 3.10、指针

- 指针变量 也是一个 变量
- 指针存放的内容是一个地址，该地址指向一块内存空间
- 计算机内存的最小单位是byte，每一个byte的内存都有一个唯一的编号，这个编号就是内存地址。编号在32位的系统下是一个32位的整数，在64位的系统下是一个64位的整数。
- 指针变量的值一般不能直接赋值一个整数，而是通过取变脸地址的方式赋值。
- `void *p2`：`无类型指针`，只是一个指针变量，而不指向任何具体的数据类型
- `p2 = NULL`：将指针赋值为空，指向NULL的指针，称为`空指针`
- 野指针：没有具体指向任何变量地址的指针叫 `野指针`。
  - 假设p运气好，随机的指向了一个程序有效的内存地址，那么代码不会出错；如果运气不好，指向一个无效地址，那么一定会出错。
  - 程序中应该避免野指针的存在，因为野指针是导致程序崩溃的主要原因。
- `空指针`是合法的，`野指针`是危险的



#### 3.10.1、指针的兼容性

- 原则上，一定是相同类型的指针指向相同类型的变量地址，不能用一种类型的指针指向另一种类型的变量地址。





### 3.8、关键字

#### 3.8.1、int

- 整形
- int不管是在32位系统下，还是64位系统下，不论是windows还是unix都是4个字节



#### 3.8.2、sizeof

- 功能：求指定数据类型在内存中的大小，返回值即为该数据所占的字节数
- 单位：字节
- 返回的字节数，跟操作系统的位数有关。同一个数据在不同的操作系统中，`sizeof()` 返回的字节数可能不同
- （size_t 类型，后续讨论）



#### 3.8.3、short

- 短整型，在32位系统下是2个字节，16个比特



#### 3.8.4、long

##### 3.8.4.1、long

- 整形（***尽量少用long，其所占字节数在不同的系统有差异，代码移植麻烦***）
- 在32位的系统下，long都是4个字节
- 在unix系统下，long是8个字节

##### 3.8.4.2、long long

- 长整型

- 不管在什么操作系统下，都是8个字节，64位
- 对于32位的操作系统，CPU寄存器是32位，所以计算long long 类型的数据，效率很低



#### 3.8.5、unsigned

- 无符号数字

##### 3.8.5.1、unsigned int

- 无符号整数
- 永远是正数

##### 3.8.5.2、unsigned short

- 无符号短整型

##### 3.8.5.3、unsigned long

- 无符号整型

##### 3.8.5.4、unsigned long long

- 无符号长整形



#### 3.8.6、return

- 一个函数遇到 `return` 语句就终止了



#### 3.8.7、char



#### 3.8.8、const

- 声明一个不能改变值的常量



#### 3.8.9、volatile

- 代表 变量（在内存中）是一个可能被CPU指令之外的地方（例如cpu所在服务器之外的设备）改变的
- 编译器不会针对这个变量去优化代码



#### 3.8.10、register

- 代表 变量 不是在内存里面的，而是在CPU的寄存器里面的
- 但是register是建议型的指令，而不是命令型的指令（比如：如果CPU没有可用的寄存器了，就会忽略这个指令）



## 四、C语言进阶

### 4.1、常用的预处理

#### 4.1.1、预设常量

```c

```

#### 4.1.2、条件预处理

```c

```


#### 4.1.3、防止偷文件重复引入

```c

```


#### 4.1.4、宏函数

```c

```


#### 4.1.5、宏函数参数连接

```c

```


#### 4.1.1、宏函数可变参数

```c

```

### 4.2、指针

#### 4.2.1、指针基本介绍

```c

```


#### 4.2.2、函数指针

```c

```


#### 4.2.3、无类型指针

```c

```

### 4.3、结构体和共同体

#### 4.3.1、结构体

```c

```


#### 4.3.2、结构体指针

```c

```


#### 4.3.3、共同体

```c

```

### 4.4、文件操作

#### 4.4.1、写出文件

```c

```



#### 4.4.2、读取文件

```c

```



#### 4.4.3、格式化写出和读取文件

```c

```

