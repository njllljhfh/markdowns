# 图像的ROI区域处理接口，POST请求

[url]: 192.168.186.128:8000/training/tasks/imgroi



## 一、逻辑

1. 前端传递 任务id，原图像id，roi区域的 [x,y,w,h]
2. 后端根据roi区域的位置和宽高裁剪图片
3. 将图片插入mongodb
4. 将图片插入mysql
5. 返回roi图像的id
6. ***前端更新该图片的id为roi后的新的图片id***



## 二、前端上传的数据，POST请求

```json
{
	"task_id":"2018062267186412370",  # 任务id
	"img_name":"img_拓海.jpg",  # 图像名字
	"img_id":"2018062499592025368",  # 原图像在 mysql 中的 id
    "roi_region":[50,80,200,400],
}
```



### 三、返回给前端的数据

```json
# 成功，返回的数据
{
	"code": 0,  # 状态码
	"msg": "roi图像 二进制信息 保存到mongo服务器,并更新mysql数据库 成功",  # 状态描述信息
	"roi_img_id": "2018070454850220082",  # roi图像在mysql中的id
	"state": "已经上传"  # 图像状态
}  

# 失败，返回的数据
{
    "code": e.errcode,  # 状态码
    "msg": e.errmsg  # 状态描述信息
}
```