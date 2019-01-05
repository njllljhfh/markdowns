# 向DL层发起开始训练请求（分割任务）



[URL]: 192.168.186.128:8000/training/trains/traintask/2018072067241762338	"最后的数字是task_id"



## 一、前端向后端发起开始训练请求，POST 请求

### 1、前端上传的数据

```json
{
    "param_class":{
                    "model_name":"李尓缺陷检测模型",  // 模型名字，如：ssd_detect_model   <type: str>
                    "augmentation_lst":[0,1,2,3,4,5],  // 这里是一个数据集的扩充操作的列表
                    "model_scope":1,  // 网络规模， 默认是NORMAL     <type: ModelScopeClass>
                    "device_type":1,  // 设备类型， 默认是GPU.      <type: DeviceClass>
                    "device_num":2,  // 设备数量，默认值为1.   <type: uint16>
                    "lr":0.0001,  // 初始学习率,默认值为0.001  <type: double>
                    "save_interval":1000, // 模型保存频率 new
                    "max_to_keep":2,  // 模型保存个数
                    "max_iters":8000,  // 最大迭代次数，默认值为50000.  <type: uint64>
				 },
    "algorithm_name":"Fast R_CNN", // 算法的名称,前端选的  <type: str>
    "algorithm_type":1,  //算法的类型（AlgorithmType类型）
    "algorithm_param":{
        			"img_size":[672,448,896],  // 单张图片采样边张尺寸(DeeplabV3+)
        			"anchors":[4,8,16,32],  // 允许范围(Fast R_CNN)
        			"ratio":[0.25,1,3,6],  // 设置anchor纵横比(Fast R_CNN)
        			"scales":300  // 短边归一化到的像素值(Fast R_CNN)
    				}
}
```





### 2、枚举数字对应的，枚举类中的字段的        含义

```python
enum AugmentationClass
{
    FLIP             = 0;
    ROTATE           = 1;
    CROP             = 2;
    CONTRAST         = 3;
    GAMMA			 = 4；
    GAUSSNOISE		 = 5；
}

enum ModelScopeClass
{
    SMALL  = 0;					#小型规模
    NORMAL = 1;					#普通规模
    BIG    = 2;					#大型规模
}

enum DeviceClass
{
    CPU = 0;                    #使用CPU
    GPU = 1;					#使用GPU
}

enum TaskTypeClass
{
    TRAIN = 0;					 #训练类型
    TEST  = 1;                   #测试类型
}

enum AlgorithmType
{
		CLASSFICATION   =0;       #分类
		DETECTION 		=1;		  #检测
		DETECTION_R		=2;		  #斜框检测
		SEGMENTATION    =3        #分割
}

enum AugmentationClass
{
		FLIP             = 0		#翻转
		ROTATE           = 1		#旋转
		CROP             = 2		#截取
		CONTRAST         = 3		#对比度
		GAMMA     	     = 4		#伽马矫正
		GAUSSNOISE       = 5		#高斯噪音
}
```

### 3、字典表，id号 对应的  参数类型

```python
# "task_type_id"
	1: "classification"  # 分类
	2: "segmentation" 	 # 分割
	3: "detection"  	 # 检测
```



### 4、返回给前端的数据

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





## 注意点

1. 当发起训练任务的请求时，DL层需要 两个大json报文。一个是训练集(train)的json报文，一个是验证集(validation)的json报文(当验证集validation中的图像数个数是0的时候，不传递该集合的大json报文)
2. 训练任务结束后，DL层不对图像进行标注。
3. 测试任务结束后，DL层会返回DL层对图像的标注，要将dl层的标注信息添加到 用户标注的原始SVG报文中，并保存到 <IMG_LAB_INFO> 表中。



## 问题讨论

20181105

1、有模型名字

2、anchors：多选

3、ratio：多选

4、scales：单选  int 有范围

​	

20181107

1.父类没有属性，

2.按照父类进行训练的时候，父类去找子类的的属性

3.标注的时候都是用子类标注