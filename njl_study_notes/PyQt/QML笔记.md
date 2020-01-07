# QML笔记



# QML Object Attributes

> QML对象属性

## id属性

id设置以后，不可以在代码中动态修改

id必须以小写字母或者下划线开头

```python
import QtQuick 2.0

Column {
    width: 200; height: 200
    TextInput { id: myTextInput; text: "Hello World" }
    Text { text: myTextInput.text }
}
```

  上面的代码中，通过  `myTextInput.id` 不可以获取myTextInput的id



# Property Attributes

可以设置静态属性，也可以绑定动态表达式。

可以被其他对象读取。

### 定义属性

自定义的属性名必须以小写字母开头，并且只能包含字母、数字、下划线，  [JavaScript](https://developer.mozilla.org/en/JavaScript/Reference/Reserved_Words) 的保留字，不能作为自定义的属性名字。

自定义属性（如：nextColor），会默认有一个 **value-change 信号**。和处理该信号的handler的名字， *on“自定义属性名”Changed* （如：onNextColorChanged）

```python
Rectangle {
    property color previousColor
    property color nextColor
    onNextColorChanged: console.log("The next color will be: " + nextColor.toString())
}
```

