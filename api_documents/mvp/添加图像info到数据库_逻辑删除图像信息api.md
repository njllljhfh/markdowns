# 上传图片，插入图像信息到mysql数据库表中。逻辑删除图像信息相关表中的数据。

[接口url]: 192.168.186.128:8000/training/files/files



## 一、上传图片，插入图像信息到 ImgInfo、TaskImgRel。post 请求

### 1、 前端发来的数据(全部)

```json
{
	"task_id":"2018062212345678911",
	"img_info":[
                    {
                        "img_name":"兰博基尼",
                        "img_type":1, 
                        "img_size":1111,
                        "width":1111,
                        "height":1111, 
                        "create_oper":"2018062310000000001"
                    },
                    {
                        "img_name":"布加迪",
                        "img_type":2,
                        "img_size":2222,
                        "width":2222,
                        "height":2222,
                        "create_oper":"2018062310000000001"
                    }
			]
}
```



### 2、插入ImgInfo表

#### 2.1、前端传来的数据---直接用于插入ImgInfo表

```python
{
    "img_name":"兰博基尼", # 图像名字  ---varchar
    "img_type":1,  # 图像类型  ---tinyint
    "img_size":3542,	# 图像大小 ---bigint
    "width":1234, # 图像宽度  ---int
    "height":5785678,  # 图像高度  ---int
    "create_oper":2018062212345678911, # 操作人ID  ---bigint
}
```



#### 2.2、逻辑处理后插入的，数据库自动生成的，以及有默认值的   ImgInfo表的 数据

```python
# 逻辑处理后插入 ImgInfo 表的数据
img_id = 2018062210000000001 # 图像id  ---bigint
img_path = /home/njl/Documents/tasks # 图像名字  ---varchar

# 有默认值的数据
is_del = 0 # 是否删除 ---bit

# 数据库自动生成数据
create_time # 创建时间  ---datetime
```

#### 2.3、图像类型

```python
来源于字典：
{
    "png":1,
    "jpeg":2,
    "bmp":3
}
```



### 3、插入TaskImgRel表

#### 3.1、前端传来的数据---直接用于插入TaskImgRel表

```python
{
    "task_id":2018062212543546841, # 图片所属任务的id  ---bigint  
}
```

#### 3.2、逻辑处理后插入的，数据库自动生成的，以及有默认值的   TaskImgRel表的 数据

```python
# 逻辑处理后插入 ImgInfo 表的数据
img_id = 2018062210000000001 # 图像id  ---bigint

# 有默认值的数据
is_del = 0 # 是否删除 ---bit

# 数据库自动生成数据
order_id # 自增长列 ---int

# 后续要更新的数据
set_type_id = 1 # 图像所属集合类型，默认设置为1，训练集  ---tinyint
img_state = 0	# 图像状态，默认为0，图像uploading中 ---tinyint
img_roi_id  # ROI图像编号  ---bigint
img_mask_id  # MASK编号  ---bigint
img_dl_id  # DL图像编号  ---bigint
```

#### 3.3、图像集合类型

```python
#来源于字典：
set_type_id = {
    "训练":1,
    "验证":2,
    "测试":3
}
```
#### 3.4、图像状态

```python
img_state = {
	"upload":0,  # 正在上传中
	"ready":1,  # 加载进入系统，但未进行任何操作
	"标注":2,  # 正在或已被作为标注对象执行标注操作
	"训练":3,  # 正在或已经进行深度学习训练操作
	"测试":4,  # 正在或已经进行任务算法测试操作
	"验证":5  # 正在或已经进行算法模型验证操作
}
```

### 4、返回给前端的数据

```json
{
    "code": 0,
    "msg": "插入图像信息到 ImgInfo、TaskImgRel 表，成功",
    "img_datas": {
        "img_拓海_1.png": "2018071616546329161",
        "img_1.jpg": "2018071663877548491",
        "img_2.jpg": "2018071673993352262",
        "img_3.jpg": "2018071691969131677",
        "img_4.jpg": "2018071667575709300",
        "img_5.jpg": "2018071693403032677",
        "img_6.jpg": "2018071625881577287",
        "img_7.jpg": "2018071658388849030",
        "img_8.jpg": "2018071643244463062",
        "img_9.jpg": "2018071620535377497"
    }
}
```

### 5、注意：保存图片文件的，服务器端 硬盘 中 的文件夹是在 这个逻辑中 ，新建的

```
在 files/services.py 中的 organize_img_objs_list（）方法中
```



## 二、对图像信息（ImgInfo、TaskImgRel表）进行逻辑删除，put 请求

#### 1、 前端发来的数据(全部)

```json
{
	"task_id":2018062267186412222,
	"img_id_list":[2018062560794247009,2018062589388426113]
}
```

#### 2、返回给前端的数据

```json
{
    "code": 0,
    "msg": "ImgInfo 和 TaskImgRel表数据更新, 成功"
}
```

