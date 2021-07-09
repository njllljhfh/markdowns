# Go入门指南

[Go入门指南](https://www.bookstack.cn/read/the-way-to-go_ZH_CN/eBook-directory.md)



# 第4章 基本结构和基本数据类型

## 4.3常量

- 常量的值必须是能够在编译时就能够确定的；你可以在其赋值表达式中涉及计算过程，但是所有用于计算的值必须在编译期间就能获得。



---



## 4.4变量

### 4.4.1 简介

- 变量的类型也可以在运行时实现自动推断

### 4.4.2值类型和引用类型

程序中所用到的内存在计算机中使用一堆箱子来表示（这也是人们在讲解它的时候的画法），这些箱子被称为 “ 字 ”。根据不同的处理器以及操作系统类型，所有的字都具有 32 位（4 字节）或 64 位（8字节）的相同长度；所有的字都使用相关的内存地址来进行表示（以十六进制数表示）。



**值类型：**

- 所有像 int、float、bool 和 string 这些基本类型都属于值类型，使用这些类型的变量直接指向存在内存中的值。
- 当使用等号 `=` 将一个变量的值赋值给另一个变量时，如：`j = i`，实际上是在内存中将 `i` 的值进行了拷贝。
- 值类型的变量的值存储在 `栈` 中。



**引用类型：**

- 一个引用类型的变量 r1 存储的是 r1 的值所在的内存地址（数字），或内存地址中第一个字所在的位置。

- 当使用赋值语句 `r2 = r1` 时，只有引用（地址）被复制。
- 在 Go 语言中，指针（第 4.9 节）属于引用类型，其它的引用类型还包括 slices（第 7 章），maps（第 8 章）和 channel（第 13 章）。被引用的变量会存储在 `堆` 中，以便进行垃圾回收，且比栈拥有更大的内存空间。



### 4.4.3打印

- 函数 `fmt.Print` 和 `fmt.Println` 会自动使用格式化标识符 `%v` 对字符串进行格式化，两者都会在每个参数之间自动增加空格，而后者还会在字符串的最后加上一个换行符。



### 4.4.4 简短形式，使用 := 赋值操作符

- 这是使用变量的首选形式，但是它只能被用在 `函数体内`，而不可以用于全局变量的声明与赋值。使用操作符 `:=` 可以高效地创建一个新的变量，称之为初始化声明。

- 全局变量是允许声明但不使用。

- 这被称为 **并行** 或 **同时** 赋值。

    ```go
    a, b, c = 5, 7, "abc"
    ```



### 4.4.5 init 函数

- 变量除了可以在全局声明中初始化，也可以在 init 函数中初始化。这是一类非常特殊的函数，它不能够被人为调用，而是在每个包完成初始化后自动执行，并且执行优先级比 main 函数高。
- 每个源文件都只能包含一个 init 函数。初始化总是以单线程执行，并且按照包的依赖关系顺序执行。



---



## 4.5 基本类型和运算符

只有两个类型相同的值才可以和二元运算符结合，另外要注意的是，Go 是强类型语言，因此不会进行隐式转换，任何不同类型之间的转换都必须显式说明（第 4.2 节）。Go 不存在像 C 那样的运算符重载，表达式的解析顺序是从左至右。



### 4.5.1 布尔类型 bool

**值之间的比较：**

- Go 对于值之间的比较有非常严格的限制，只有两个类型相同的值才可以进行比较，如果值的类型是接口（interface，第 11 章），它们也必须都实现了相同的接口。如果其中一个值是常量，那么另外一个值的类型必须和该常量类型相兼容的。如果以上条件都不满足，则其中一个值的类型必须在被转换为和另外一个值的类型相同之后才可以进行比较。



`&&`  **和**  `||`  **的特性：**

- 在 Go 语言中，`&&`  和  `||`  是具有快捷性质的运算符，当运算符左边表达式的值已经能够决定整个表达式的值的时候（`&&` 左边的值为 false，`||` 左边的值为 true），运算符右边的表达式将不会被执行。利用这个性质，如果你有多个条件判断，应当将计算过程较为复杂的表达式放在运算符的右侧以减少不必要的运算。



### 4.5.2 数字类型

#### 4.5.2.1 整型 int 和浮点型 float

**整形：**

Go 也有基于架构的类型，例如：int、uint 和 uintptr。

这些类型的长度都是根据运行程序所在的操作系统类型所决定的：

- `int` 和 `uint` 在 32 位操作系统上，它们均使用 32 位（4 个字节），在 64 位操作系统上，它们均使用 64 位（8 个字节）。
- `uintptr` 的长度被设定为足够存放一个指针即可。

`int` 型是计算最快的一种类型。

整型的零值为 0。



**浮点型：**

- Go 语言中没有 float 类型。（Go语言中只有 `float32` 和 `float64`）没有double类型。

浮点型的零值为 0.0。



**注意：**

- 你应该尽可能地使用 `float64`，因为 `math` 包中所有有关数学运算的函数都会要求接收这个类型。

- 你可以通过增加前缀 0 来表示 8 进制数（如：077），增加前缀 0x 来表示 16 进制数（如：0xFF），以及使用 e 来表示 10 的连乘（如： 1e3 = 1000，或者 6.022e23 = 6.022 x 1e23）。

- Go 中不允许不同类型之间的混合使用，但是对于常量的类型限制非常少，因此允许常量之间的混合使用，下面这个程序很好地解释了这个现象（该程序无法通过编译）：

    ```go
    package main
    func main() {
        var a int
        var b int32
        a = 15
        b = a + a  // 编译错误（cannot use a + a (type int) as type int32 in assignment） 
        b = b + 5  // 因为 5 是常量，所以可以通过编译
    }
    ```



#### 4.5.2.2 复数

 **复数:**

- Go 拥有以下复数类型：

    ```go
    complex64 (32 位实数和虚数)
    complex128 (64 位实数和虚数)
    ```

- 复数支持和其它数字类型一样的运算。当你使用等号 `==` 或者不等号 `!=` 对复数进行比较运算时，注意对精确度的把握。`cmath` 包中包含了一些操作复数的公共方法。如果你对内存的要求不是特别高，最好使用 complex128 作为计算类型，因为相关函数都使用这个类型的参数。



#### 4.5.2.3 位运算

**位运算：**

位运算只能用于整数类型的变量，且需当它们拥有等长位模式时。

`%b` 是用于表示位的格式化标识符。

**二元运算符**

- 按位与 `&`：

- 按位或 `|`：

- 按位异或 `^`：

- 位清除 `&^`：将指定位置上的值设置为 0。

    ```go
    package main
    
    import "fmt"
    
    func main() {
    	a := 12
    	b := 4
    	c := a &^ b
    	fmt.Printf("a: %08b\n", a)
    	fmt.Printf("b: %08b\n", b)
    	fmt.Printf("c: %08b\n", c)
    	fmt.Printf("c: %#v   二进制：%08b\n", c, c)
    
    	/*输出：
    	a: 00001100
    	b: 00000100
    	c: 00001000
    	c: 8   二进制：00001000
    	*/
    }
    
    ```

    

**一元运算符**

- 按位补足 `^`：

- 位左移 `<<`：

- 位右移 `>>`：



#### 4.5.2.4 逻辑运算符

Go 中拥有以下逻辑运算符：`==`、`!=`（第 4.5.1 节）、`<`、`<=`、`>`、`>=`。

它们之所以被称为逻辑运算符是因为它们的运算结果总是为布尔值 `bool`。例如：

```
b3 := 10 > 5 // b3 is true
```



#### 4.5.2.5 算术运算符

常见可用于整数和浮点数的二元运算符有 `+`、`-`、`*` 和 `/`。

- `/` 对于整数运算而言，结果依旧为整数，例如：`9 / 4 -> 2`。

- 取余运算符只能作用于整数：`9 % 4 -> 1`。
- 整数除以 0 可能导致程序崩溃，将会导致运行时的恐慌状态（如果除以 0 的行为在编译时就能被捕捉到，则会引发编译错误）；第 13 章将会详细讲解如何正确地处理此类情况。
- 浮点数除以 0.0 会返回一个无穷尽的结果，使用 `+Inf` 表示。

- 你可以将语句 `b = b + a` 简写为 `b+=a`，同样的写法也可用于 `-=`、`*=`、`/=`、`%=`。
- 对于整数和浮点数，你可以使用一元运算符 `++`（递增）和 `--`（递减），但只能用于后缀。
    - 同时，带有 `++` 和 `--` 的只能作为语句，而非表达式，因此 `n = i++` 这种写法是无效的，其它像 `f(i++)` 或者 `a[i]=b[i++]` 这些可以用于 C、C++ 和 Java 中的写法在 Go 中也是不允许的。

- 在运算时 **溢出** 不会产生错误，Go 会简单地将超出位数抛弃。如果你需要范围无限大的整数或者有理数（意味着只被限制于计算机内存），你可以使用标准库中的 `big` 包，该包提供了类似 `big.Int` 和 `big.Rat` 这样的类型（第 9.4 节）。



#### 4.5.2.6 随机数

一些像游戏或者统计学类的应用需要用到随机数。`rand` 包实现了伪随机数的生成。

```go
package main

import (
	"fmt"
	"math/rand"
	"time"
)

func main() {
	for i := 0; i < 10; i++ {
		a := rand.Int()
		fmt.Printf("%d / ", a)
	}
	for i := 0; i < 5; i++ {
		r := rand.Intn(8)
		fmt.Printf("%d / ", r)
	}
	fmt.Println()

	timens := int64(time.Now().Nanosecond())
	rand.Seed(timens)
	for i := 0; i < 10; i++ {
		fmt.Printf("%2.2f / ", 100*rand.Float32())
	}
}
```

函数 `rand.Float32` 和 `rand.Float64` 返回介于 [0.0, 1.0) 之间的伪随机数，其中包括 0.0 但不包括 1.0。函数 `rand.Intn` 返回介于 [0, n) 之间的伪随机数。

你可以使用 `rand.Seed(value)` 函数来提供伪随机数的生成种子，一般情况下都会使用当前时间的纳秒级数字（第 4.8 节）。



### 4.5.3 运算符与优先级



### 4.5.4 类型别名

- 当你在使用某个类型时，你可以给它起另一个名字，然后你就可以在你的代码中使用新的名字（用于简化名称或解决名称冲突）。

- 在 `type TZ int` 中，TZ 就是 int 类型的新名称（用于表示程序中的时区），然后就可以使用 TZ 来操作 int 类型的数据。

    ```go
    package main
    
    import "fmt"
    
    type TZ int
    
    func main() {
        var a, b TZ = 3, 4
        c := a + b
        fmt.Printf("c has the value: %d", c) // 输出：c has the value: 7
    }
    ```

- 实际上，类型别名得到的新类型并非和原类型完全相同，新类型不会拥有原类型所附带的方法（第 10 章）；TZ 可以自定义一个方法用来输出更加人性化的时区信息。



### 4.5.5 字符类型

严格来说，这并不是 Go 语言的一个类型，字符只是整数的特殊用例。`byte` 类型是 `uint8` 的别名，对于只占用 1 个字节的传统 ASCII 编码的字符来说，完全没有问题。例如：`var ch byte = 'A'`；字符使用单引号括起来。

在 ASCII 码表中，A 的值是 65，而使用 16 进制表示则为 41，所以下面的写法是等效的：

```go
var ch byte = 65 或 var ch byte = '\x41'
```

（`\x` 总是紧跟着长度为 2 的 16 进制数）



包 `unicode` 包含了一些针对测试字符的非常有用的函数（其中 `ch` 代表字符）：

- 判断是否为字母：`unicode.IsLetter(ch)`
- 判断是否为数字：`unicode.IsDigit(ch)`
- 判断是否为空白符号：`unicode.IsSpace(ch)`

这些函数返回一个布尔值。



包 `utf8` 拥有更多与 rune 类型相关的函数。



---



## 4.6 字符串

Go 支持以下 2 种形式的字面值：

- 解释字符串：

    该类字符串使用双引号括起来，其中的相关的转义字符将被替换，这些转义字符包括：

    - `\n`：换行符
    - `\r`：回车符
    - `\t`：tab 键
    - `\u` 或 `\U`：Unicode 字符
    - `\\`：反斜杠自身

- 非解释字符串：

    该类字符串使用反引号括起来，支持换行，例如：

    ```go
    `This is a raw string \n` 中的 `\n` 会被原样输出。
    ```

- `string` 类型的零值为长度为零的字符串，即空字符串 `""`。

- **注意事项** 获取字符串中某个字节的地址的行为是非法的，例如：`&str[i]`。
- 在循环中使用加号 `+` 拼接字符串并不是最高效的做法，更好的办法是使用函数 `strings.Join()`（第 4.7.10 节），有没有更好的办法了？有！使用字节缓冲（`bytes.Buffer`）拼接更加给力（第 7.2.6 节）！



---



## 4.7 strings 和 strconv 包



### 4.7.12 字符串与其它类型的转换

与字符串相关的类型转换都是通过 `strconv` 包实现的。

该包包含了一些变量用于获取程序运行的操作系统平台下 int 类型所占的位数，如：`strconv.IntSize`。



**其他类型转字符串：**

任何类型 **T** 转换为字符串总是成功的。

针对从数字类型转换到字符串，Go 提供了以下函数：

- `strconv.Itoa(i int) string` 返回数字 i 所表示的字符串类型的十进制数。
- `strconv.FormatFloat(f float64, fmt byte, prec int, bitSize int) string` 将 64 位浮点型的数字转换为字符串，其中 `fmt` 表示格式（其值可以是 `'b'`、`'e'`、`'f'` 或 `'g'`），`prec` 表示精度，`bitSize` 则使用 32 表示 float32，用 64 表示 float64。



**字符串转其他类型：**

将字符串转换为其它类型 **tp** 并不总是可能的，可能会在运行时抛出错误 `parsing "…": invalid argument`。

针对从字符串类型转换为数字类型，Go 提供了以下函数：

- `strconv.Atoi(s string) (i int, err error)` 将字符串转换为 int 型。
- `strconv.ParseFloat(s string, bitSize int) (f float64, err error)` 将字符串转换为 float64 型。



---



## 4.8 时间和日期



---

## 4.9 指针



使用一个指针引用一个值被称为间接引用。

当一个指针被定义后没有分配到任何变量时，它的值为 `nil`。

一个指针变量通常缩写为 `ptr`。





**注意事项**

- 你不能获取字面量或常量的地址，例如：

```go
const i = 5
ptr := &i //error: cannot take the address of i

ptr2 := &10 //error: cannot take the address of 10
```

- Go 语言为程序员提供了控制数据结构的指针的能力；但是，你不能进行指针运算。因此 `c = *p++` 在 Go 语言的代码中是不合法的。

- 指针的一个高级应用是你可以传递一个变量的引用（如函数的参数），这样不会传递变量的拷贝。指针传递是很廉价的，只占用 4 个或 8 个字节。当程序在工作中需要占用大量的内存，或很多变量，或者两者都有，使用指针会减少内存占用和提高效率。被指向的变量也保存在内存中，直到没有任何指针指向它们，所以从它们被创建开始就具有相互独立的生命周期。

- 指针也可以指向另一个指针，并且可以进行任意深度的嵌套，导致你可以有多级的间接引用，但在大多数情况这会使你的代码结构不清晰。

- 对一个空指针的反向引用是不合法的，并且会使程序崩溃

    ```go
    package main
    func main() {
        var p *int = nil
        *p = 0
    }
    // in Windows: stops only with: <exit code="-1073741819" msg="process crashed"/>
    // runtime error: invalid memory address or nil pointer dereference
    ```

    



---



# 第5章 控制结构

到目前为止，我们看到的 Go 程序都是从 main() 函数开始执行，然后按顺序执行该函数体中的代码。但我们经常会需要只有在满足一些特定情况时才执行某些代码，也就是说在代码里进行条件判断。针对这种需求，Go 提供了下面这些条件结构和分支结构：

- if-else 结构
- switch 结构
- select 结构，用于 channel 的选择（第 14.4 节）



可以使用迭代或循环结构来重复执行一次或多次某段代码（任务）：

- for (range) 结构



一些如 `break` 和 `continue` 这样的关键字可以用于中途改变循环的状态。

此外，你还可以使用 `return` 来结束某个函数的执行，或使用 `goto` 和标签来调整程序的执行位置。



Go 完全省略了 `if`、`switch` 和 `for` 结构中条件语句两侧的括号，相比 Java、C++ 和 C# 中减少了很多视觉混乱的因素，同时也使你的代码更加简洁。



## 5.1 if-else 结构

- if 可以包含一个初始化语句（如：给一个变量赋值）。这种写法具有固定的格式（在初始化语句后方必须加上分号）

    ```go
    // 但要注意的是，使用简短方式 := 声明的变量的作用域只存在于 if 结构中（在 if 结构的大括号之间，如果使用 if-else 结构则在 else 代码块中变量也会存在）。
    if val := 10; val > max {
        // do something
    }
    ```



## 5.2 测试多返回值函数的错误

如果我们想要在错误发生的同时终止程序的运行，我们可以使用 `os` 包的 `Exit` 函数：

```go
// （此处的退出代码 1 可以使用外部脚本获取到）
if err != nil {
    fmt.Printf("Program stopping with error %v", err)
    os.Exit(1)
}
```



可以将错误的获取放置在 if 语句的初始化部分：

```go
if err := file.Chmod(0664); err != nil {
    fmt.Println(err)
    return err
}
```



## 5.3 switch 结构

在下面这个代码片段中，变量 a 和 b 被平行初始化，然后作为判断条件：

```go
switch a, b := x[i], y[j]; {
    case a < b: t = -1
    case a == b: t = 0
    case a > b: t = 1
}
```



`fallthrough` 的特性：

- Go里面 switch 默认相当于每个case最后带有break，匹配成功后不会自动向下执行其他case，而是跳出整个switch, 但是可以使用fallthrough强制执行后面的case代码。
- fallthrough不能用在switch的最后一个分支
- fallthrough到下一个case块时，**不执行case匹配检查！**



## 5.4 for 结构

- 循环开始前，会执行且仅会执行一次初始化语句。

- 特别注意，永远不要在循环体内修改计数器，这在任何语言中都是非常差的实践！

- 还可以在循环中同时使用多个计数器：

    ```go
    for i, j := 0, N; i < j; i, j = i+1, j-1 {}
    ```

- ASCII 编码的字符占用 1 个字节，既每个索引都指向不同的字符，而非 ASCII 编码的字符（占有 2 到 4 个字节）不能单纯地使用索引来判断是否为同一个字符。我们会在第 5.4.4 节解决这个问题。

    ```go
    package main
    
    import "fmt"
    
    func main() {
        str := "Go is a beautiful language!"
        fmt.Printf("The length of str is: %d\n", len(str))
        for ix :=0; ix < len(str); ix++ {
            fmt.Printf("Character on position %d is: %c \n", ix, str[ix])
        }
        
        str2 := "日本語"
        fmt.Printf("The length of str2 is: %d\n", len(str2))
        for ix :=0; ix < len(str2); ix++ {
            fmt.Printf("Character on position %d is: %c \n", ix, str2[ix])
        }
    }
    ```
    
- 死循环： `for { }`





### 5.4.4 for-range 结构

- 这是 Go 特有的一种的迭代结构，您会发现它在许多情况下都非常有用。它可以迭代任何一个集合（包括数组和 map，详见第 7 和 8 章）。语法上很类似其它语言中 foreach 语句，但您依旧可以获得每次迭代所对应的索引。一般形式为：`for ix, val := range coll { }`。

- 要注意的是，`val` 始终为集合中对应索引的值拷贝，因此它一般只具有只读性质，对它所做的任何修改都不会影响到集合中原有的值（**如果 `val` 为指针，则会产生指针的拷贝，依旧可以修改集合中的原值**）。一个字符串是 Unicode 编码的字符（或称之为 `rune`）集合，因此您也可以用它迭代字符串：

    ```go
    // 运行代码，从输出结果中我们可以看到，常用英文字符使用 1 个字节表示，而汉字使用 3 个字符表示。
    func main() {
    	str := "Go is a beautiful language!"
    	fmt.Printf("The length of str is: %d\n", len(str))
    	for pos, char := range str {
    		fmt.Printf("Character on position %d is: %c \n", pos, char)
    	}
    	fmt.Println()
    
    	str2 := "Chinese: 中国人"
    	fmt.Printf("The length of str2 is: %d\n", len(str2))
    	for pos, char := range str2 {
    		fmt.Printf("character %c starts at byte position %d\n", char, pos)
    	}
    	fmt.Println()
    
    	fmt.Println("index    int(rune)    rune    char bytes")
    	for index, rune_ := range str2 {
    		fmt.Printf("%-2d      %d      %U '%c' % X\n", index, rune_, rune_, rune_, []byte(string(rune_)))
    	}
    }
    ```



## 5.5 Break 与 continue

break:

- 一个 break 的作用范围为该语句出现后的最内部的结构，它可以被用于任何形式的 for 循环（计数器、条件判断等）。
- 但在 switch 或 select 语句中（详见第 13 章），break 语句的作用结果是跳过整个代码块，执行后续的代码。



continue:

- 关键字 continue 忽略剩余的循环体而直接进入下一次循环的过程，但不是无条件执行下一次循环，执行之前依旧需要满足循环的判断条件。
- 另外，关键字 continue 只能被用于 for 循环中。





## 5.6 标签 与 goto(不推荐使用)

for、switch 或 select 语句都可以配合标签（label）形式的标识符使用，即某一行第一个以冒号（`:`）结尾的单词（gofmt 会将后续代码自动移至下一行）。

```go
package main

import "fmt"

func main() {
LABEL1:
	for i := 0; i <= 5; i++ {
		for j := 0; j <= 5; j++ {
			if j == 4 {
				continue LABEL1
				// break LABEL1
			}
			fmt.Printf("i is: %d, and j is: %d\n", i, j)
		}
	}
}

```

您可以看到当 j=4  和  j=5 的时候，没有任何输出：标签的作用对象为外部循环，因此 i 会直接变成下一个循环的值，而此时 j 的值就被重设为 0，即它的初始值。如果将 continue 改为 break，则不会只退出内层循环，而是直接退出外层循环了。



还可以使用 goto 语句和标签配合使用来模拟循环。

```go
package main

func main() {
	i := 0
HERE:
	print(i)
	i++
	if i == 5 {
		return
	}
	goto HERE
}
//上面的代码会输出 01234
```



**特别注意** 使用标签和 goto 语句是不被鼓励的：它们会很快导致非常糟糕的程序设计，而且总有更加可读的替代方案来实现相同的需求。



一个建议使用 goto 语句的示例会在第 15.1 章的 simple_tcp_server.go 中出现：示例中在发生读取错误时，使用 goto 来跳出无限读取循环并关闭相应的客户端链接。



定义但未使用标签会导致编译错误：`label … defined and not used`。

---



# 第6章 函数（function）

函数是 Go 里面的基本代码块：Go 函数的功能非常强大，以至于被认为拥有函数式编程语言的多种特性。



## 6.1 介绍

Go是编译型语言，所以函数编写的顺序是无关紧要的；鉴于可读性的需求，最好把 `main()` 函数写在文件的前面，其他函数按照一定逻辑顺序进行编写（例如函数被调用的顺序）。

简单的 `return` 语句也可以用来结束 for 死循环，或者结束一个协程（goroutine）。



Go 里面有三种类型的函数：

- 普通的带有名字的函数
- 匿名函数或者lambda函数（参考 [第 6.8 节](https://www.bookstack.cn/read/the-way-to-go_ZH_CN/eBook-06.8.md)）
- 方法（Methods，参考 [第 10.6 节](https://www.bookstack.cn/read/the-way-to-go_ZH_CN/eBook-10.6.md)）



除了main()、init()函数外，其它所有类型的函数都可以有参数与返回值。函数参数、返回值以及它们的类型被统称为***函数签名***。



假设 f1 需要 3 个参数 `f1(a, b, c int)`，同时 f2 返回 3 个参数 `f2(a, b int) (int, int, int)`，就可以这样调用 f1：`f1(f2(a, b))`。



函数重载（function overloading）指的是可以编写多个同名函数，只要它们拥有不同的形参与/或者不同的返回值，在 Go 里面函数重载是***不被允许的***。这将导致一个编译错误：

```go
funcName redeclared inthis book, previous declaration at lineno
```



如果需要申明一个在外部定义的函数，你只需要给出函数名与函数签名，不需要给出函数体：

```
func flushICache(begin,end uintptr)// implemented externally
```

**函数也可以以申明的方式被使用，作为一个函数类型**，就像：

```
type binOp func(int,int)int
```

在这里，不需要函数体 `{}`。

函数是一等值（first-class value）：它们可以赋值给变量，就像 `add := binOp` 一样。

这个变量知道自己指向的函数的签名，所以给它赋一个具有不同签名的函数值是不可能的。

函数值（functions value）之间可以相互比较：如果它们引用的是相同的函数或者都是 nil 的话，则认为它们是相同的函数。函数不能在其它函数里面声明（不能嵌套），不过我们可以通过使用匿名函数（参考 [第 6.8 节](https://www.bookstack.cn/read/the-way-to-go_ZH_CN/eBook-06.8.md)）来破除这个限制。

目前 Go 没有泛型（generic）的概念，也就是说它不支持那种支持多种类型的函数。不过在大部分情况下可以通过接口（interface），特别是空接口与类型选择（type switch，参考 [第 11.12 节](https://www.bookstack.cn/read/the-way-to-go_ZH_CN/eBook-11.12.md)）与/或者通过使用反射（reflection，参考 [第 6.8 节](https://www.bookstack.cn/read/the-way-to-go_ZH_CN/eBook-06.8.md)）来实现相似的功能。使用这些技术将导致代码更为复杂、性能更为低下，所以在非常注意性能的的场合，最好是为每一个类型单独创建一个函数，而且代码可读性更强。



## 6.2 函数参数与返回值

函数定义时，它的形参一般是有名字的，不过我们也可以定义没有形参名的函数，只有相应的形参类型，就像这样：`func f(int, int, float64)`。



### 6.2.1 按值传递（call by value）按引用传递（call by reference）

Go 默认使用按值传递来传递参数，也就是传递参数的副本。函数接收参数副本之后，在使用变量的过程中可能对副本的值进行更改，但不会影响到原来的变量，比如 `Function(arg1)`。

如果你希望函数可以直接修改参数的值，而不是对参数的副本进行操作，你需要将参数的地址（变量名前面添加&符号，比如 &variable）传递给函数，这就是按引用传递，比如 `Function(&arg1)`，此时传递给函数的是一个指针。如果传递给函数的是一个指针，指针的值（一个地址）会被复制，但指针的值所指向的地址上的值不会被复制；我们可以通过这个指针的值来修改这个值所指向的地址上的值。（**译者注：指针也是变量类型，有自己的地址和值，通常指针的值指向一个变量的地址。所以，按引用传递也是按值传递。**）

几乎在任何情况下，传递指针（一个32位或者64位的值）的消耗都比传递副本来得少。

在函数调用时，像切片（slice）、字典（map）、接口（interface）、通道（channel）这样的引用类型都是默认使用引用传递（即使没有显式的指出指针）。

如果一个函数需要返回四到五个值，我们可以传递一个切片给函数（如果返回值具有相同类型）或者是传递一个结构体（如果返回值具有不同的类型）。因为传递一个指针允许直接修改变量的值，消耗也更少。



### 6.2.2 命名的返回值（named return variables）

命名返回值作为结果形参（result parameters）被初始化为相应类型的零值，当需要返回的时候，我们只需要一条简单的不带参数的return 语句。需要注意的是，即使只有一个命名返回值，也需要使用 `()` 括起来（参考 [第 6.6 节](https://www.bookstack.cn/read/the-way-to-go_ZH_CN/eBook-06.6.md)的 fibonacci.go 函数）。

注意：

- return 或 return var 都是可以的。
- 不过 `return var = expression`（表达式） 会引发一个编译错误：`syntax error: unexpected =, expecting semicolon or newline or }`。

即使函数使用了命名返回值，你依旧可以无视它而返回明确的值。

任何一个非命名返回值（使用非命名返回值是很糟的编程习惯）在 `return` 语句里面都要明确指出包含返回值的变量或是一个可计算的值（就像上面警告所指出的那样）。

**尽量使用命名返回值：会使代码更清晰、更简短，同时更加容易读懂。**



### 6.2.3 空白符（blank identifier）



### 6.2.4 改变外部变量（outside variable）

传递指针给函数不但可以节省内存（因为没有复制变量的值），而且赋予了函数直接修改外部变量的能力，所以被修改的变量不再需要使用 `return` 返回。如下的例子，`reply` 是一个指向 `int` 变量的指针，通过这个指针，我们在函数内修改了这个 `int` 变量的数值。

```go
package main

import (
    "fmt"
)

// this function changes reply:
func Multiply(a, b int, reply *int) {
    *reply = a * b
}
func main() {
    n := 0
    reply := &n
    Multiply(10, 5, reply)
    fmt.Println("Multiply:", *reply) // Multiply: 50
}
```

这仅仅是个指导性的例子，当需要在函数内改变一个占用内存比较大的变量时，性能优势就更加明显了。然而，如果不小心使用的话，传递一个指针很容易引发一些不确定的事，所以，我们要十分小心那些可以改变外部变量的函数，在必要时，需要添加注释以便其他人能够更加清楚的知道函数里面到底发生了什么。



## 6.3 传递变长参数

如果函数的最后一个参数是采用 `...type` 的形式，那么这个函数就可以处理一个变长的参数，这个长度可以为 0，这样的函数称为变参函数。

```
func myFunc(a, b, arg ...int) {}
```

这个函数接受一个类似某个类型的 slice 的参数（详见第 7 章），该参数可以通过第 5.4.4 节中提到的 for 循环结构迭代。

示例函数和调用：

```go
func Greeting(prefix string, who ...string)
Greeting("hello:", "Joe", "Anna", "Eileen")
```

在 Greeting 函数中，变量 `who` 的值为 `[]string{"Joe", "Anna", "Eileen"}`。



如果参数被存储在一个 slice 类型的变量 `slice` 中，则可以通过 `slice...` 的形式来传递参数，调用变参函数。

```go
package main

import "fmt"

func main() {
    x := min(1, 3, 2, 0)
    fmt.Printf("The minimum is: %d\n", x)
    slice := []int{7, 9, 3, 5, 1}
    x = min(slice...)
    fmt.Printf("The minimum in the slice is: %d", x)
}
func min(s ...int) int {
    if len(s) == 0 {
        return 0
    }
    min := s[0]
    for _, v := range s {
        if v < min {
            min = v
        }
    }
    return min
}
```



一个接受变长参数的函数可以将这个参数作为其它函数的参数进行传递：

```go
func F1(s ...string) {
    F2(s...)
    F3(s)
}
func F2(s ...string) { }
func F3(s []string) { }
```



但是如果变长参数的类型并不是都相同的呢？使用 5 个参数来进行传递并不是很明智的选择，有 2 种方案可以解决这个问题：

1. 使用结构（详见第 10 章）：

    定义一个结构类型，假设它叫 `Options`，用以存储所有可能的参数：

    ```go
     type Options struct {
         par1 type1,
         par2 type2,
         ...
     }
    ```

    函数 F1 可以使用正常的参数 a 和 b，以及一个没有任何初始化的 Options 结构： `F1(a, b, Options {})`。如果需要对选项进行初始化，则可以使用 `F1(a, b, Options {par1:val1, par2:val2})`。

2. 使用空接口：

    如果一个变长参数的类型没有被指定，则可以使用默认的空接口 `interface{}`，这样就可以接受任何类型的参数（详见第 11.9 节）。该方案不仅可以用于长度未知的参数，还可以用于任何不确定类型的参数。一般而言我们会使用一个 for-range 循环以及 switch 结构对每个参数的类型进行判断：

    ```go
    func typecheck(values ...interface{}) {
        for _, value := range values {
            switch v := value.(type) {
            case int:
                print(v)
            case float64:
                print(v)
            case string:
                print(v)
            case bool:
                print(v)
            default:
                print(v)
            }
        }
    }
    ```

    

## 6.4 defer 和 追踪

关键字 defer 允许我们推迟到函数返回之前（或任意位置执行 `return` 语句之后）一刻才执行某个语句或函数（为什么要在返回之后才执行这些语句？因为 `return` 语句同样可以包含一些操作，而不是单纯地返回某个值）。

关键字 defer 的用法类似于面向对象编程语言 Java 和 C# 的 `finally` 语句块，它一般用于释放某些已分配的资源。



当有多个 defer 行为被注册时，它们会以逆序执行（类似栈，即后进先出）：

```go
package main

import "fmt"

func main() {
    f()
}

func f() {
    for i := 0; i < 5; i++ {
        defer fmt.Printf("%d ", i)
    }
}
```



关键字 defer 允许我们进行一些函数执行完成后的收尾工作，例如：

1. 关闭文件流 （详见 [第 12.2 节](https://www.bookstack.cn/read/the-way-to-go_ZH_CN/eBook-12.2.md)）

```go
// open a file  
defer file.Close()
```

2. 解锁一个加锁的资源 （详见 [第 9.3 节](https://www.bookstack.cn/read/the-way-to-go_ZH_CN/eBook-09.3.md)）

```go
mu.Lock()  
defer mu.Unlock()
```

3. 打印最终报告

```go
printHeader()  
defer printFooter()
```

4. 关闭数据库链接

```go
// open a database connection  
defer disconnectFromDB()
```

合理使用 defer 语句能够使得代码更加简洁。



**使用 defer 语句实现代码追踪**

简洁版：

```go
package main

import "fmt"

func main() {
    b()
}

func trace(s string) string {
    fmt.Println("entering:", s)
    return s
}

func un(s string) {
    fmt.Println("leaving:", s)
}

func a() {
    defer un(trace("a"))
    fmt.Println("in a")
}

func b() {
    defer un(trace("b"))
    fmt.Println("in b")
    a()
}
```



**使用 defer 语句来记录函数的参数与返回值**

下面的代码展示了另一种在调试时使用 defer 语句的手法：

```go
package main

import (
    "io"
    "log"
)

func func1(s string) (n int, err error) {
    defer func() {
        log.Printf("func1(%q) = %d, %v", s, n, err)
    }()
    return 7, io.EOF
}

func main() {
    func1("Go")
}
// 输出结果：2021/07/05 20:20:57 func1("Go") = 7, EOF
```



## 6.5 内置函数

Go 语言拥有一些不需要进行导入操作就可以使用的内置函数。它们有时可以针对不同的类型进行操作，例如：len、cap 和 append，或必须用于系统级的操作，例如：panic。因此，它们需要直接获得编译器的支持。

- close
- len、cap
- new、make
- copy、append
- panic、recover
- print、println
- complex、real imag



## 6.6 递归函数

特别注意的是，使用 int 类型最多只能计算到 12 的阶乘，因为一般情况下 int 类型的大小为 32 位，继续计算会导致溢出错误。那么，如何才能解决这个问题呢？

最好的解决方案就是使用 big 包（详见第 9.4 节）。



## 6.7 将函数作为参数

函数可以作为其它函数的参数进行传递，然后在其它函数内调用执行，一般称之为***回调***。下面是一个将函数作为参数的简单例子：

```go
package main

import (
    "fmt"
)

func main() {
    callback(1, Add)
}

func Add(a, b int) {
    fmt.Printf("The sum of %d and %d is: %d\n", a, b, a+b)
}

func callback(y int, f func(int, int)) {
    f(y, 2) // this becomes Add(1, 2)
}
```



**练习 6.7**

包 `strings` 中的 `Map` 函数和 `strings.IndexFunc()` 一样都是非常好的使用例子。请学习它的源代码并基于该函数书写一个程序，要求将指定文本内的所有非 ASCII 字符替换成 `?` 或空格。您需要怎么做才能删除这些字符呢？





## 6.8 闭包

当我们不希望给函数起名字的时候，可以使用匿名函数，例如：`func(x, y int) int { return x + y }`。

这样的一个函数不能够独立存在（编译器会返回错误：`non-declaration statement outside function body`），但可以被赋值于某个变量，即保存函数的地址到变量中：`fplus := func(x, y int) int { return x + y }`，然后通过变量名对函数进行调用：`fplus(3,4)`。

当然，您也可以直接对匿名函数进行调用：`func(x, y int) int { return x + y } (3, 4)`。

匿名函数像所有函数一样可以接受或不接受参数。

请学习以下示例并思考：函数 `f` 返回时，变量 `ret` 的值是什么？

```go
package main

import "fmt"

func main() {
	fmt.Println(f())
}

func f() (ret int) {
	defer func() {
		ret++
	}()
	return 1
}
// 变量 ret 的值为 2，因为 ret++ 是在执行 return 1 语句后发生的。
// 这可用于在返回语句之后修改返回的 error 时使用。
```



## 6.9 应用闭包：将函数作为返回值

闭包实现计数器

```go
package main

import "fmt"

func main() {
	var f = Adder()
	fmt.Print(f(1), " - ")
	fmt.Print(f(20), " - ")
	fmt.Print(f(300))
}

func Adder() func(int) int {
	var x int
	return func(delta int) int {
		x += delta
		return x
	}
}
```

函数 Adder() 现在被赋值到变量 f 中（类型为 `func(int) int`）。

输出结果：`1 - 21 - 321`

三次调用函数 f 的过程中函数 Adder() 中变量 delta 的值分别为：1、20 和 300。

我们可以看到，在多次调用中，变量 x 的值是被保留的，即 `0 + 1 = 1`，然后 `1 + 20 = 21`，最后 `21 + 300 = 321`：闭包函数保存并积累其中的变量的值，不管外部函数退出与否，它都能够继续操作外部函数中的局部变量。

这些局部变量同样可以是参数，例如之前例子中的 `Adder(x int)`。



在闭包中使用到的变量可以是在闭包函数体内声明的，也可以是在外部函数声明的：

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	var g int
	go func(i int) {
		s := 0
		for j := 0; j <= i; j++ {
			s += j
		}
		g = s
	}(1000) // Passes argument 1000 to the function literal.

	time.Sleep(100 * time.Millisecond)
	fmt.Println(g)
}
```



**练习 6.9** 不使用递归但使用闭包改写第 6.6 节中的斐波那契数列程序。

```go
package main

import "fmt"

func main() {
	fib := fibonacci()
	for i := 0; i < 10; i++ {
		fmt.Println(fib())
	}
}

func fibonacci() func() int {
	a, b := 0, 1
	return func() int {
		a, b = b, a+b
		return a
	}
}
```



可以返回其它函数的函数和接受其它函数作为参数的函数均被称之为高阶函数，是函数式语言的特点。我们已经在第 6.7 中得知函数也是一种值，因此很显然 Go 语言具有一些函数式语言的特性。闭包在 Go 语言中非常常见，常用于 goroutine 和管道操作（详见第 14.8-14.9 节）。在第 11.14 节的程序中，我们将会看到 Go 语言中的函数在处理混合对象时的强大能力。



## 6.10 使用闭包调试

当您在分析和调试复杂的程序时，无数个函数在不同的代码文件中相互调用，如果这时候能够准确地知道哪个文件中的具体哪个函数正在执行，对于调试是十分有帮助的。您可以使用 `runtime` 或 `log` 包中的特殊函数来实现这样的功能。包 `runtime` 中的函数 `Caller()` 提供了相应的信息，因此可以在需要的时候实现一个 `where()` 闭包函数来打印函数执行的位置：

```go
where := func() {
    _, file, line, _ := runtime.Caller(1)
    log.Printf("%s:%d", file, line)
}
where()
// some code
where()
// some more code
where()
```

您也可以设置 `log` 包中的 flag 参数来实现：

```go
log.SetFlags(log.Llongfile)
log.Print("")
```

或使用一个更加简短版本的 `where` 函数：

```go
var where = log.Print
func func1() {
where()
... some code
where()
... some code
where()
}
```



## 6.11 计算函数执行时间

有时候，能够知道一个计算执行消耗的时间是非常有意义的，尤其是在对比和基准测试中。最简单的一个办法就是在计算开始之前设置一个起始时候，再由计算结束时的结束时间，最后取出它们的差值，就是这个计算所消耗的时间。想要实现这样的做法，可以使用 `time` 包中的 `Now()` 和 `Sub` 函数：

```go
package main

import (
	"fmt"
	"time"
)

func longCalculation() {
	time.Sleep(500 * time.Millisecond)
}

func main() {
	start := time.Now()
	longCalculation()
	end := time.Now()
	delta := end.Sub(start)
	fmt.Printf("longCalculation took this amount of time: %s\n", delta)
}
```

您可以查看示例 6.20 [fibonacci.go](https://www.bookstack.cn/read/the-way-to-go_ZH_CN/$eBook-examples-chapter_6-fibonacci.go) 作为实例学习。

如果您对一段代码进行了所谓的优化，请务必对它们之间的效率进行对比再做出最后的判断。在接下来的章节中，我们会学习如何进行有价值的优化操作。



## 6.12 通过内存缓存来提升性能

当在进行大量的计算时，提升性能最直接有效的一种方式就是避免重复计算。通过在内存中缓存和重复利用相同计算的结果，称之为内存缓存。最明显的例子就是生成斐波那契数列的程序（详见第 6.6 和 6.11 节）：

要计算数列中第 n 个数字，需要先得到之前两个数的值，但很明显绝大多数情况下前两个数的值都是已经计算过的。即每个更后面的数都是基于之前计算结果的重复计算。

而我们要做就是将第 n 个数的值存在数组中索引为 n 的位置（详见第 7 章），然后在数组中查找是否已经计算过，如果没有找到，则再进行计算。

```go
package main

import (
	"fmt"
	"time"
)

const LIM = 41

var fibs [LIM]uint64

func main() {
	var result uint64 = 0
	start := time.Now()
	for i := 0; i < LIM; i++ {
		result = fibonacci(i)
		fmt.Printf("fibonacci(%d) is: %d\n", i, result)
	}
	end := time.Now()
	delta := end.Sub(start)
	fmt.Printf("longCalculation took this amount of time: %s\n", delta)
}
func fibonacci(n int) (res uint64) {
	// memoization: check if fibonacci(n) is already known in array:
	if fibs[n] != 0 {
		res = fibs[n]
		return
	}
	if n <= 1 {
		res = 1
	} else {
		res = fibonacci(n-1) + fibonacci(n-2)
	}
	fibs[n] = res
	return
}
```

内存缓存的优势显而易见，而且您还可以将它应用到其它类型的计算中，例如使用 map（详见第 7 章）而不是数组或切片。

内存缓存的技术在使用计算成本相对昂贵的函数时非常有用（不仅限于例子中的递归），譬如大量进行相同参数的运算。这种技术还可以应用于纯函数中，即相同输入必定获得相同输出的函数。



---



# 第7章 数组与切片


这章我们开始剖析 **容器**, 它是可以包含大量条目（item）的数据结构, 例如数组、切片和 map。从这看到 Go 明显受到 Python 的影响。

以 `[]` 符号标识的数组类型几乎在所有的编程语言中都是一个基本主力。Go 语言中的数组也是类似的，只是有一些特点。Go 没有 C 那么灵活，但是拥有切片（slice）类型。这是一种建立在 Go 语言数组类型之上的抽象，要想理解切片我们必须先理解数组。数组有特定的用处，但是却有一些呆板，所以在 Go 语言的代码里并不是特别常见。相对的，切片确实随处可见的。它们构建在数组之上并且提供更强大的能力和便捷。



## 7.1 声明和初始化（数组）

### 7.1.1 概念

数组是具有相同 **唯一类型** 的一组已编号且长度固定的数据项序列（这是一种同构的数据结构）

数组长度必须是一个常量表达式，并且必须是一个非负整数。

数组长度也是数组类型的一部分，所以 `[5]int` 和 `[10]int` 是属于不同类型的。

**注意事项** 如果我们想让数组元素类型为任意类型的话可以使用空接口作为类型（参考 [第 11 章](https://www.bookstack.cn/read/the-way-to-go_ZH_CN/eBook-11.9.md)）。当使用值时我们必须先做一个类型判断（参考 [第 11 章](https://www.bookstack.cn/read/the-way-to-go_ZH_CN/eBook-11.3.md)）。

元素的数目，也称为长度或者数组大小必须是固定的并且在声明该数组时就给出（编译时需要知道数组长度以便分配内存）；数组长度最大为 2Gb。

数组声明的格式是：

```go
 var identifier [len]type
```

对索引项为 `i` 的数组元素赋值可以这么操作：`arr[i] = value`，所以数组是 **可变的**。

在声明 **数组** 时你可以忽略数组的长度并用 `...` 代替，让编译器为你自动推导数组的长度。

```go
var arrLazy = [...]int{5, 6, 7, 8, 22}
```



Go 语言中的数组是一种 **值类型**（不像 C/C++ 中是指向首元素的指针），所以可以通过 `new()` 来创建： `var arr1 = new([5]int)`。

那么这种方式和 `var arr2 [5]int` 的区别是什么呢？arr1 的类型是 `*[5]int`，而 arr2的类型是 `[5]int`。

这样的结果就是当把一个数组赋值给另一个时，需要再做一次数组内存的拷贝操作。例如：

```go
arr2 := *arr1
arr2[2] = 100
```

这样两个数组就有了不同的值，在赋值后修改 arr2 不会对 arr1 生效。

所以在函数中数组作为参数传入时，如 `func1(arr2)`，会产生一次数组拷贝，func1 方法不会修改原始的数组 arr2。

如果你想修改原数组，那么 arr2 必须通过&操作符以引用方式传过来，例如 func1(&arr2），下面是一个例子：

```go
package main
import "fmt"
func f(a [3]int) { fmt.Println(a) }
func fp(a *[3]int) { fmt.Println(a) }
func main() {
    var ar [3]int
    f(ar)     // passes a copy of ar
    fp(&ar) // passes a pointer to ar
}
// 输出结果：
// [0 0 0]
// &[0 0 0]
```



### 7.1.2 数组常量

如果数组值已经提前知道了，那么可以通过 **数组常量** 的方法来初始化数组，而不用依次使用 `[]=` 方法（所有的组成元素都有相同的常量语法）。

```go
package main
import "fmt"

func main() {
    // var arrAge = [5]int{18, 20, 15, 22, 16}
    // var arrLazy = [...]int{5, 6, 7, 8, 22}
    // var arrLazy = []int{5, 6, 7, 8, 22}    //注：初始化得到的实际上是切片slice
    var arrKeyValue = [5]string{3: "Chris", 4: "Ron"}
    // var arrKeyValue = []string{3: "Chris", 4: "Ron"}    //注：初始化得到的实际上是切片slice
    
    for i:=0; i < len(arrKeyValue); i++ {
        fmt.Printf("Person at %d is %s\n", i, arrKeyValue[i])
    }
}
```



### 7.1.3 多维数组

数组通常是一维的，但是可以用来组装成多维数组，例如：`[3][5]int`，`[2][2][2]float64`。

内部数组总是长度相同的。Go 语言的多维数组是矩形式的（**唯一的例外** 是切片的数组，参见第 7.2.5 节）。

```go
package main

import "fmt"

const (
	WIDTH  = 10
	HEIGHT = 5
)

type pixel int

var screen [HEIGHT][WIDTH]pixel

func main() {
	for x := 0; x < HEIGHT; x++ {
		i := pixel(0)
		for y := 0; y < WIDTH; y, i = y+1, i+pixel(1) {
			screen[x][y] = i
		}
	}
	fmt.Printf("%v", screen)
}
```



### 7.1.4 将数组传递给函数

把一个大数组传递给函数会消耗很多内存。有两种方法可以避免这种现象：

- 传递数组的指针
- 使用数组的切片

接下来的例子阐明了第一种方法：

```go
package main

import "fmt"

func main() {
	array := [3]float64{7.0, 8.5, 9.1}
	x := Sum(&array) // Note the explicit address-of operator
	// to pass a pointer to the array
	fmt.Printf("The sum of the array is: %f", x)
}

func Sum(a *[3]float64) (sum float64) {
	for _, v := range a { // dereferencing *a to get back to the array is not necessary!
		sum += v
	}
	return
}
```

但这在 Go 中并不常用，通常使用切片（参考 [第 7.2 节](https://www.bookstack.cn/read/the-way-to-go_ZH_CN/eBook-07.2.md)）。



## 7.2 切片

### 7.2.1 概念

- 切片（slice）是对数组一个连续片段的引用（该数组我们称之为相关数组，通常是匿名的），所以切片是一个 **引用类型**（因此更类似于 C/C++ 中的数组类型，或者 Python 中的 list 类型）。这个片段可以是整个数组，或者是由起始和终止索引标识的一些项的子集。需要注意的是，终止索引标识的项不包括在切片内。切片提供了一个相关数组的动态窗口。

- 给定项的切片索引可能比相关数组的相同元素的索引小。和数组不同的是，切片的长度可以在运行时修改，最小为 0 最大为相关数组的长度：切片是一个 **长度可变的数组**。

- 切片提供了计算容量的函数 `cap()` 可以测量切片最长可以达到多少：切片的容量是从它的第一个元素到其底层数组的末尾元素的个数。如果 s 是一个切片，`cap(s)` 就是从 `s[0]` 到其底层数组末尾元素的数组长度。切片的长度永远不会超过它的容量，所以对于 切片 `s` 来说该不等式永远成立：`0 <= len(s) <= cap(s)`。

- 多个切片如果表示同一个数组的片段，它们可以共享数据；因此一个切片和相关数组的其他切片是共享存储的，相反，不同的数组总是代表不同的存储。数组实际上是切片的构建块。

- **优点** 因为切片是引用，所以它们不需要使用额外的内存并且比使用数组更有效率，所以在 Go 代码中 切片比数组更常用。



- 声明切片的格式是： `var identifier []type`（不需要说明长度）。

- 一个切片在未初始化之前默认为 nil，长度为 0。

- 切片的初始化格式是：`var slice1 []type = arr1[start:end]`。

- 这表示 slice1 是由数组 arr1 从 start 索引到 `end-1` 索引之间的元素构成的子集（切分数组，start:end 被称为 slice 表达式）。所以 `slice1[0]` 就等于 `arr1[start]`。这可以在 arr1 被填充前就定义好。

- 如果某个人写：`var slice1 []type = arr1[:]` 那么 slice1 就等于完整的 arr1 数组（所以这种表示方式是 `arr1[0:len(arr1)]` 的一种缩写）。另外一种表述方式是：`slice1 = &arr1`。

- 一个由数字 1、2、3 组成的切片可以这么生成：`s := [3]int{1,2,3}[:]` (注: 应先用`s := [3]int{1, 2, 3}` 生成数组, 再使用 `s[:]` 转成切片) 甚至更简单的 `s := []int{1,2,3}`。

- `s2 := s[:]` 是用切片组成的切片，拥有相同的元素，但是仍然指向相同的相关数组。

- 一个切片 s 可以这样扩展到它的大小上限：`s = s[:cap(s)]`，如果再扩大的话就会导致运行时错误（参见第 7.7 节）。

- 对于每一个切片（包括 string），以下状态总是成立的：

    ```go
    s == s[:i] + s[i:] // i是一个整数且: 0 <= i <= len(s)
    len(s) <= cap(s)
    ```

- 切片也可以用类似数组的方式初始化：`var x = []int{2, 3, 5, 7, 11}`。这样就创建了一个长度为 5 的数组并且创建了一个相关切片。

- 切片在内存中的组织方式实际上是 ***一个有 3 个域的结构体***：**指向相关数组的指针**，**切片长度** 以及 **切片容量**。下图给出了一个长度为 2，容量为 4 的切片 y。

    - `y[0] = 3` 且 `y[1] = 5`。
    - 切片 `y[0:4]` 由 元素 3，5，7 和 11 组成。

    ![7.2_fig7.2](./images/7.2_fig7.2.png)

    

- 如果 s2 是一个 slice，你可以将 s2 向后移动一位 `s2 = s2[1:]`，但是末尾没有移动。切片只能向后移动，`s2 = s2[-1:]` 会导致编译错误。切片 **不能 **被重新分片以获取数组的前一个元素。

- **注意** 绝对不要用指针指向 slice。切片本身已经是一个引用类型，所以它本身就是一个指针！！！



### 7.2.2 将切片传递给函数

如果你有一个函数需要对数组做操作，你可能总是需要把参数声明为切片。当你调用该函数时，把数组分片，创建为一个 切片引用并传递给该函数。这里有一个计算数组元素和的方法:

```go
 package main

import "fmt"

func main() {
	var arr = [5]int{0, 1, 2, 3, 4}
	res := sum(arr[:])
	fmt.Printf("res = %v", res)
}

func sum(a []int) int {
	s := 0
	for i := 0; i < len(a); i++ {
		s += a[i]
	}
	return s
}
```



### 7.2.3 用 make() 创建一个切片

- 当相关数组还没有定义时，我们可以使用 make() 函数来创建一个切片 同时创建好相关数组：

    ```go
    var slice1 []type = make([]type, len)
    
    // 也可以简写为 
    slice1 := make([]type, len)  // 这里 len 是数组的长度并且也是 slice 的初始长度。
    ```

- 所以定义 `s2 := make([]int, 10)`，那么 `cap(s2) == len(s2) == 10`。

- make 接受 2 个参数：元素的类型以及切片的元素个数。

- 如果你想创建一个 slice1，它不占用整个数组，而只是占用以 len 为个数个项，那么只要：`slice1 := make([]type, len, cap)`。

- make 的使用方式是：`func make([]T, len, cap)`，其中 cap 是可选参数。

- 所以下面两种方法可以生成相同的切片:

    ```go
    make([]int, 50, 100)
    new([100]int)[0:50]
    ```

- 下图描述了使用 make 方法生成的切片的内存结构：

    ![7.2_fig7.2.1_make生成切片](./images/7.2_fig7.2.1_make生成切片.png)

- 因为字符串是纯粹不可变的字节数组，它们也可以被切分成 切片。



### 7.2.4 new() 和 make() 的区别

看起来二者没有什么区别，都在堆上分配内存，但是它们的行为不同，适用于不同的类型。

- **new(T)** 为每个新的类型 **T** 分配一片内存，初始化为 0 并且返回类型为 ***T**  的内存地址：这种方法 **返回一个指向类型为 T，值为 0 的地址的指针**，它适用于值类型如数组和结构体（参见第 10 章）；它相当于 `&T{}`。
- **make(T)** **返回一个类型为 T 的初始值**，它只适用于3种内建的引用类型：**切片**、**map** 和 **channel**（参见第 8 章，第 13 章）。

换言之，**new** 函数分配内存，**make** 函数初始化；下图给出了区别：

![7.2_fig7.3](./images/7.2_fig7.3.png)

在图 7.3 的第一幅图中：

```go
var p *[]int = new([]int) // *p == nil; with len and cap 0
p := new([]int)
```

在第二幅图中， `p := make([]int, 0)` ，切片 已经被初始化，但是指向一个空的数组。

以上两种方式实用性都不高。下面的方法：

```go
var v []int = make([]int, 10, 50)
```

或者

```go
v := make([]int, 10, 50)
```

这样分配一个有 50 个 int 值的数组，并且创建了一个长度为 10，容量为 50 的 切片 v，该 切片 指向数组的前 10 个元素。



*译者注：如何理解new、make、slice、map、channel的关系*

*1.slice、map以及channel都是golang内建的一种引用类型，三者在内存中存在多个组成部分， 需要对内存组成部分初始化后才能使用，而make就是对三者进行初始化的一种操作方式*

*2. new 获取的是存储指定变量内存地址的一个变量，对于变量内部结构并不会执行相应的初始化操作， 所以slice、map、channel需要make进行初始化并获取对应的内存地址，而非new简单的获取内存地址*



### 7.2.5 多维 切片

和数组一样，切片通常也是一维的，但是也可以由一维组合成高维。通过分片的分片（或者切片的数组），**长度可以任意动态变化**，所以 Go 语言的多维切片可以任意切分。而且，内层的切片必须单独分配（通过 make 函数）。



### 7.2.6 bytes 包

类型 `[]byte` 的切片十分常见，Go 语言有一个 bytes 包专门用来解决这种类型的操作方法。

bytes 包和字符串包十分类似（参见第 4.7 节）。而且它还包含一个十分有用的类型 Buffer:

```go
import "bytes"
type Buffer struct {
    ...
}
```

这是一个长度可变的 bytes 的 buffer，提供 Read 和 Write 方法，因为读写长度未知的 bytes 最好使用 buffer。

Buffer 可以这样定义：`var buffer bytes.Buffer`。

或者使用 new 获得一个指针：`var r *bytes.Buffer = new(bytes.Buffer)`。

或者通过函数：`func NewBuffer(buf []byte) *Buffer`，创建一个 Buffer 对象并且用 buf 初始化好；NewBuffer 最好用在从 buf 读取的时候使用。



**通过 buffer 串联字符串**

类似于 Java 的 StringBuilder 类。

在下面的代码段中，我们创建一个 buffer，通过 `buffer.WriteString(s)` 方法将字符串 s 追加到后面，最后再通过 `buffer.String()` 方法转换为 string：

```go
var buffer bytes.Buffer
for {
    if s, ok := getNextString(); ok { //method getNextString() not shown here
        buffer.WriteString(s)
    } else {
        break
    }
}
fmt.Print(buffer.String(), "\n")
```

这种实现方式比使用 `+=` 要更节省内存和 CPU，尤其是要串联的字符串数目特别多的时候。



## 7.3 For-range 结构

这种构建方法可以应用于数组和切片:

```go
for ix, value := range slice1 {
	...
}
```

第一个返回值 ix 是数组或者切片的索引，第二个是在该索引位置的值；他们都是仅在 for 循环内部可见的局部变量。value 只是 slice1 某个索引位置的值的一个拷贝，不能用来修改 slice1 该索引位置的值。

如果你只需要索引，你可以忽略第二个变量，例如：

```go
package main

import "fmt"

func main() {
	seasons := []string{"Spring", "Summer", "Autumn", "Winter"}
	for i := range seasons {
		fmt.Printf("%v", i)
	}
}
// Output: 0 1 2 3
```



## 7.4 切片重组（reslice）

我们已经知道切片创建的时候通常比相关数组小，例如：

```go
slice1 := make([]type, start_length, capacity)
```

其中 `start_length` 作为切片初始长度而 `capacity` 作为相关数组的长度。

这么做的好处是我们的切片在达到容量上限后可以扩容。改变切片长度的过程称之为切片重组 **reslicing**，做法如下：`slice1 = slice1[0:end]`，其中 end 是新的末尾索引（即长度）。

将切片扩展 1 位可以这么做：

```go
sl = sl[0:len(sl)+1]
```

切片可以反复扩展直到占据整个相关数组。

```go
package main

import "fmt"

func main() {
	var ar = [10]int{0, 1, 2, 3, 4, 5, 6, 7, 8, 9}
	var a = ar[5:7] // reference to subarray {5,6} - len(a) is 2 and cap(a) is 5
	fmt.Printf("len = %d, cap = %d\n", len(a), cap(a))
	
	a = a[0:4] // ref of subarray {5,6,7,8} - len(a) is now 4 but cap(a) is still 5
	fmt.Printf("len = %d, cap = %d\n", len(a), cap(a))

	a = a[1:1]
	fmt.Printf("len = %d, cap = %d\n", len(a), cap(a))
}
// 输出结果：
// len = 2, cap = 5
// len = 4, cap = 5
// len = 0, cap = 4
```



## 7.5 切片的复制与追加

如果想增加切片的容量，我们必须创建一个新的更大的切片并把原分片的内容都拷贝过来。下面的代码描述了从拷贝切片的 copy 函数和向切片追加新元素的 append 函数。

```go
package main
import "fmt"
func main() {
    slFrom := []int{1, 2, 3}
    slTo := make([]int, 10)
    n := copy(slTo, slFrom)
    fmt.Println(slTo)
    fmt.Printf("Copied %d elements\n", n) // n == 3
    sl3 := []int{1, 2, 3}
    sl3 = append(sl3, 4, 5, 6)
    fmt.Println(sl3)
}
```

`func append(s[]T, x ...T) []T` 其中 append 方法将 **0 个或多个** 具有相同类型 s 的元素追加到切片后面并且返回新的切片；追加的元素必须和原切片的元素同类型。如果 s 的容量不足以存储新增元素，append 会分配新的切片来保证已有切片元素和新增元素的存储。因此，返回的切片可能已经指向一个不同的相关数组了。append 方法总是返回成功，除非系统内存耗尽了。

如果你想将切片 y 追加到切片 x 后面，只要将第二个参数扩展成一个列表即可：`x = append(x, y...)`。

**注意**： append 在大多数情况下很好用，但是如果你想完全掌控整个追加过程，你可以实现一个这样的 AppendByte 方法：

```go
func AppendByte(slice []byte, data ...byte) []byte {
    m := len(slice)
    n := m + len(data)
    if n > cap(slice) { // if necessary, reallocate
        // allocate double what's needed, for future growth.
        newSlice := make([]byte, (n+1)*2)
        copy(newSlice, slice)
        slice = newSlice
    }
    slice = slice[0:n]
    copy(slice[m:n], data)
    return slice
}
```

`func copy(dst, src []T) int` copy 方法将类型为 T 的切片从源地址 src 拷贝到目标地址 dst，覆盖 dst 的相关元素，并且返回拷贝的元素个数。源地址和目标地址可能会有重叠。拷贝个数是 src 和 dst 的长度最小值。如果 src 是字符串那么元素类型就是 byte。如果你还想继续使用 src，在拷贝结束后执行 `src = dst`。



## 7.6 字符串、数组和切片的应用

### 7.6.1 从字符串生成字节切片

假设 s 是一个字符串（本质上是一个字节数组），那么就可以直接通过 `c := []byte(s)` 来获取一个字节的切片 c。另外，您还可以通过 copy 函数来达到相同的目的：`copy(dst []byte, src string)`。



同样的，还可以使用 for-range 来获得每个元素：

```go
package main
import "fmt"
func main() {
    s := "\u00ff\u754c"
    for i, c := range s {
        fmt.Printf("%d:%c ", i, c)
    }
}
// 输出
// 0:ÿ 2:界
```

我们知道，Unicode 字符会占用 2 个字节，有些甚至需要 3 个或者 4 个字节来进行表示。如果发现错误的 UTF8 字符，则该字符会被设置为 U+FFFD 并且索引向前移动一个字节。和字符串转换一样，您同样可以使用 `c := []int32(s)` 语法，这样切片中的每个 int 都会包含对应的 Unicode 代码，因为字符串中的每个字符都会对应一个整数。类似的，您也可以将字符串转换为元素类型为 rune 的切片：`r := []rune(s)`。

可以通过代码 `len([]int32(s))` 来获得字符串中字符的数量，但使用 `utf8.RuneCountInString(s)` 效率会更高一点。



### 7.6.2 获取字符串的某一部分

使用 `substr := str[start:end]` 可以从字符串 str 获取到从索引 start 开始到 `end-1` 位置的子字符串。同样的，`str[start:]` 则表示获取从 start 开始到 `len(str)-1` 位置的子字符串。而 `str[:end]` 表示获取从 0 开始到 `end-1` 的子字符串。



## 7.6.3 字符串和切片的内存结构

在内存中，一个字符串实际上是一个 **双字结构**，即 **一个指向实际数据的指针** 和 **另一个记录字符串长度的整数**（见图 7.4）。因为指针对用户来说是完全不可见，因此我们可以依旧把字符串看做是一个值类型，也就是一个 **字符数组**。

字符串 `string s = "hello"` 和子字符串 `t = s[2:3]` 在内存中的结构可以用下图表示：

![7.6_fig7.4-字符串内存结构](./images/7.6_fig7.4-字符串内存结构.png)



### 7.6.4 修改字符串中的某个字符

Go 语言中的字符串是不可变的，也就是说 `str[index]` 这样的表达式是不可以被放在等号左侧的。如果尝试运行 `str[i] = 'D'` 会得到错误：`cannot assign to str[i]`。

因此，您必须先将字符串转换成字节数组，然后再通过修改数组中的元素值来达到修改字符串的目的，最后将字节数组转换回字符串格式。

例如，将字符串 “hello” 转换为 “cello”：

```go
func main() {
	s := "hello"
	c := []byte(s)
	c[0] = 'c'
	s2 := string(c) // s2 == "cello"
	fmt.Printf("s2=%v\n", s2)
}
```

所以，您可以通过操作切片来完成对字符串的操作。



### 7.6.5 字节数组对比函数

下面的 `Compare` 函数会返回两个字节数组字典顺序的整数对比结果，

即 `0 if a == b, -1 if a < b, 1 if a > b`。

```go
package main

import "fmt"

func main() {
	a := []byte("你好，中国")
	b := []byte("你好，中国")
	fmt.Printf("Compare(a, b)=%v", Compare(a, b))
}

func Compare(a, b []byte) int {
	for i := 0; i < len(a) && i < len(b); i++ {
		switch {
		case a[i] > b[i]:
			return 1
		case a[i] < b[i]:
			return -1
		}
	}
	// 数组的长度可能不同
	switch {
	case len(a) < len(b):
		return -1
	case len(a) > len(b):
		return 1
	}
	return 0 // 数组相等
}
```



### 7.6.6 搜索及排序切片和数组

标准库提供了 `sort` **包** 来实现常见的搜索和排序操作。您可以使用 `sort` 包中的函数 `func Ints(a []int)` 来实现对 int 类型的切片排序。例如 `sort.Ints(arri)`，其中变量 arri 就是需要被升序排序的数组或切片。为了检查某个数组是否已经被排序，可以通过函数 `IntsAreSorted(a []int) bool` 来检查，如果返回 true 则表示已经被排序。

类似的，可以使用函数 `func Float64s(a []float64)` 来排序 float64 的元素，或使用函数 `func Strings(a []string)` 排序字符串元素。

想要在数组或切片中搜索一个元素，该数组或切片必须先被排序（因为标准库的搜索算法使用的是 **二分法**）。然后，您就可以使用函数 `func SearchInts(a []int, n int) int` 进行搜索，并返回对应结果的索引值。

当然，还可以搜索 float64 和字符串：

```go
func SearchFloat64s(a []float64, x float64) int
func SearchStrings(a []string, x string) int
```

您可以通过查看 [官方文档](http://golang.org/pkg/sort/) 来获取更详细的信息。

这就是如何使用 `sort` 包的方法，我们会在第 11.6 节对它的细节进行深入，并实现一个属于我们自己的版本。



### 7.6.7 append 函数常见操作

我们在第 7.5 节提到的 append 非常有用，它能够用于各种方面的操作：

1. 将切片 b 的元素追加到切片 a 之后：

    ```go
    a = append(a, b...)
    ```

2. 复制切片 a 的元素到新的切片 b 上：

    ```go
     b = make([]T, len(a))
     copy(b, a)
    ```

3. 删除位于索引 i 的元素：

    ```go
    a = append(a[:i], a[i+1:]...)
    ```

4. 切除切片 a 中从索引 i 至 j 位置的元素：

    ```go
    a = append(a[:i], a[j:]...)
    ```

5. 为切片 a 扩展 j 个元素长度：

    ```go
    a = append(a, make([]T, j)...)
    ```

6. 在索引 i 的位置插入元素 x：

    ```go
    a = append(a[:i], append([]T{x}, a[i:]...)...)
    ```

7. 在索引 i 的位置插入长度为 j 的新切片：

    ```go
    a = append(a[:i], append(make([]T, j), a[i:]...)...)
    ```

8. 在索引 i 的位置插入切片 b 的所有元素：

    ```go
    a = append(a[:i], append(b, a[i:]...)...)
    ```

9. 取出位于切片 a 最末尾的元素 x：

    ```go
    x, a = a[len(a)-1], a[:len(a)-1]
    ```

10. 将元素 x 追加到切片 a：

    ```go
    a = append(a, x)
    ```



因此，您可以使用切片和 append 操作来表示任意可变长度的序列。

从数学的角度来看，切片相当于向量，如果需要的话可以定义一个向量作为切片的别名来进行操作。

如果您需要更加完整的方案，可以学习一下 Eleanor McHugh 编写的几个包：[slices](http://github.com/feyeleanor/slices)、[chain](http://github.com/feyeleanor/chain) 和 [lists](http://github.com/feyeleanor/lists)。



### 7.6.8 切片和垃圾回收

切片的底层指向一个数组，该数组的实际容量可能要大于切片所定义的容量。**只有在没有任何切片指向的时候**，底层的数组内存才会被释放，这种特性有时会导致程序占用多余的内存。

**示例** 函数 `FindDigits` 将一个文件加载到内存，然后搜索其中所有的数字并返回一个切片。

```go
var digitRegexp = regexp.MustCompile("[0-9]+")
func FindDigits(filename string) []byte {
    b, _ := ioutil.ReadFile(filename)
    return digitRegexp.Find(b)
}
```

这段代码可以顺利运行，但返回的 `[]byte` 指向的底层是整个文件的数据。只要该返回的切片不被释放，垃圾回收器就不能释放整个文件所占用的内存。换句话说，一点点有用的数据却占用了整个文件的内存。

想要避免这个问题，可以通过拷贝我们需要的部分到一个新的切片中：

```go
func FindDigits(filename string) []byte {
   b, _ := ioutil.ReadFile(filename)
   b = digitRegexp.Find(b)
   c := make([]byte, len(b))
   copy(c, b)
   return c
}
```

事实上，上面这段代码只能找到第一个匹配正则表达式的数字串。要想找到所有的数字，可以尝试下面这段代码：

```go
func FindFileDigits(filename string) []byte {
   fileBytes, _ := ioutil.ReadFile(filename)
   b := digitRegexp.FindAll(fileBytes, len(fileBytes))
   c := make([]byte, 0)
   for _, bytes := range b {
      c = append(c, bytes...)
   }
   return c
}
```



---



# 第8章 Map

map 是一种特殊的数据结构：一种元素对（pair）的无序集合，pair 的一个元素是 key，对应的另一个元素是 value，所以这个结构也称为关联数组或字典。这是一种快速寻找值的理想结构：给定 key，对应的 value 可以迅速定位。

map 这种数据结构在其他编程语言中也称为字典（Python）、hash 和 HashTable 等。



## 8.1 声明、初始化和 make

### 8.1.1 概念

map 是引用类型，可以使用如下声明：

```go
var map1 map[keytype]valuetype
var map1 map[string]int
```

（`[keytype]` 和 `valuetype` 之间允许有空格，但是 gofmt 移除了空格）

在声明的时候不需要知道 map 的长度，map 是可以动态增长的。

未初始化的 map 的值是 nil。

key 可以是任意可以用 == 或者 != 操作符比较的类型，比如 string、int、float。所以数组、切片和结构体不能作为 key (译者注：含有数组切片的结构体不能作为 key，只包含内建类型的 struct 是可以作为 key 的），但是指针和接口类型可以。如果要用结构体作为 key 可以提供 `Key()` 和 `Hash()` 方法，这样可以通过结构体的域计算出唯一的数字或者字符串的 key。

value 可以是任意类型的；通过使用空接口类型（详见第 11.9 节），我们可以存储任意值，但是使用这种类型作为值时需要先做一次类型断言（详见第 11.3 节）。

map 传递给函数的代价很小：在 32 位机器上占 4 个字节，64 位机器上占 8 个字节，无论实际上存储了多少数据。通过 key 在 map 中寻找值是很快的，比线性查找快得多，但是仍然比从数组和切片的索引中直接读取要慢 100 倍；所以如果你很在乎性能的话还是建议用切片来解决问题。

map 也可以用函数作为自己的值，这样就可以用来做分支结构（详见第 5 章）：key 用来选择要执行的函数。

如果 key1 是 map1 的key，那么 `map1[key1]` 就是对应 key1 的值，就如同数组索引符号一样（数组可以视为一种简单形式的 map，key 是从 0 开始的整数）。

key1 对应的值可以通过赋值符号来设置为 val1：`map1[key1] = val1`。

令 `v := map1[key1]` 可以将 key1 对应的值赋值给 v；如果 map 中没有 key1 存在，那么 v 将被赋值为 map1 的值类型的空值。

常用的 `len(map1)` 方法可以获得 map 中的 pair 数目，这个数目是可以伸缩的，因为 map-pairs 在运行时可以动态添加和删除。



示例:

```go
package main

import "fmt"

func main() {
	var mapLit map[string]int
	// var mapCreated map[string]float32
	var mapAssigned map[string]int
	mapLit = map[string]int{"one": 1, "two": 2}
	mapCreated := make(map[string]float32)
	mapAssigned = mapLit
	mapCreated["key1"] = 4.5
	mapCreated["key2"] = 3.14159
	mapAssigned["two"] = 3
	fmt.Printf("Map literal at \"one\" is: %d\n", mapLit["one"])
	fmt.Printf("Map created at \"key2\" is: %f\n", mapCreated["key2"])
	fmt.Printf("Map assigned at \"two\" is: %d\n", mapLit["two"])
	fmt.Printf("Map literal at \"ten\" is: %d\n", mapLit["ten"])
}
```

mapLit 说明了 `map literals` 的使用方法： map 可以用 `{key1: val1, key2: val2}` 的描述方法来初始化，就像数组和结构体一样。

map 是 **引用类型** 的： 内存用 make 方法来分配。

map 的初始化：`var map1 = make(map[keytype]valuetype)`。

或者简写为：`map1 := make(map[keytype]valuetype)`。

上面例子中的 mapCreated 就是用这种方式创建的：`mapCreated := make(map[string]float32)`。

相当于：`mapCreated := map[string]float32{}`。

mapAssigned 也是 mapLit 的引用，对 mapAssigned 的修改也会影响到 mapLit 的值。

**不要使用 new，永远用 make 来构造 map**

**注意** 如果你错误的使用 new() 分配了一个引用对象，你会获得一个空引用的指针，相当于声明了一个未初始化的变量并且取了它的地址：

```go
mapCreated1 := new(map[string]float32)
mapCreated1["asd"] = 1.1 // invalid operation: mapCreated1["asd"] (type *map[string]float32 does not support indexing)
```



### 8.1.2 map 容量

和数组不同，map 可以根据新增的 key-value 对动态的伸缩，因此它不存在固定长度或者最大限制。但是你也可以选择标明 map 的初始容量 `capacity`，就像这样：`make(map[keytype]valuetype, cap)`。例如：

```go
map2 := make(map[string]float32,100)
```

当 map 增长到容量上限的时候，如果再增加新的 key-value 对，map 的大小会自动加 1。所以出于性能的考虑，对于大的 map 或者会快速扩张的 map，即使只是大概知道容量，也最好先标明。



### 8.1.3 用切片作为 map 的值

既然一个 key 只能对应一个 value，而 value 又是一个原始类型，那么如果一个 key 要对应多个值怎么办？例如，当我们要处理unix机器上的所有进程，以父进程（pid 为整型）作为 key，所有的子进程（以所有子进程的 pid 组成的切片）作为 value。通过将 value 定义为 `[]int` 类型或者其他类型的切片，就可以优雅的解决这个问题。

这里有一些定义这种 map 的例子：

```go
mp1 := make(map[int][]int)
mp2 := make(map[int]*[]int)
```



## 8.2 测试键值对是否存在及删除元素

- 测试键值对是否存在，我们可以这么用：

    ```go
    val1, isPresent := map1[key1]
    ```

    - isPresent 返回一个 bool 值：如果 key1 存在于 map1，val1 就是 key1 对应的 value 值，并且 isPresent为true；如果 key1 不存在，val1 就是一个空值，并且 isPresent 会返回 false。

- 如果你只是想判断某个 key 是否存在而不关心它对应的值到底是多少，你可以这么做：

    ```go
    _, ok := map1[key1] // 如果key1存在则ok == true，否则ok为false
    ```

- 或者和 if 混合使用：

    ```go
    if _, ok := map1[key1]; ok {
        // ...
    }
    ```



- 从 map1 中删除 key1：

    直接 `delete(map1, key1)` 就可以。

    如果 key1 不存在，该操作不会产生错误。



## 8.3 for-range 的配套用法

可以使用 for 循环构造 map：

```go
for key, value := range map1 {
    ...
}
```

第一个返回值 key 是 map 中的 key 值，第二个返回值则是该 key 对应的 value 值；这两个都是仅 for 循环内部可见的局部变量。其中第一个返回值key值是一个可选元素。如果你只关心值，可以这么使用：

```go
for _, value := range map1 {
    ...
}
```

如果只想获取 key，你可以这么使用：

```go
for key := range map1 {
    fmt.Printf("key is: %d\n", key)
}
```

- 注意 map 不是按照 key 的顺序排列的，也不是按照 value 的序排列的。



## 8.4 map 类型的切片

假设我们想获取一个 map 类型的切片，我们必须使用两次 `make()` 函数，第一次分配切片，第二次分配 切片中每个 map 元素。

```go
package main
import "fmt"
func main() {
    // Version A:
    items := make([]map[int]int, 5)
    for i:= range items {
        items[i] = make(map[int]int, 1)
        items[i][1] = 2
    }
    fmt.Printf("Version A: Value of items: %v\n", items)
    // Version B: NOT GOOD!
    items2 := make([]map[int]int, 5)
    for _, item := range items2 {
        item = make(map[int]int, 1) // item is only a copy of the slice element.
        item[1] = 2 // This 'item' will be lost on the next iteration.
    }
    fmt.Printf("Version B: Value of items: %v\n", items2)
}

// 输出结果
// Version A: Value of items: [map[1:2] map[1:2] map[1:2] map[1:2] map[1:2]]
// Version B: Value of items: [map[] map[] map[] map[] map[]]
```

需要注意的是，应当像 A 版本那样通过索引使用切片的 map 元素。在 B 版本中获得的项只是 map 值的一个拷贝而已，所以真正的 map 元素没有得到初始化。



## 8.5 map 的排序

map 默认是无序的，不管是按照 key 还是按照 value 默认都不排序（详见第 8.3 节）。

如果你想为 map 排序，需要将 key（或者 value）拷贝到一个切片，再对切片排序（使用 sort 包，详见第 7.6.6 节），然后可以使用切片的 for-range 方法打印出所有的 key 和 value。

下面有一个示例：

```go
// the telephone alphabet:
package main

import (
	"fmt"
	"sort"
)

var (
	barVal = map[string]int{
		"alpha": 34, "bravo": 56, "charlie": 23,
		"delta": 87, "echo": 56, "foxtrot": 12,
		"golf": 34, "hotel": 16, "indio": 87,
		"juliet": 65, "kili": 43, "lima": 98}
)

func main() {
	fmt.Println("unsorted:")
	for k, v := range barVal {
		fmt.Printf("Key: %v, Value: %v / ", k, v)
	}
	keys := make([]string, len(barVal))
	i := 0
	for k, _ := range barVal {
		keys[i] = k
		i++
	}
	sort.Strings(keys)
	fmt.Println()
	fmt.Println("sorted:")
	for _, k := range keys {
		fmt.Printf("Key: %v, Value: %v / ", k, barVal[k])
	}
}
```



## 8.6 将 map 的键值对调

这里对调是指调换 key 和 value。如果 map 的值类型可以作为 key 且所有的 value 是唯一的，那么通过下面的方法可以简单的做到键值对调。

```go
package main

import (
	"fmt"
)

var (
	barVal = map[string]int{"alpha": 34, "bravo": 56, "charlie": 23,
		"delta": 87, "echo": 56, "foxtrot": 12,
		"golf": 34, "hotel": 16, "indio": 87,
		"juliet": 65, "kili": 43, "lima": 98}
)

func main() {
	invMap := make(map[int]string, len(barVal))
	for k, v := range barVal {
		invMap[v] = k
	}
	fmt.Println("inverted:")
	for k, v := range invMap {
		fmt.Printf("Key: %v, Value: %v / ", k, v)
	}
}
```

如果原始 value 值不唯一那这么做肯定会出问题；这种情况下不会报错，但是当遇到不唯一的 key 时应当直接停止对调，且此时对调后的 map 很可能没有包含原 map 的所有键值对！一种解决方法就是仔细检查唯一性并且使用多值 map，比如使用 `map[int][]string` 类型。



---



# 第9章 包（package）

