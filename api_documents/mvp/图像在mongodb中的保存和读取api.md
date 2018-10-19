# 图像在mongodb中的 保存 和 读取（与前端交互）

[URL]: 192.168.186.128:8000/training/tasks/imgs



## 一、保存从前端传来的图片信息到mongodb，POST 请求

### 1、前端上传的数据

```json
{
	"task_id":"2018062267186412370",  # 任务id
	"img_name":"img_拓海.jpg",  # 图像名字
	"img_id":"2018062499592025368",  # 图像在 mysql 中的 id
	"img_data":"/9j/4AAQSkZJRgABAQAAAQABAAD/2wBDABQODxIPDRQSEBI=="  # 图像的base64数据
}
```

### 2、返回给前端的数据

```json
# 成功，返回的数据
{
	"code": 0,  # 状态码
	"msg": "图像 二进制信息 保存到mongo服务器,并更新mysql数据库 成功",  # 状态描述信息
	"img_id": "2018070454850220082",  # 图像在mysql中的id
	"mongo_id": "5b3c4368e6361037ac7429e6",  # 图像在mongo中的id
	"state": "已经上传"  # 图像状态
}  

# 失败，返回的数据
{
    "code": e.errcode,  # 状态码
    "msg": e.errmsg  # 状态描述信息
}
```





## 二、从mongo中获取图像二进制数据，GET 请求

[原图ulr]: 192.168.186.128:8000/training/tasks/imgs?img_id=2018070454850220082&img_scale=normal

[缩小图url]: 192.168.186.128:8000/training/tasks/imgs?img_id=2018082996151711264&img_scale=small



### 1、前端上传的数据

```python
# url中的参数
img_id = 2018070454850220082
img_scale = normal  # 正常尺寸
img_scale = small  # 小尺寸
```



### 2、返回给前端的数据

```json
# 成功，返回给前端的数据
img_data_byte # 直接返回 图像的二进制数据

# 失败，返回给前端的数据
{
    "code": e.errcode,  # 状态码
    "msg": e.errmsg  # 状态描述信息
}
```

