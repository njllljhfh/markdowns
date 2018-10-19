# 1、opencv接口表：

**谁**在什么**时候**，**做什么**样事情，怎么做的，**结果**是怎么样，关系人是谁，**是否成功**（状态），resquest/reponse报文是否有效；起始时间，加上错误代码

任务：和opencv商定一下错误的代码

一张图片标注以后，保存图片标注信息，

​	说明：任务失败以后，可能会设置重复请求的次数（比如重新执行3次）

我的任务：与小坤沟通，确定opencv接口可能出现的errorcode

```python
# 例如方案
0---成功
1~5 请求参数异常
6~  响应错误
```



### 方案

```python
import time
print(time.strftime("%Y%m%d-%H-%M-%S", time.localtime()))

data_info = {
    'user_id':201806190000000001, # 操作人
    'create_tiem':"%Y%m%d-%H-%M-%S", # 操作时间
    'oper_type':  # 操作类型
    'status':状态码, # 操作执行结果，成功还是失败。如果失败，对应的状态码是什么。
}

status_code = {
    'success':0,  # 成功
    'error1':1,  # 错误类型1
    'error2':2,  # 错误类型2
}

oper_type_code = {
    'type1':0001, # 操作类型1
    'type2':0002, # 操作类型2
}

```

