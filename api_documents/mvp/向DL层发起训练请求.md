# 向DL层发起开始训练请求（与前端交互）



[URL]: 192.168.186.128:8000/training/trains/traintask/2018072067241762338	"最后的数字是task_id"



## 一、前端向后端发起开始训练请求，POST 请求

### 1、前端上传的数据

```json
{
    "model_name":"李尓缺陷检测模型",  # 模型名字，如：ssd_detect_model   <type: str>
    "dataset_expansion_lst":[0,1,2,3],  # 这里是一个数据集的扩充操作的列表（对应前端在点击开始训练窗口中的，最上面的4个复选框），如：[HOR_FLIPE,VER_FLIPE]。列表中的元素类型 <type: ExpansionDataClass>
    "net_model":"MobileNet",  # 网络模型，用网络名称区分， 默认为MobileNet   <type: str>
    "model_scope":1,  # 网络规模， 默认是NORMAL     <type: ModelScopeClass>
    "device_type":1,  # 设备类型， 默认是GPU.      <type: DeviceClass>
    "inital_learning_rate":0.0001,  # 初始学习率,默认值为0.001  <type: double>
    "device_num":2,  # 设备数量，默认值为1.   <type: uint16>
    "max_num_iters":8000,  # 最大迭代次数，默认值为50000.  <type: uint64>
    "batch_size":20,  # 每一次放入迭代的图片数量， 默认值为10.  <type: uint32>
    "snap_shot_ivterval":10000,  # 模型保存间隙.  <type: uint64>
    
    
    # 下面这些 字段 （不需要了）
    #"task_type_id":1,  # 任务类型id（创建任务时选择的类型，分割、分类、检测）.   <type: int>
    #"task_name":"Lear_defect",  # 任务名称，对应大json报文中的 "dataset_name"   <type: str>
    #"total_image_num":100,  # 该任务下图像的总数量
    #"set_img_num":{                # 图像的三个分组中的图像个数
    #				"train":80,  #训练集 图像个数
    #				"test":0,  #测试集 图像个数
    #				"validation":20  #验证集 图像个数
	#			}
}
```

### 2、注意点

1. 当发起训练任务的请求时，DL层需要 两个大json报文。一个是训练集(train)的json报文，一个是验证集(validation)的json报文(当验证集validation中的图像数个数是0的时候，不传递该集合的大json报文)
2. 训练任务结束后，DL层不对图像进行标注。
3. 测试任务结束后，DL层会返回DL层对图像的标注，要将dl层的标注信息添加到 用户标注的原始SVG报文中，并保存到 <IMG_LAB_INFO> 表中。

### 3、枚举数字对应的，枚举类中的字段的        含义

```python
# "dataset_expansion_lst"	一个数据集的扩充操作的列表		对应的类----> <ExpansionDataClass>
    HOR_FLIPE = 0  # 水平翻转
    VER_FLIPE = 1  # 垂直翻转
    ROTATE_90 = 2  # 90度旋转
    VAGUE = 3  # 模糊

# "model_scope"				网络规模		对应的类----> <ModelScopeClass>
    SMALL = 0  # 小型规模
    NORMAL = 1  # 普通规模
    BIG = 2  # 大型规模

# "device_type"				设备类型		对应的类----> <DeviceClass>
    CPU = 0  # 使用CPU
    GPU = 1  # 使用GPU
```

### 4、字典表，id号 对应的  参数类型

```python
# "task_type_id"
	1: "classification"  # 分类
	2: "segmentation"  # 分割
	3: "detection"  # 检测
```



### 5、返回给前端的数据

```json
# 成功，返回的数据
{
    "code": 0,
    "msg": "向DL层发起训练请求,成功",
    "dl_model_id_list": [1,2,3]
}

# 失败，返回的数据
{
    "code": 错误码,
    "msg": "错误信息"
}
```