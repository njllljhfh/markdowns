# 新建任务 & 删除任务

新建训练任务，可选类型：分割、分类、检测。不同类型的任务类对应不同配置文件

第一步 插入 用户任务信息表

[接口url]: 192.168.186.128:8000/training/tasks/task

## 一、新建任务，post,插入 用户任务信息表UserTask 

### 1、前端传入的数据

```python
# 前端传过来的数据
{
    "task_name":"任务001",
    "task_type_id":1,  # 任务类型
    # creare_oper：1001,# 用户ID，待定
    # org_id：2002, # 用户所属组织机构，待定
    # group_id：3003, # 用户所属组，待定
    # origin_task_id： 2018062010000000001,# 源任务id，默认是null,待定
}
```

### 2、插入数据到 UserTask 表

#### 2.1、前端传来的数据，直接插入UserTask表

```json
{
    "task_name":"任务001",
    "task_type_id":1
}
```

#### 2.2、逻辑处理后插入的，数据库自动生成的，以及有默认值的    数据

```python
# 逻辑处理后，插入UserTask表中
task_id = 2018062010000000001
create_date = 20180620  # YYYYMMDD，后期做索引  int

# 有默认值的字段
is_del = 0
state = 0

# 数据库自动生成
create_time
update_time
```

#### 2.3、任务类型

```python
# 任务类型
tasks_type = {
    '分类':1，
    '分割':2，
    '检测':3,
}
```


#### 2.4、错误码

```python
# 错误码
class ErrCode(object):
    date_miss = 1
```

### 3 返回给前端的数据

```json
# 成功，返回
{
    "code": 0,
    "task_id": "2018062010000000001"
}
# 失败返回
{
    "code": 错误码,
    "msg": 错误信息
}
```

## 二、删除任务，更新 UserTask & TaskImgRel表 

### 1、前端传入的数据

```python
# 前端传过来的数据
{
    task_id:"2018062010000000001"
}
```

### 2、返回给前端的数据

```json
# 成功返回的数据
{
    "code": 0,
    "msg": "删除任务成功"
}

# 失败返回的数据
{
    "code": 任务删除失败提示码,
    "msg": "删除任务失败的原因"
}
```

