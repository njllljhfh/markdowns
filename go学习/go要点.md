# go要点



`iota` 也可以用在表达式中，如：`iota + 50`。在每遇到一个新的常量块或单个常量声明时， `iota` 都会重置为 0（ **简单地讲，每遇到一次 const 关键字，iota 就重置为 0** ）。



当一个变量被声明之后，系统自动赋予它该类型的零值：int 为 0，float 为 0.0，bool 为 false，string 为空字符串，指针为 nil。记住，所有的内存在 Go 中都是经过初始化的。



对于值类型的变量 `i` ，当使用等号 `=` 将一个变量的值赋值给另一个变量时，如：`j = i`，实际上是在内存中将 i 的值进行了拷贝。



对于引用类型的变量 `r1` ，当使用赋值语句 `r2 = r1` 时，只有引用（地址）被复制。



在 Go 语言中，**指针**属于引用类型，其它的引用类型还包括 **slices**，**maps** 和 **channel**。被引用的变量会存储在堆中，以便进行垃圾回收，且比栈拥有更大的内存空间。



初始化声明操作符 `:=` ，只能用在函数体内。



如果你声明了一个局部变量却没有在相同的代码块中使用它，会得到编译错误。但是全局变量是允许声明但不使用。



在 Go 语言中，`&&` 和 `||` 是具有快捷性质的运算符，当运算符左边表达式的值已经能够决定整个表达式的值的时候（`&&` 左边的值为 false，`||` 左边的值为 true），运算符右边的表达式将不会被执行。利用这个性质，如果你有多个条件判断，应当将计算过程较为复杂的表达式放在运算符的右侧以减少不必要的运算。



对于数字类型，可以通过增加前缀 0 来表示 8 进制数（如：077），增加前缀 0x 来表示 16 进制数（如：0xFF），以及使用 e 来表示 10 的连乘（如： 1e3 = 1000，或者 6.022e23 = 6.022 x 1e23）。



`byte` 类型是 `uint8` 的别名。

`rune` 也是 Go 当中的一个类型，并且是 `int32` 的别名。



如果传递给函数的是一个指针，指针的值（一个地址）会被复制，但指针的值所指向的地址上的值不会被复制；我们可以通过这个指针的值来修改这个值所指向的地址上的值。（**指针也是变量类型，有自己的地址和值，通常指针的值指向一个变量的地址。所以，按引用传递也是按值传递。**）



如果 s 是一个切片，s 的容量 `cap(s)` 就是从 `s[0]` 到数组末尾的数组长度。切片的长度永远不会超过它的容量，所以对于 切片 s 来说该不等式永远成立：`0 <= len(s) <= cap(s)`。



For-range 结构，这种构建方法可以应用于数组和切片:

```go
for ix, value := range slice1 {
	...
}
```

第一个返回值 ix 是数组或者切片的索引，第二个是在该索引位置的值；他们都是仅在 for 循环内部可见的局部变量。value 只是 slice1 某个索引位置的值的一个 **拷贝**，不能用来修改 slice1 该索引位置的值。



**注意** 绝对不要用指针指向 slice。切片本身已经是一个引用类型，所以它本身就是一个指针!!



- `new(T)` 为每个新的类型T分配一片内存，初始化为 0 并且返回类型为 `*T` 的内存地址：这种方法 **返回一个指向类型为 T，值为 0 的地址的指针**，它适用于值类型如数组和结构体（参见第 10 章）；它相当于 `&T{}`。
- `make(T)` **返回一个类型为 T 的初始值**，它只适用于3种内建的引用类型：切片、map 和 channel（参见第 8 章，第 13 章）。



*如何理解new、make、slice、map、channel的关系*

*1.slice、map以及channel都是golang内建的一种引用类型，三者在内存中存在多个组成部分， 需要对内存组成部分初始化后才能使用，而make就是对三者进行初始化的一种操作方式*

*2. new 获取的是存储指定变量内存地址的一个变量，对于变量内部结构并不会执行相应的初始化操作， 所以slice、map、channel需要make进行初始化并获取对应的内存地址，而非new简单的获取内存地址*



如果 s 是一个字符串（本质上是一个字节数组），那么就可以直接通过 `c := []byte(s)` 来获取一个字节的切片 c。



Go 语言中的字符串是不可变的，也就是说 `str[index]` 这样的表达式是不可以被放在等号左侧的。如果尝试运行 `str[i] = 'D'` 会得到错误：`cannot assign to str[i]`。



map 传递给函数的代价很小：在 32 位机器上占 4 个字节，64 位机器上占 8 个字节，无论实际上存储了多少数据。通过 key 在 map 中寻找值是很快的，比线性查找快得多，但是仍然比从数组和切片的索引中直接读取要慢 100 倍；所以如果你很在乎性能的话还是建议用切片来解决问题。



令 `v := map1[key1]` 可以将 key1 对应的值赋值给 v；如果 map 中没有 key1 存在，那么 v 将被赋值为 map1 的值类型的空值。



**不要使用 new，永远用 make 来构造 map**

**注意** 如果你错误的使用 new() 分配了一个引用对象，你会获得一个空引用的指针，相当于声明了一个未初始化的变量并且取了它的地址：

```go
mapCreated := new(map[string]float32)
// 接下来当我们调用：mapCreated["key1"] = 4.5 的时候，编译器会报错：
// invalid operation: cannot index mapCreated (variable of type *map[string]float32)
mapCreated["key1"] = 4.5
```



和数组不同，map 可以根据新增的 key-value 对动态的伸缩，因此它不存在固定长度或者最大限制。但是你也可以选择标明 map 的初始容量 `capacity`，就像这样：`make(map[keytype]valuetype, cap)`。例如：

