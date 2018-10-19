## 一、向DL层发起训练请求

###1.1、OpenCV以及DL层的相关参数和格式

```python
class TaskProcess(object):
    def __init__(self):
        pass

    def task_start_request(self, param_request, task_id, task_type, img_mongodb_ids, json_mongodb_id):
        '''
        服务请求开始
        :param param_request:    深度学习的一些参数，结构为ParamClass
        :param task_id:          任务ID号 ,类型为uint64
        :param task_type:        用来区分训练和测试,类型为TaskTypeClass
        :param  img_mongodb_ids: 图像mongodbid的列表, 类型为list
        :param  json_mongodb_id : json 的Imongodb ID号，类型为string
        :return:错误码 0表示正常
        '''
        

# 用于任务请求的一些参数的封装类
class ParamClass(object):
    def __init__(self, model_name='', dataset_expansion_lst=[],
                 net_model='MobileNet', model_scope=ModelScopeClass.NORMAL,
                 device_type=DeviceClass.GPU, inital_learning_rate=0.001,
                 device_num=1, max_num_iters=50000,
                 batch_size=10, snap_shot_ivterval=10000):
        self.model_name = model_name  # 模型名字，如：ssd_detect_model   <type:str>
        self.dataset_expansion_lst = dataset_expansion_lst  # 这里是一个数据集的扩充操作的列表，如：[HOR_FLIPE, VER_FLIPE]。列表中的元素类型 <type:ExpansionDataClass>
        self.net_model = net_model  # 网络模型，用网络名称区分， 默认为MobileNet   <type:str>
        self.model_scope = model_scope  # 网络规模， 默认是NORMAL     <type:ModelScopeClass>
        self.device_type = device_type  # 设备类型， 默认是GPU.      <type:DeviceClass>
        self.inital_learning_rate = inital_learning_rate  # 初始学习率,默认值为0.001  <type:double>
        self.device_num = device_num  # 设备数量，默认值为1.   <type:uint16>
        self.max_num_iters = max_num_iters  # 最大迭代次数，默认值为50000.  <type:uint64>
        self.batch_size = batch_size  # 每一次放入迭代的图片数量， 默认值为10.  <type:uint32>
        self.snap_shot_ivterval = snap_shot_ivterval  # 模型保存间隙.  <type:uint64>


# 向dl发起训练请求，dl返回的状态码
@unique  # 用来表示数据扩充的方式
class TaskErrorCode(Enum):
    OK = 0  # 返回OK
    TASK_TYPE_ERROR = 1  # task_type error
    JASON_MONGOID_INVALID = 2  # json_id在数据库中无效
    IMAG_MONGOID_INVALID = 3  # img_id在数据库中无效
    TASK_ID_INVALID = 4  # 任务id无效
    COLLECTIONS_EMPTY = 5  # 数据库集合为空
    TASK_TEST_NOT_OVER = 6  # 测试任务还没有结束，结果还没出来
   


```



### 1.2、组织DL请求报文

1. 先组织 "dataset_info "数据集信息

2. 同步组织 "image_info "和 "instance_info "的数据信息

   - 外层for循环遍历 图像信息表<IMG_INFO>的对象，内层for循环遍历 图像标注记录表<IMG_LAB_INFO>的对象。

   - 外层for循环组织json报文中 "image_info " 字段的数据信息，内层for循环组织json报文中 "instance_info " 字段的数据信息

3. 最后组织 "class_info " 的数据信息

4. 

5. 

6. 

7. 

8. 

   

   

   

### 1.3、注意事项

DL接口表只发送训练或者测试请求的时候进行记录，"end_time " 是接口执行结束的时间。

​    