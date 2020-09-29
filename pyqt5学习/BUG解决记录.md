# BUG记录

> 作者：牛金龙



### QComboBox的下拉选项显示问题：

- 问题描述
  - QComboBox的下拉选项，选择最后一个选项并保存（如新任务窗口），当再次打开该窗口（如修改任务窗口）时，QComboBox下拉菜单中的item的高度变小，导致item显示不全。
  - 该问题只出现在个别的电脑上，原因不明

- 解决方法

  - 用代理，给QComboBox的itme设置样式

  - pyqt代码

    ```python
    itemDelegate = QStyledItemDelegate()  # 代理设置下拉框item的高度
    # ComboBox
    self.task_oper_type_combobox = QtWidgets.QComboBox(parent)
    self.task_oper_type_combobox.setObjectName("task_oper_type_combobox")
    self.task_oper_type_combobox.setMaximumSize(QtCore.QSize(16777215, 20))
    self.task_oper_type_combobox.setEnabled(True)
    self.task_oper_type_combobox.setMaxVisibleItems(max_visible_item_count)
    self.task_oper_type_combobox.setItemDelegate(itemDelegate)
    ```

  - qss代码

    ```css
    /********** 下拉后，整个下拉窗体样式 **********/
    QComboBox QAbstractItemView {
        background-color: rgb(54, 54, 54);
        color: rgb(255, 255, 255);
    }
    
    QComboBox QAbstractItemView::item {
        height: 20px;
    }
    
    /* 下拉后，整个下拉窗体被选择的每项的样式 */
    QComboBox QAbstractItemView::item:selected {
        color: rgb(255, 255, 255);
        background-color: rgb(0, 120, 215);
    }
    ```



---