```go
map2 := make(map[string]float32, 100)
```

当 map 增长到容量上限的时候，如果再增加新的 key-value 对，map 的大小会自动加 1。所以出于性能的考虑，对于大的 map 或者会快速扩张的 map，即使只是大概知道容量，也最好先标明。



从 map1 中删除 key1：直接 `delete(map1, key1)` 就可以。如果 key1 不存在，该操作不会产生错误。



注意 map 不是按照 key 的顺序排列的，也不是按照 value 的序排列的。



一个类型加上它的方法等价于面向对象中的一个类。一个重要的区别是：在 Go 中，类型的代码和绑定在它上面的方法的代码可以不放置在一起，它们可以存在在不同的源文件，唯一的要求是：它们必须是同一个包的。



别名类型没有原始类型上已经定义过的方法。



类型和作用在它上面定义的方法必须在同一个包里定义，这就是为什么不能在 int、float 或类似这些的类型上定义方法。试图在 int 类型上定义方法会得到一个编译错误：

```go
cannot define new methods on non-local type int
```



如果方法不需要使用 `recv` 的值，可以用 **_** 替换它，比如：

```
 复制代码func (_ receiver_type) methodName(parameter_list) (return_value_list) { ... }
```



在 Go 中，（接收者）类型关联的方法不写在类型结构里面，就像类那样；耦合更加宽松；类型和方法之间的关联由接收者来建立。



鉴于性能的原因，`recv` 最常见的是一个指向 receiver_type 的指针（因为我们不想要一个实例的拷贝，如果按值调用的话就会是这样），特别是在 receiver 类型是结构体时，就更是如此了。



在值和指针上调用方法：

- 可以有连接到类型的方法，也可以有连接到类型指针的方法。

- 但是这没关系：对于类型 T，如果在 *T 上存在方法 `Meth()`，并且 `t` 是这个类型的变量，那么 `t.Meth()` 会被自动转换为 `(&t).Meth()`。
- **指针方法和值方法都可以在指针或非指针上被调用**





不要在 `String()` 方法里面调用涉及 `String()` 方法的方法，它会导致意料之外的错误，比如下面的例子，它导致了一个无限递归调用（`TT.String()` 调用 `fmt.Sprintf`，而 `fmt.Sprintf` 又会反过来调用 `TT.String()`…），很快就会导致内存溢出：

```go
type TT float64
func (t TT) String() string {
    return fmt.Sprintf("%v", t)
}
t.String()
```





当函数的参数是接口类型时：

- 指针方法可以通过指针调用
- 值方法可以通过值调用
- 接收者是值的方法可以通过指针调用，因为指针会首先被解引用
- 接收者是指针的方法不可以通过值调用，因为存储在接口中的值没有地址

```go
package main

import (
    "fmt"
)

type List []int

func (l List) Len() int {
    return len(l)
}

func (l *List) Append(val int) {
    *l = append(*l, val)
}

type Appender interface {
    Append(int)
}

func CountInto(a Appender, start, end int) {
    for i := start; i <= end; i++ {
        a.Append(i)
    }
}

type Lener interface {
    Len() int
}

func LongEnough(l Lener) bool {
    return l.Len()*10 > 42
}

func main() {
    // A bare value
    var lst List
    // compiler error:
    // cannot use lst (type List) as type Appender in argument to CountInto:
    //       List does not implement Appender (Append method has pointer receiver)
    // CountInto(lst, 1, 10)
    if LongEnough(lst) { // VALID:Identical receiver type
        fmt.Printf("- lst is long enough\n")
    }
    // A pointer value
    plst := new(List)
    CountInto(plst, 1, 10) //VALID:Identical receiver type
    if LongEnough(plst) {
        // VALID: a *List can be dereferenced for the receiver
        fmt.Printf("- plst is long enough\n")
    }
}
```





协程可以运行在多个操作系统线程之间，也可以运行在线程之内，让你可以很小的内存占用就可以处理大量的任务。由于操作系统线程上的协程时间片，你可以使用少量的操作系统线程就能拥有任意多个提供服务的协程，而且 Go 运行时可以聪明的意识到哪些协程被阻塞了，暂时搁置它们并处理其他协程。



协程是独立的处理单元，一旦陆续启动一些协程，你无法确定他们是什么时候真正开始执行的。你的代码逻辑必须独立于协程调用的顺序。



同一个操作符 `<-` 既用于**发送**也用于**接收**，但 Go 会根据操作对象弄明白该干什么 。虽非强制要求，但为了可读性通道的命名通常以 `ch` 开头或者包含 `chan`。通道的发送和接收都是**原子操作**：它们总是互不干扰的完成的。



对于带缓冲的通道，在缓冲满载（缓冲被全部使用）之前，给一个带缓冲的通道发送数据是不会阻塞的，而从通道读取数据也不会阻塞，直到缓冲空了。



内置的 `cap` 函数可以返回缓冲区的容量。如果容量大于 0，通道就是异步的了：缓冲满载（发送）或变空（接收）之前通信不会阻塞，元素会按照发送的顺序被接收。如果容量是0或者未设置，通信仅在收发双方准备好的情况下才可以成功。



在设计算法时首先考虑使用无缓冲通道，只在不确定的情况下使用缓冲。



通道可以被显式的关闭；尽管它们和文件不同：不必每次都关闭。只有在当需要告诉接收者不会再提供新的值的时候，才需要关闭通道。只有发送者需要关闭通道，接收者永远不会需要。



