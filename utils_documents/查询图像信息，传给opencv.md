##1、mlk 调用的接口，获取OpenCV的输入参数



##2、与mlk确认，调用我的接口，你需要的是 索引id，还是具体信息

输入参数：

```python
# 任务id
task_id

# 要保存的json数据
json
```

返回参数：

```python
# njl从 mysql中的 TaskImgRel表中读取 task_id 
task_id
# 图像的id 图像的mongo_id 图像的json_mongoid 信息，这三个要一一对应
img_ids
img_mongo_ids

# json的数据已经保存在了mysql里。现在的逻辑是，mlk从mysql的表中把json取出来；然后调用njl的json_mongo存储接口，把json数据存到mongo中。
json_mongo_id  

```

