# GO语言

> [源文档地址](http://c.biancheng.net/golang/)



# 一、简介





# 二、基本语法

## 2.14、指针

### 2.14.1、概述

- 与 [Java](http://c.biancheng.net/java/) 和 .NET 等编程语言不同，[Go语言](http://c.biancheng.net/golang/)为程序员提供了控制[数据结构](http://c.biancheng.net/data_structure/)指针的能力，但是，并不能进行指针运算。Go语言允许你控制特定集合的数据结构、分配的数量以及内存访问模式，这对于构建运行良好的系统是非常重要的。指针对于性能的影响不言而喻，如果你想要做系统编程、操作系统或者网络应用，指针更是不可或缺的一部分。
- 指针（pointer）在Go语言中可以被拆分为两个核心概念：
    - 类型指针，允许对这个指针类型的数据进行修改，传递数据可以直接使用指针，而无须拷贝数据，类型指针不能进行偏移和运算。
    - 切片，由指向起始元素的原始指针、元素数量和容量组成。
- 受益于这样的约束和拆分，Go语言的指针类型变量即拥有指针高效访问的特点，又不会发生指针偏移，从而避免了非法修改关键性数据的问题。同时，垃圾回收也比较容易对不会发生偏移的指针进行检索和回收。
- 切片比原始指针具备更强大的特性，而且更为安全。切片在发生越界时，运行时会报出宕机，并打出堆栈，而原始指针只会崩溃。

### 2.14.2、C/[C++](http://c.biancheng.net/cplus/)中的指针

- 说到 C/C++ 中的指针，会让许多人“谈虎色变”，尤其是对指针的偏移、运算和转换。
- 其实，指针是 C/C++ 语言拥有极高性能的根本所在，在操作大块数据和做偏移时即方便又便捷。因此，操作系统依然使用[C语言](http://c.biancheng.net/c/)及指针的特性进行编写。
- C/C++ 中指针饱受诟病的根本原因是指针的运算和内存释放，C/C++ 语言中的裸指针可以自由偏移，甚至可以在某些情况下偏移进入操作系统的核心区域，我们的计算机操作系统经常需要更新、修复漏洞的本质，就是为解决指针越界访问所导致的“缓冲区溢出”的问题。
- 要明白指针，需要知道几个概念：指针地址、指针类型和指针取值，下面将展开详细说明。

### 2.14.3、认识指针地址和指针类型

- 一个指针变量可以指向任何一个值的内存地址，它所指向的值的内存地址在 32 和 64 位机器上分别占用 4 或 8 个字节，占用字节的大小与所指向的值的大小无关。当一个指针被定义后没有分配到任何变量时，它的默认值为 nil。指针变量通常缩写为 ptr。
- 每个变量在运行时都拥有一个地址，这个地址代表变量在内存中的位置。Go语言中使用在变量名前面添加`&`操作符（前缀）来获取变量的内存地址（取地址操作），格式如下：

```go
// 其中 v 代表被取地址的变量，变量 v 的地址使用变量 ptr 进行接收，ptr 的类型为*T，
// 称做 T 的指针类型，*代表指针。
ptr := &v    // v 的类型为 T
```

- 指针实际用法，可以通过下面的例子了解：

```go
package main
import (
    "fmt"
)
func main() {
    var cat int = 1
    var str string = "banana"
    fmt.Printf("%p %p", &cat, &str)
}

// - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
// 代码输出如下：
// 0xc042052088 0xc0420461b0
// - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

提示：变量、指针和地址三者的关系是，每个变量都拥有地址，指针的值就是地址。
```

### 2.14.4、从指针获取指针指向的值

- 当使用 `&` 操作符对普通变量进行取地址操作并得到变量的指针后，可以对指针使用 `*` 操作符，也就是指针取值，代码如下。

```go
package main
import (
    "fmt"
)
func main() {
    // 准备一个字符串类型
    var house = "Malibu Point 10880, 90265"
    // 对字符串取地址, ptr类型为*string
    ptr := &house
    // 打印ptr的类型
    fmt.Printf("ptr type: %T\n", ptr)
    // 打印ptr的指针地址
    fmt.Printf("address: %p\n", ptr)
    // 对指针进行取值操作
    value := *ptr
    // 取值后的类型
    fmt.Printf("value type: %T\n", value)
    // 指针取值后就是指向变量的值
    fmt.Printf("value: %s\n", value)
}

// - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
// 代码输出如下：
// ptr type: *string
// address: 0xc0420401b0
// value type: string
// value: Malibu Point 10880, 90265
// - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

```

- 取地址操作符`&`和取值操作符`*`是一对互补操作符，`&`取出地址，`*`根据地址取出地址指向的值。
- 变量、指针地址、指针变量、取地址、取值的相互关系和特性如下：
    - 对变量进行取地址操作使用`&`操作符，可以获得这个变量的指针变量。
    - 指针变量的值是指针地址。
    - 对指针变量进行取值操作使用`*`操作符，可以获得指针变量指向的原变量的值。

### 2.14.5、使用指针修改值

- 通过指针不仅可以取值，也可以修改值。
- 前面已经演示了使用多重赋值的方法进行数值交换，使用指针同样可以进行数值交换，代码如下：

```go
package main

import "fmt"

// 交换函数
func swap(a, b *int) {
    // 取a指针的值, 赋给临时变量t
    t := *a
    // 取b指针的值, 赋给a指针指向的变量
    *a = *b
    // 将a指针的值赋给b指针指向的变量
    *b = t
}

func main() {
	// 准备两个变量, 赋值1和2
    x, y := 1, 2
    // 交换变量值
    swap(&x, &y)
    // 输出变量值
    fmt.Println(x, y)
}

// - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
// 代码输出如下：
// 2 1
// - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
```

- `*`操作符作为右值时，意义是取指针的值，作为左值时，也就是放在赋值操作符的左边时，表示 a 指针指向的变量。其实归纳起来，`*`操作符的根本意义就是操作指针指向的变量。当操作在右值时，就是取指向变量的值，当操作在左值时，就是将值设置给指向的变量。
- 如果在 swap() 函数中交换操作的是指针值，会发生什么情况？可以参考下面代码：

```go
package main
import "fmt"
func swap(a, b *int) {
    b, a = a, b
}
func main() {
    x, y := 1, 2
    swap(&x, &y)
    fmt.Println(x, y)
}

// - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
// 代码输出如下：
// 1 2
// - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

结果表明，交换是不成功的。上面代码中的 swap() 函数交换的是 a 和 b 的地址，在交换完毕后，a 和 b 的变量值确实被交换。但和 a、b 关联的两个变量并没有实际关联。这就像写有两座房子的卡片放在桌上一字摊开，交换两座房子的卡片后并不会对两座房子有任何影响。
```

### 2.14.6、示例：使用指针变量获取命令行的输入信息

- Go语言内置的 flag 包实现了对命令行参数的解析，flag 包使得开发命令行工具更为简单。
- 下面的代码通过提前定义一些命令行指令和对应的变量，并在运行时输入对应的参数，经过 flag 包的解析后即可获取命令行的数据。
- 【示例】获取命令行输入：

```go
package main
// 导入系统包
import (
    "flag"
    "fmt"
)
// 定义命令行参数
var mode = flag.String("mode", "", "process mode")
func main() {
    // 解析命令行参数
    flag.Parse()
    // 输出命令行参数
    fmt.Println(*mode)
}

// 将这段代码命名为 main.go，然后使用如下命令行运行：
go run main.go --mode=fast
// - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
// 代码输出如下：
// fast
// - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

```

### 2.14.7、创建指针的另一种方法——new() 函数

- Go语言还提供了另外一种方法来创建指针变量，格式如下：

```go
new(类型)
```

一般这样写：

```go
// new() 函数可以创建一个对应类型的指针，创建过程会分配内存，被创建的指针指向默认值。
str := new(string)
*str = "Go语言教程"
fmt.Println(*str)
```



## 2.15、变量逃逸分析



## 2.16、变量的生命周期



## 2.17、常量

### 2.17.1、定义

- 常量的定义格式和变量的声明语法类似：`const name [type] = value`，例如：

```go
// 在Go语言中，你可以省略类型说明符 [type]，因为编译器可以根据变量的值来推断其类型。
const pi = 3.14159 // 相当于 math.Pi 的近似值
```

- 和变量声明一样，可以批量声明多个常量：

```go
const (
    e  = 2.7182818
    pi = 3.1415926
)
```

- 常量是在编译时被创建的，即使定义在函数内部也是如此，并且只能是布尔型、数字型（整数型、浮点型和复数）和字符串型。由于编译时的限制，定义常量的表达式必须为能被编译器求值的常量表达式。
- 所有常量的运算都可以在编译期完成，这样不仅可以减少运行时的工作，也方便其他代码的编译优化，当操作数是常量时，一些运行时的错误也可以在编译时被发现，例如整数除零、字符串索引越界、任何导致无效浮点数的操作等。
- 一个常量的声明也可以包含一个类型和一个值，但是如果没有显式指明类型，那么将从右边的表达式推断类型。

### 2.17.2、iota 常量生成器

- 常量声明可以使用 iota 常量生成器初始化，它用于生成一组以相似规则初始化的常量，但是不用每行都写一遍初始化表达式。在一个 const 声明语句中，**在第一个声明的常量所在的行，iota 将会被置为 0，然后在每一个有常量声明的行加一**。

```go
// 首先定义一个 Weekday 命名类型，然后为一周的每天定义了一个常量，从周日 0 开始。
// 在其它编程语言中，这种类型一般被称为枚举类型。
// 周日将对应 0，周一为 1，以此类推。

type Weekday int

const (
    Sunday Weekday = iota
    Monday
    Tuesday
    Wednesday
    Thursday
    Friday
    Saturday
)
```

### 2.17.3、无类型常量

- Go语言的常量有个不同寻常之处。虽然一个常量可以有任意一个确定的基础类型，例如 int 或 float64，或者是类似 time.Duration 这样的基础类型，但是许多常量并没有一个明确的基础类型。
- 编译器为这些没有明确的基础类型的数字常量提供比基础类型更高精度的算术运算，可以认为至少有 256bit 的运算精度。这里有六种未明确类型的常量类型，分别是:
    - 无类型的布尔型、无类型的整数、无类型的字符、无类型的浮点数、无类型的复数、无类型的字符串。

- 通过延迟明确常量的具体类型，不仅可以提供更高的运算精度，而且可以直接用于更多的表达式而不需要显式的类型转换。

```go
// math.Pi 无类型的浮点数常量，可以直接用于任意需要浮点数或复数的地方：
var x float32 = math.Pi
var y float64 = math.Pi
var z complex128 = math.Pi

// 如果 math.Pi 被确定为特定类型，比如 float64，那么结果精度可能会不一样，
// 同时对于需要 float32 或 complex128 类型值的地方则需要一个明确的强制类型转换：
const Pi64 float64 = math.Pi
var x float32 = float32(Pi64)
var y float64 = Pi64
var z complex128 = complex128(Pi64)
```

- 对于常量面值，不同的写法可能会对应不同的类型。例如 0、0.0、0i 和 \u0000 虽然有着相同的常量值，但是它们分别对应无类型的整数、无类型的浮点数、无类型的复数和无类型的字符等不同的常量类型。同样，true 和 false 也是无类型的布尔类型，字符串面值常量是无类型的字符串类型。





## 2.18、模拟枚举

### 2.18.1、枚举

- Go语言现阶段没有枚举类型，但是可以使用 const 常量配合上一节《Go语言常量》中介绍的 iota 来模拟枚举类型，请看下面的代码：

```go
type Weapon int

const (
     Arrow Weapon = iota    // 开始生成枚举值, 默认为0
     Shuriken
     SniperRifle
     Rifle
     Blower
)

// 输出所有枚举值
fmt.Println(Arrow, Shuriken, SniperRifle, Rifle, Blower)

// 使用枚举类型并赋初值
var weapon Weapon = Blower
fmt.Println(weapon)

// - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
// 代码输出如下：
// 0 1 2 3 4
// 4
```

- 当然，iota 不仅可以生成每次增加 1 的枚举值。还可以利用 iota 来做一些强大的枚举常量值生成器。下面的代码可以方便的生成标志位常量：

```go
const (
    FlagNone = 1 << iota  // 移位
    FlagRed
    FlagGreen
    FlagBlue
)

fmt.Printf("%d %d %d\n", FlagRed, FlagGreen, FlagBlue)  // 十进制输出
fmt.Printf("%b %b %b\n", FlagRed, FlagGreen, FlagBlue)  // 二进制输出

// - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
// 代码输出如下：
// 2 4 8
// 10 100 1000
```

### 2.18.2、将枚举值转换为字符串

- 枚举在 [C#](http://c.biancheng.net/csharp/) 中是一个独立的类型，可以通过枚举值获取该值对应的字符串。例如，C# 中 Week 枚举值 Monday 为 1，那么可以通过 Week.Monday.ToString() 函数获得 Monday 字符串。
- Go语言中也可以实现这一功能，代码如下所示：

```go
package main

import "fmt"

// 声明芯片类型
type ChipType int

//定义 ChipType 类型的方法 String()，返回值为字符串类型。
const (
    None ChipType = iota
    CPU    // 中央处理器
    GPU    // 图形处理器
)

// 定义 ChipType 类型的方法 String()，返回值为字符串类型。
func (c ChipType) String() string {
    switch c {
    case None:
        return "None"
    case CPU:
        return "CPU"
    case GPU:
        return "GPU"
    }

    return "N/A"
}

func main() {

    // 输出CPU的值并以整型格式显示
    fmt.Printf("%s %d", CPU, CPU)
}

// 定义了 String() 方法的 ChipType 在使用上和普通的常量没有区别。
// 当这个类型需要显示为字符串时，Go语言会自动寻找 String() 方法并进行调用。


// - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
// 代码输出如下：
// CPU 1
```



## 2.19、类型别名

### 2.19.1、概述

- 类型别名是 Go 1.9 版本添加的新功能，主要用于解决代码升级、迁移中存在的类型兼容性问题。在 C/[C++](http://c.biancheng.net/cplus/) 语言中，代码重构升级可以使用宏快速定义一段新的代码，Go语言中没有选择加入宏，而是解决了重构中最麻烦的类型名变更问题。

- 在 Go 1.9 版本之前定义内建类型的代码是这样写的：

```go
type byte uint8
type rune int32
```

- 而在 Go 1.9 版本之后变为：

```go
type byte = uint8
type rune = int32
// 这个修改就是配合类型别名而进行的修改。
```

### 2.19.2、区分类型别名与类型定义

- 定义类型别名的写法为：

```go
// 类型别名规定：TypeAlias 只是 Type 的别名，本质上 TypeAlias 与 Type 是同一个类型，
// 就像一个孩子小时候有小名、乳名，上学后用学名，英语老师又会给他起英文名，但这些名字都指的是他本人。
type TypeAlias = Type
```

- 类型别名与类型定义表面上看只有一个等号的差异，那么它们之间实际的区别有哪些呢？下面通过一段代码来理解。

```go
package main

import (
    "fmt"
)

// 将NewInt定义为int类型
type NewInt int

// 将 IntAlias 设置为 int 的一个别名，使用 IntAlias 与 int 等效。
type IntAlias = int

func main() {

    // 将a声明为NewInt类型
    var a NewInt
    // 查看a的类型名
    fmt.Printf("a type: %T\n", a)

    // 将a2声明为IntAlias类型
    var a2 IntAlias
    // 查看a2的类型名
    fmt.Printf("a2 type: %T\n", a2)
}

// - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
// 代码输出如下：
// a type: main.NewInt
// a2 type: int
// - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

// 结果显示 a 的类型是 main.NewInt，表示 main 包下定义的 NewInt 类型，
// a2 类型是 int，IntAlias 类型只会在代码中存在，编译完成时，不会有 IntAlias 类型。

```

### 2.19.3、非本地类型不能定义方法

- 能够随意地为各种类型起名字，是否意味着可以在自己包里为这些类型任意添加方法呢？参见下面的代码演示：

```go
package main
import (
    "time"
)
// 定义time.Duration的别名为MyDuration
type MyDuration = time.Duration
// 为MyDuration添加一个函数
func (m MyDuration) EasySet(a string) {
}
func main() {
}

// - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
// 编译上面代码报错，信息如下：
// cannot define new methods on non-local type time.Duration

// 编译器提示：不能在一个非本地的类型 time.Duration 上定义新方法，
// 非本地类型指的就是 time.Duration 不是在 main 包中定义的，
// 而是在 time 包中定义的，与 main 包不在同一个包中，因此不能为不在一个包中的类型定义方法。
// - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

解决这个问题有下面两种方法：
	1.将第 8 行修改为 type MyDuration time.Duration，也就是将 MyDuration 从别名改为类型；
	2.将 MyDuration 的别名定义放在 time 包中。
```

### 2.19.4、在结构体成员嵌入时使用别名

- 当类型别名作为结构体嵌入的成员时会发生什么情况呢？请参考下面的代码。

```go
package main

import (
    "fmt"
    "reflect"
)

// 定义商标结构
type Brand struct {
}

// 为商标结构添加Show()方法
func (t Brand) Show() {
}

// 为Brand定义一个别名FakeBrand
type FakeBrand = Brand

// 定义车辆结构
type Vehicle struct {

    // 嵌入两个结构
    FakeBrand
    Brand
}

func main() {

    // 声明变量a为车辆类型
    var a Vehicle
   
    // 指定调用FakeBrand的Show
    a.FakeBrand.Show()

    // 取a的类型反射对象
    ta := reflect.TypeOf(a)

    // 遍历a的所有成员
    for i := 0; i < ta.NumField(); i++ {

        // a的成员信息
        f := ta.Field(i)

        // 打印成员的字段名和类型
        fmt.Printf("FieldName: %v, FieldType: %v\n", f.Name, f.Type.
            Name())
    }
}

// - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
// 代码输出如下：
// FieldName: FakeBrand, FieldType: Brand
// FieldName: Brand, FieldType: Brand
// - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

// 这个例子中，FakeBrand 是 Brand 的一个别名，
// 在 Vehicle 中嵌入 FakeBrand 和 Brand 并不意味着嵌入两个 Brand，
// FakeBrand 的类型会以名字的方式保留在 Vehicle 的成员中。

// - - - - - - - - - -
// 如果尝试将 a.FakeBrand.Show() 改为 a.Show()
// 编译器将发生报错：
//     ambiguous selector a.Show
// 在调用 Show() 方法时，因为两个类型都有 Show() 方法，会发生歧义，
// 证明 FakeBrand 的本质确实是 Brand 类型。
```



## 2.20、注释的定义及使用

### 2.20.1、概述

- 在讲解[Go语言](http://c.biancheng.net/golang/)注释之前，大家可以先来看一下下面这段代码。

```go
//test.go
package main
import "fmt" // 引入 fmt 包
func main() {
   fmt.Printf("Καλημέρα κόσμε; or こんにちは 世界\n")
}
```

- 注释不会被编译，但可以通过 godoc 来获取。
- Go语言中注释一般分为两种，分别是单行注释和多行注释：
    - **单行注释** 是最常见的注释形式，可以在任何地方使用以`//`开头的单行注释；
    - **多行注释** 也叫块注释，均已以`/*`开头，并以`*/`结尾，且不可以嵌套使用，多行注释一般用于包的文档描述或注释成块的代码片段。

- 每一个包都应该有相关注释，上述代码中在 package 语句之前的注释内容将被默认认为是这个包的文档说明，其中应该提供一些相关信息并对整体功能做简要的介绍，一个包可以分散在多个文件中，但是只需要对其中一个进行注释说明即可。
- 当开发人员需要了解包的一些情况时，自然会用 godoc 来显示包的文档说明。在首行的简要注释之后可以用成段的注释来进行更详细的说明，而不必拥挤在一起。另外，在多段注释之间应以空行分隔加以区分，如下所示：

```go
// Package superman implements methods for saving the world.
//
// Experience has shown that a small number of procedures can prove
// helpful when attempting to save the world.
package superman
```

### 2.20.2、godoc 工具



## 2.21、关键字与标识

### 2.21.1、概述

- [Go语言](http://c.biancheng.net/golang/)的代码中的几乎所有东西都有一个名称或标识符，另外，Go语言是区分大小写的，这与 C 家族中的其它语言相同。有效的标识符必须以字符（可以使用任何 UTF-8 编码的字符或`_`）开头，然后紧跟着 0 个或多个字符或 Unicode 数字，如：X56、group1、_x23、i、өԑ12。
- 以下是无效的标识符：
    - 1ab（以数字开头）
    - case（Go语言的关键字）
    - a+b（运算符是不允许的）

- `_`本身就是一个特殊的标识符，被称为空白标识符，它可以像其他标识符那样用于变量的声明或赋值（任何类型都可以赋值给它），但任何赋给这个标识符的值都将被抛弃，因此这些值不能在后续的代码中使用，也不可以使用这个标识符作为变量对其它变量进行赋值或运算。
- 在编码过程中，可能会遇到没有名称的变量、类型或方法，这样做的好处是可以极大地增强代码的灵活性，这些变量被统称为匿名变量。
- 下表列举了Go语言中会使用到的 25 个关键字或保留字：

| break    | default     | func   | interface | select |
| -------- | ----------- | ------ | --------- | ------ |
| case     | defer       | go     | map       | struct |
| chan     | else        | goto   | package   | switch |
| const    | fallthrough | if     | range     | type   |
| continue | for         | import | return    | var    |

- 之所以刻意地将Go语言中的关键字保持的这么少，是为了简化在编译过程第一步中的代码解析。和其它语言一样，关键字不能够作标识符使用。
- 除了以上介绍的这些关键字，Go语言还有 36 个预定义标识符，其中包含了基本类型的名称和一些基本的内置函数（见下表），它们的作用都将在接下来的章节中进行进一步地讲解。

| append | bool    | byte    | cap     | close  | complex | complex64 | complex128 | uint16  |
| ------ | ------- | ------- | ------- | ------ | ------- | --------- | ---------- | ------- |
| copy   | false   | float32 | float64 | imag   | int     | int8      | int16      | uint32  |
| int32  | int64   | iota    | len     | make   | new     | nil       | panic      | uint64  |
| print  | println | real    | recover | string | true    | uint      | uint8      | uintptr |



## 2.22、字符串和数字的相互转换

### 2.22.1、概述

- 除了字符串、文字符号和字节之间的转换，我们常常也需要相互转换数值及其字符串表示形式。这由 strconv 包的函数完成。

- 要将整数转换成字符串，一种选择是使用`fmt.Sprintf`，另一种做法是用函数`strconv.itoa("integer to ASCII")`：

```go
x := 123
y := fmt.Sprintf("%d", x)
fmt.Println(y, strconv.Itoa(x)) // "123 1231"
```

- FormatInt 和 FormatUint 可以按不同的进位制格式化数字：

```go
fmt.Println(strconv.FormatInt(int64(x), 2)) // "1111011"
```

- fmt.Printf 里的谓词 %b、%d、%o 和 %x 往往比 Format 函数方便，若要包含数字以外的附加信息，它就尤其有用：

```go
s := fmt.Sprintf("x=%b", x) //"x=1111011"
```

- strconv 包内的 Atoi 函数或 ParseInt 函数用于解释表示整数的字符串，而 ParseUint 用于无符号整数：

```go
x, err := strconv.Atoi("123")                // x 是整型
y, err := strconv.ParseInt("123", 10, 64)    // 十进制，最长为 64 位
// ParseInt 的第三个参数指定结果必须匹配何种大小的整型；例如，16 表示 int16，而 0 作为特殊值表示 int。任何情况下，结果 y 的类型总是 int64，可将他另外转换成较小的类型。
```

- 有时候，单行输入由字符串和数字依次混合构成，需要用 fmt.Scanf 解释，可惜 fmt.Scanf 也许不够灵活，处理不完整或不规则输入时尤甚。



## 2.23、组合和方法集

### 2.23.1、概述

- 结构类型（struct）为[Go语言](http://c.biancheng.net/golang/)提供了强大的类型扩展，主要体现在两个方面：
    - 第一，struct 可以嵌入任意其他类型的字段；
    - 第二，struct 可以嵌套自身的指针类型的字段。

- 这两个特性决定了 struct 类型有着强大的表达力，几乎可以表示任意的[数据结构](http://c.biancheng.net/data_structure/)。同时，结合结构类型的方法，“数据＋方法”可以灵活地表达程序逻辑。

- Go语言的结构（struct）和[C语言](http://c.biancheng.net/c/)的 struct 一样，内存分配按照字段顺序依次开辟连续的存储空间，没有插入额外的东西（除字段对齐外），不像 [C++](http://c.biancheng.net/cplus/) 那样为了实现多态在对象内存模型里插入了虚拟函数指针，这种设计的优点使数据和逻辑彻底分离，对象内存区只存放数据，干净简单；类型的方法也是显式带上接收者，没有像 C++ 一样使用隐式的 this 指针，这是一种优秀的设计方法。
- Go语言中的数据就是数据，逻辑就是逻辑，二者是“正交”的，底层实现上没有相关性，在语言使用层又为开发者提供了统一的数据和逻辑抽象视图，***这种外部统一、内部隔离的面向对象设计*** 是Go语言优秀设计的体现。

### 2.23.2、组合

- 从前面讨论的命名类型的方法可知，使用 type 定义的新类型不会继承原有类型的方法，有个特例就是命名结构类型，命名结构类型可以嵌套其他的命名类型的字段，外层的结构类型是可以调用嵌入字段类型的方法，这种调用既可以是显式的调用，也可以是隐式的调用。这就是 Go 的“继承”，准确地说这就是 Go 的“组合”。
- 因为Go语言没有继承的语义，结构和字段之间是“has k”的关系，而不是“is a”的关系；没有父子的概念，仅仅是整体和局部的概念，所以后续统称这种嵌套的结构和字段的关系为组合。
- struct 中的组合非常灵活，可以表现为水平的字段扩展，由于 struct 可以嵌套其他 struct 字段，所以组合也可以分层次扩展。struct 类型中的字段称为“内嵌字段”，内嵌字段的访问和方法调用遵照的规约接下来进行讲解。

#### 2.23.2.1、内嵌字段的初始化和访问

- struct 的字段访问使用点操作符“.”，struct 的字段可以嵌套很多层，只要内嵌的字段是唯一的即可，不需要使用全路径进行访问。在以下示例中，可以使用 z.a 代替 z.Y.X.a。

```go
package main
type X struct {
    a int
}
type Y struct {
    X
    b int
}
type Z struct {
    Y
    c int
}
func main() {
    x := X{a: 1}
    y := Y{
        X: x,
        b: 2,
    }
    z := Z{
        Y: y,
        c: 3,
    }
    //z.a, z.Y.a, z.Y.X.a 三者是等价的， z.a z.Y.a 是 z.Y.X.a 的简写
    println(z.a, z.Y.a, z.Y.X.a) //1 1 1
    z = Z{}
    z.a = 2
    println(z.a, z.Y.a, z.Y.X.a) //2 2 2
}
```

- 在 struct 的多层嵌套中，不同嵌套层次可以有相同的字段，此时最好使用完全路径进行访问和初始化。在实际数据结构的定义中应该尽量避开相同的字段，以免在使用中出现歧义。例如：

```go
package main
type X struct {
    a int
}
type Y struct {
    X
    a int
}
type Z struct {
    Y
    a int
}
func main() {
    x := X{a: 1}
    y := Y{
        X: x,
        a: 2,
    }
    z := Z{
        Y: y,
        a: 3,
    }
    //此时的z.a, z.Y.a, z.Y.X.a 代表不同的字段
    println(z.a, z.Y.a, z.Y.X.a) // 3 2 1
    z = Z{}
    z.a = 4
    z.Y.a = 5
    z.Y.X.a = 6
    //此时的z.a, z.Y.a, z.Y.X.a 代表不同的字段
    println(z.a, z.Y.a, z.Y.X.a) // 4 5 6
}
```

#### 2.23.2.2、内嵌字段的方法调用

- struct 类型方法调用也使用点操作符，不同嵌套层次的字段可以有相同的方法，外层变量调用内嵌字段的方法时也可以像嵌套字段的访问一样使用简化模式。如果外层字段和内层字段有相同的方法，则使用简化模式访问外层的方法会覆盖内层的方法。
- 即在简写模式下，Go 编译器优先从外向内逐层查找方法，同名方法中外层的方法能够覆盖内层的方法。这个特性有点类似于面向对象编程中，子类覆盖父类的同名方法。示例如下：

```go
package main
import "fmt"
type X struct {
    a int
}
type Y struct {
    X
    b int
}
type Z struct {
    Y
    c int
}
func (x X) Print() {
    fmt.Printf("In X, a ＝ %d\n", x.a)
}
func (x X) XPrint() {
    fmt.Printf("In X, a ＝ %d\n", x.a)
}
func (y Y) Print() {
    fmt.Printf("In Y, b ＝ %d\n", y.b)
}
func (z Z) Print() {
    fmt.Printf("In Z, c ＝ %d\n", z.c)
    //显式的完全路径调用内嵌字段的方法
    z.Y.Print()
    z.Y.X.Print()
}
func main() {
    x := X{a: 1}
    y := Y{
        X: x,
        b: 2,
    }
    z := Z{
        Y: y,
        c: 3,
    }
    //从外向内查找，首先找到的是 Z 的 Print() 方法
    z.Print()
    //从外向内查找，最后找到的是 x 的 XPrint()方法
    z.XPrint()
    z.Y.XPrint()
}
```

- 不推荐在多层的 struct 类型中内嵌多个同名的字段；但是并不反对 struct 定义和内嵌字段同名方法的用法，因为这提供了一种编程技术，使得 struct 能够重写内嵌字段的方法，提供面向对象编程中子类覆盖父类的同名方法的功能。



### 2.23.3、组合的方法集

- $\color{red}{\large组合结构的方法集有如下规则}$：
    - 若类型 S 包含匿名字段 ` T`，则 S 的方法集包含 `T` 的方法集。
    - 若类型 S 包含匿名字段 `*T`，则 S 的方法集包含 `T` 和 `*T` 方法集。
    - 不管类型 S 中嵌入的匿名字段是 `T` 还是 `*T`，`*S` 方法集总是包含 `T` 和 `*T` 方法集。

- 下面举个例子来验证这个规则的正确性，前面讲到方法集时提到 Go 编译器会对方法调用进行自动转换，为了阻止自动转换，本示例使用方法表达式的调用方式，这样能更清楚地理解这个方法集的规约。

```go
package main
type X struct {
    a int
}
type Y struct {
    X
}
type Z struct {
    *X
}
func (x X) Get() int {
    return x.a
}
func (x *X) Set(i int) {
    x.a = i
}
func main() {
    x := X{a: 1}
    y := Y{
        X: x,
    }
    println(y.Get()) // 1
    //此处编译器做了自动转换
    y.Set(2)
    println(y.Get()) // 2
    //为了不让编译器做自动转换，使用方法表达式调用方式
    //Y 内嵌字段 X，所以 type y 的方法集是 Get, type *Y 的方法集是 Set Get
    (*Y).Set(&y, 3)
    //type y 的方法集合并没有 Set 方法，所以下一句编译不能通过
    //Y.Set(y, 3)
    println(y.Get()) // 3
    z := Z{
        X: &x,
    }
    //按照嵌套字段的方法集的规则
    //Z 内嵌字段＊X ，所以 type Z 和 type *Z 方法集都包含类型 X 定义的方法 Get 和 Set
    //为了不让编译器做自动转换，仍然使用方法表达式调用方式
    Z.Set(z, 4)
    println(z.Get()) // 4
    (*Z).Set(&z, 5)
    println(z.Get()) // 5
}
```

- 到目前为止还没有发现方法集有多大的用途，而且通过实践发现，Go 编译器会进行自动转换，看起来不需要太关注方法集，这种认识是错误的。编译器的自动转换仅适用于直接通过类型实例调用方法时才有效，类型实例传递给接口时，编译器不会进行自动转换，而是会进行严格的方法集校验。
- Go 函数的调用实参都是值拷贝，方法调用参数传递也是一样的机制，具体类型变量传递给接口时也是值拷贝，如果传递给接口变量的是值类型，但调用方法的接收者是指针类型，则程序运行时虽然能够将接收者转换为指针，但这个指针是副本的指针，并不是我们期望的原变量的指针。(???)
- 所以语言设计者为了杜绝这种非期望的行为，在编译时做了严格的方法集合的检查，不允许产生这种调用；如果传递给接口的变量是指针类型，则接口调用的是值类型的方法，程序运行时能够自动转换为值类型，这种转换不会带来副作用，符合调用者的预期，所以这种转换是允许的，而且这种情况符合方法集的规约。(???)



## 2.24、Go语言嵌入类型

- [Go语言](http://c.biancheng.net/golang/)允许用户扩展或者修改已有类型的行为。这个功能对代码复用很重要，在修改已有类型以符合新类型的时候也很重要。这个功能是通过嵌入类型（type embedding）完成的。嵌入类型是将已有的类型直接声明在新的结构类型里。被嵌入的类型被称为新的外部类型的内部类型。

- 通过嵌入类型，与内部类型相关的标识符会提升到外部类型上。这些被提升的标识符就像直接声明在外部类型里的标识符一样，也是外部类型的一部分。这样外部类型就组合了内部类型包含的所有属性，并且可以添加新的字段和方法。外部类型也可以通过声明与内部类型标识符同名的标识符来覆盖内部标识符的字段或者方法。这就是扩展或者修改已有类型的方法。
- 让我们通过一个示例程序来演示嵌入类型的基本用法，代码如下所示。

```go
// 这个示例程序展示如何将一个类型嵌入另一个类型，以及
// 内部类型和外部类型之间的关系
package main
import (
    "fmt"
)
// user 在程序里定义一个用户类型
type user struct {
    name string
    email string
}
// notify 实现了一个可以通过user 类型值的指针
// 调用的方法
func (u *user) notify() {
    fmt.Printf("Sending user email to %s<%s>\n",
    u.name,
    u.email)
}
// admin 代表一个拥有权限的管理员用户
type admin struct {
    user // 嵌入类型
    level string
}
// main 是应用程序的入口
func main() {
    // 创建一个admin 用户
    ad := admin{
        user: user{
            name: "john smith",
            email: "john@yahoo.com",
        },
        level: "super",
    }
    // 我们可以直接访问内部类型的方法
    ad.user.notify()
    // 内部类型的方法也被提升到外部类型
    ad.notify()
}
```

- 让我们修改一下这个例子，加入一个接口，代码如下所示。

```go
// 这个示例程序展示如何将嵌入类型应用于接口
package main
import (
    "fmt"
)
// notifier 是一个定义了
// 通知类行为的接口
type notifier interface {
    notify()
}
// user 在程序里定义一个用户类型
type user struct {
    name string
    email string
}
// 通过 user 类型值的指针
// 调用的方法
func (u *user) notify() {
    fmt.Printf("Sending user email to %s<%s>\n",
    u.name,
    u.email)
}
// admin 代表一个拥有权限的管理员用户
type admin struct {
    user
    level string
}
// main 是应用程序的入口
func main() {
    // 创建一个 admin 用户
    ad := admin{
        user: user{
            name: "john smith",
            email: "john@yahoo.com",
        },
        level: "super",
    }
    // 给 admin 用户发送一个通知
    // 用于实现接口的内部类型的方法，被提升到
    // 外部类型
    sendNotification(&ad)
}
// sendNotification 接受一个实现了notifier 接口的值
// 并发送通知
func sendNotification(n notifier) {
    n.notify()
}

// - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
// 代码输出如下：
// Sending user email to john smith<john@yahoo.com>
// - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

由于内部类型的提升，内部类型实现的接口会自动提升到外部类型。这意味着由于内部类型的实现，外部类型也同样实现了这个接口。运行这个示例程序，会得到如下所示的输出。
```

- 如果外部类型并不需要使用内部类型的实现，而想使用自己的一套实现，该怎么办？让我们看另一个示例程序是如何解决这个问题的，代码如下所示。

```go
// 这个示例程序展示当内部类型和外部类型要
// 实现同一个接口时的做法
package main
import (
    "fmt"
)
// notifier 是一个定义了
// 通知类行为的接口
type notifier interface {
    notify()
}
// user 在程序里定义一个用户类型
type user struct {
    name string
    email string
}
// 通过user 类型值的指针
// 调用的方法
func (u *user) notify() {
    fmt.Printf("Sending user email to %s<%s>\n",
        u.name,
        u.email)
}
// admin 代表一个拥有权限的管理员用户
type admin struct {
    user
    level string
}
// 通过 admin 类型值的指针
// 调用的方法
func (a *admin) notify() {
    fmt.Printf("Sending admin email to %s<%s>\n",
        a.name,
        a.email)
}
// main 是应用程序的入口
func main() {
    // 创建一个 admin 用户
    ad := admin{
        user: user{
            name: "john smith",
            email: "john@yahoo.com",
        },
        level: "super",
    }
    // 给admin 用户发送一个通知
    // 接口的嵌入的内部类型实现并没有提升到
    // 外部类型
    sendNotification(&ad)
    // 我们可以直接访问内部类型的方法
    ad.user.notify()
    // 内部类型的方法没有被提升
    ad.notify()
}
// sendNotification 接受一个实现了 notifier 接口的值
// 并发送通知
func sendNotification(n notifier) {
    n.notify()
}

上面代码所示的示例程序的大部分和之前的程序相同，只有一些小变化，这个示例程序为 admin 类型增加了 notifier 接口的实现。当 admin 类型的实现被调用时，会显示 "Sending admin email"。作为对比，user 类型的实现被调用时，会显示 "Sending user email"。

main 函数里也有一些变化，在代码的第 46 行，我们再次创建了外部类型的变量 ad。在第 57 行，将 ad 变量的地址传给 sendNotification 函数，这个指针实现了接口所需要的方法集。

// - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
// 代码输出如下：
// Sending admin email to john smith<john@yahoo.com>
// Sending user email to john smith<john@yahoo.com>
// Sending admin email to john smith<john@yahoo.com>
// - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
这次我们看到了 admin 类型是如何实现 notifier 接口的，以及如何由 sendNotification 函数以及直接使用外部类型的变量 ad 来执行 admin 类型实现的方法。这表明，如果外部类型实现了 notify 方法，内部类型的实现就不会被提升。

不过内部类型的值一直存在，因此还可以通过直接访问内部类型的值，来调用没有被提升的内部类型实现的方法。
```



# 三、数组

## 3.1、数组

### 3.1.1、概述

- 数组是一个由固定长度的特定类型元素组成的序列，一个数组可以由零个或多个元素组成。因为数组的长度是固定的，所以在[Go语言](http://c.biancheng.net/golang/)中很少直接使用数组。
- 和数组对应的类型是 Slice（切片），Slice 是可以增长和收缩的动态序列，功能也更灵活，但是想要理解 slice 工作原理的话需要先理解数组，所以本节主要为大家讲解数组的使用，至于 Slice（切片）将在《[Go语言切片](http://c.biancheng.net/view/27.html)》一节中为大家讲解。

### 3.1.2、数组的声明

- 数组的声明语法如下：

    ```
    var 数组变量名 [元素数量]Type
    ```

    - 数组变量名：数组声明及使用时的变量名。
    - 元素数量：数组的元素数量，可以是一个表达式，但最终通过编译期计算的结果必须是整型数值，元素数量不能含有到运行时才能确认大小的数值。
    - Type：可以是任意基本类型，包括数组本身，类型为数组本身时，可以实现多维数组。

- 数组的每个元素都可以通过索引下标来访问，索引下标的范围是从 0 开始到数组长度减 1 的位置，内置函数 `len()` 可以返回数组中元素的个数。

    ```go
    var a [3]int             // 定义三个整数的数组
    fmt.Println(a[0])        // 打印第一个元素
    fmt.Println(a[len(a)-1]) // 打印最后一个元素
    // 打印索引和元素
    for i, v := range a {
        fmt.Printf("%d %d\n", i, v)
    }
    // 仅打印元素
    for _, v := range a {
        fmt.Printf("%d\n", v)
    }
    ```

- 默认情况下，数组的每个元素都会被初始化为元素类型对应的零值，对于数字类型来说就是 0，同时也可以使用数组字面值语法，用一组值来初始化数组：

    ```go
    var q [3]int = [3]int{1, 2, 3}
    var r [3]int = [3]int{1, 2}
    fmt.Println(r[2]) // 0
    ```

- 在数组的定义中，如果在数组长度的位置出现“...”省略号，则表示数组的长度是根据初始化值的个数来计算，因此，上面数组 q 的定义可以简化为：

    ```go
    q := [...]int{1, 2, 3}
    fmt.Printf("%T\n", q) // "[3]int"
    ```

- 数组的长度是数组类型的一个组成部分，因此 `[3]int` 和 `[4]int` 是两种不同的数组类型，数组的长度必须是常量表达式，因为数组的长度需要 ***在编译阶段确定***。

    ```go
    q := [3]int{1, 2, 3}
    q = [4]int{1, 2, 3, 4} // 编译错误：无法将 [4]int 赋给 [3]int
    ```

### 3.1.3、比较两个数组是否相等

- 如果两个数组类型相同（包括数组的长度，数组中元素的类型）的情况下，我们可以直接通过比较运算符（`==` 和`!=`）来判断两个数组是否相等，只有当两个数组的所有元素都是相等的时候数组才是相等的，不能比较两个类型不同的数组，否则程序将无法完成编译。

    ```go
    a := [2]int{1, 2}
    b := [...]int{1, 2}
    c := [2]int{1, 3}
    fmt.Println(a == b, a == c, b == c) // "true false false"
    d := [3]int{1, 2}
    fmt.Println(a == d) // 编译错误：无法比较 [2]int == [3]int
    ```

### 3.1.4、遍历数组——访问每一个数组元素

- 遍历数组也和遍历切片类似，代码如下所示：

    ```go
    var team [3]string
    team[0] = "hammer"
    team[1] = "soldier"
    team[2] = "mum"
    for k, v := range team {
        fmt.Println(k, v)
    }
    
    // - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
    // 代码输出如下：
    // 0 hammer
    // 1 soldier
    // 2 mum
    // - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
    ```



## 3.2、多维数组

### 3.2.1、概述

- 在[Go语言](http://c.biancheng.net/golang/)中数组本身只有一个维度，不过可以通过组合多个数组来创建多维数组。多维数组很容易管理具有父子关系的数据或者与坐标系相关联的数据。

- 结合上一节《[Go语言数组](http://c.biancheng.net/view/26.html)》中所学到的知识，下面通过实例来演示一下如何声明 ***二维数组***。

    - 二维数组

    ```go
    // 声明一个二维整型数组，两个维度分别存储 4 个元素和 2 个元素
    var array [4][2]int
    // 使用数组字面量来声明并初始化一个二维整型数组
    array = [4][2]int{{10, 11}, {20, 21}, {30, 31}, {40, 41}}
    // 声明并初始化外层数组中索引为 1 个和 3 的元素
    array = [4][2]int{1: {20, 21}, 3: {40, 41}}
    // 声明并初始化外层数组和内层数组的单个元素
    array = [4][2]int{1: {0: 20}, 3: {1: 41}}
    ```

    - 

