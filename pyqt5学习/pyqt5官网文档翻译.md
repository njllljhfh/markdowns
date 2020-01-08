

# pyqt5官网翻译

> 官网地址：
[pyqt5官网]: https://www.riverbankcomputing.com/static/Docs/PyQt5/index.html#	"2020-01-07"





# 支持信号与槽

## .1、概述

​		对象之间通过信号和槽来进行通信是Qt的关键特性之一，信号和槽的使用支持了可重用组件的开发。

​		当可能感兴趣的事件发生时，就会发出信号。槽是Python可调用的函数。如果一个信号连接到一个槽，那么当信号发出时，这个槽就会被调用；如果一个信号没有连接到一个槽，那么当信号发出时，什么也不会发生。



信号/槽机制具有以下特点：

- 一个信号可以连接多个槽
- 一个信号可以连接另一个信号
- 信号的参数可以是任何 ***Python*** 类型
- 一个槽可以被连接到多个信号
- 信号与槽的连接方式可以是直接的（同步连接），也可是排队的（异步的）
- 信号与槽的连接可能会跨线程
- 信号可能断开连接



## .2、解绑和绑定信号

​		信号(特别是未绑定的信号)是一个类属性。当一个信号作为类的一个实例的属性被引用时，PyQt5会自动将这个实例绑定到这个信号，以创建一个绑定信号。这与Python本身用于从类函数创建绑定方法的机制相同。

​		绑定信号具有实现相关功能的 `connect()`、`disconnect()` 和 `emit()` 方法。它还有一个 `signal` 属性，它是Qt的 `SIGNAL()` 宏返回的信号的签名。

​		信号可能重载。具有特定名称的信号可以支持多个签名。为了选择所需的信号，可以使用签名对信号进行索引。签名是类型的序列。类型可以是Python类型对象，也可以是c++类型名称的字符串。c++类型的名称被自动规范化，例如，`QVariant` 可以用来代替非规范化的 `const QVariant &` 。

​		如果一个信号被重载，那么它将有一个默认值，如果没有给定索引，默认值将被使用。

​		当一个信号被发出时，如果可能，任何参数都被转换成c++类型。如果一个参数没有相应的c++类型，那么它被包装在一个特殊的c++类型中，允许它在Qt的meta-type系统中传递，同时确保它的引用计数被正确维护。



## .3、用pyqtSignal定义新的信号

pass



## .4、连接信号、断开连接信号、发出信号

### .4.1、使用绑定信号的connect()方法将信号连接到槽

```python
connect(
slot[, type=PyQt5.QtCore.Qt.AutoConnection[,no_receiver_check=False]]
) → PyQt5.QtCore.QMetaObject.Connection
```

​		将信号连接到槽。如果连接失败，将引发异常。

### .4.2、使用绑定信号的disconnect()方法将信号从槽断开

```python
disconnect([slot])

	断开信号的一个或多个槽。如果槽没有连接到信号，或者信号根本没有连接到任何槽，则会引发异常。
    【参数：slot】 - 要断开连接的可选槽，可以是 connect() 返回的连接对象、一个 Python 可调用对象或是另一个绑定信号。如果省略它，那么连接到信号的所有槽都断开连接。
```

### .4.3、使用绑定信号的emit()方法发出信号

```python
emit(*args)

	发射一个信号。
	【参数：args】 - 可选的参数序列，该参数序列将会传递到任何已连接的槽上。
```



下面的代码演示了无参数信号的定义、连接和发射:

```python
from PyQt5.QtCore import QObject, pyqtSignal

class Foo(QObject):

    # Define a new signal called 'trigger' that has no arguments.
    trigger = pyqtSignal()

    def connect_and_emit_trigger(self):
        # Connect the trigger signal to a slot.
        self.trigger.connect(self.handle_trigger)

        # Emit the signal.
        self.trigger.emit()

    def handle_trigger(self):
        # Show that the slot has been called.

        print "trigger signal received"
```



下面的代码演示了重载信号的连接:

```python
from PyQt5.QtWidgets import QComboBox

class Bar(QComboBox):

    def connect_activated(self):
        # The PyQt5 documentation will define what the default overload is.
        # In this case it is the overload with the single integer argument.
        self.activated.connect(self.handle_int)

        # For non-default overloads we have to specify which we want to
        # connect.  In this case the one with the single string argument.
        # (Note that we could also explicitly specify the default if we
        # wanted to.)
        self.activated[str].connect(self.handle_string)

    def handle_int(self, index):
        print "activated signal passed integer", index

    def handle_string(self, text):
        print "activated signal passed QString", text
```