#### 1、filter 查询集，查询不到，返回一个  空的查询集   

```python
 <QuerySet []>
```



#### 2、dict 的 get( ) 方法

```python
#语法
get()方法语法：
dict.get(key, default=None)

#参数
key -- 字典中要查找的键。
default -- 如果指定键的值不存在时，返回该默认值值。

#返回值
返回指定键的值，如果值不在字典中返回默认值 None。
```



#### 3、django 无法识别mysql中的bit类型

```python
django 无法识别mysql中的bit类型，如它的注释：This field type is a guess。在将其更改为 models.BooleanField()后，在检索数据时，无法直接通过if self.auth_user_is_admin进行bool的判断， 无论数据库中auth_user_is_admin是0或是1，if判断均为true，通过添加if ord(self.auth_user_is_admin):来对值进行转换，一切正常了。
```

![](.\images\测试mysql数据库中读出的bit1类型-用ord方法转换成int类型.png)



#### 4、values_list('img_id', flat=True)，查询某个字段的列表

```python
# flat=True, 返回 例如如<QuerySet [1, 2, 3, ...]>。
# 如果没有这个参数，默认返回 例如 <QuerySet[(1,), (2,), (3,), ...]>

img_ids_set_src = TaskImgRel.objects.filter(task_id=data_dict.get("task_id")).values_list('img_id', flat=True)
```



#### 5、get请求  ，用  HttpResponse( ) 返回 图片二进制信息给前端

```python
class Imgs(View):
    """图像信息的 在 mongodb 中的 存取"""

    def get(self, request):
        # 从mongodb 中获取 图像 二进制 信息
        # resp 为图像的 二进制信息
        resp = get_img_data_bit(request)
        
        print('Imgs---get---resp--->')
        # return JsonResponse(resp)
        return HttpResponse(resp, content_type="image/png;image/jpeg;image/gif;image/tiff")
```



#### 6、关于用pymongo 用 .put方法向 GridFS 中插入图像数据后，返回的 mongo_id。以及用  .get方法 通过 mongo_id 查询 图像二进制信息的问题。

```python
import gridfs
from pymongo import MongoClient
from bson.objectid import ObjectId

# mongodb数据库 名字
db_name = "mvp_dl"
# 数据库中的 集合的 名字
collection_name = "IMG"

# 建立与mongodb服务器的链接
mongodb_server_client = MongoClient(mongodb_host, mongodb_port)

# 获取指定的数据库 对象
mongodb_db = mongodb_server_client[db_name]

# 获取 mongodb数据库中 指定 GridFS Buckets 对象
gridfs_obj = gridfs.GridFS(database=mongodb_db, collection=collection_name)

# 保存 一张 图像二进制数据 到mongodb数据库的 GridFS Buckets 中
# .put() 方法，只有 data 参数是必填的
# mongodb_id ---> ObjectId('5b3718fce636103bea8feb38')
mongodb_id = gridfs_obj.put(img_data, filename=img_name)

# mysql 数据库中 ImgInfo 中 mongo_id 保存的类型是 str类型
# str类型的mongo_id, 要用bson.objectid 中的 ObjectId 转换后，才能作为 .get()方法的 查询条件
# gridfs_obj.get(ObjectId(mongo_id)) 的返回类型是<class 'gridfs.grid_file.GridOut'>,是一个文件。
img_data_bit = gridfs_obj.get(ObjectId(mongo_id)).read()

```



#### 7、关于用base64编码 图片的二进制信息

```python
import base64
# json 不能传递 二进制 信息
# 将 图像 二进制信息 编码成 base64 格式
# base64.b64encode(img_data_bit)的返回值仍然是 <class 'bytes'>
# 需要 再一次 decode() ，转换为 str 类型，才能 在json中传递 
img_data_base64 = base64.b64encode(img_data_bit).decode("utf-8")
```



#### 8、关于numpy_array数组类型数据，如何保存到 mongo 中的 GridFS 中

[参考官方文档链接]: https://docs.python.org/3.6/library/pickle.html?highlight=dumps#pickle.dumps
[参考博客]: https://blog.csdn.net/qq_34877350/article/details/78614626

```python
import gridfs
import numpy as np
import pickle
from bson.binary import Binary
from bson import ObjectId

# 准备一个 numpy 数组
# type(my_nparray)---> <class 'numpy.ndarray'>
# my_nparray.shape---> (2, 3)
my_nparray = np.array([[1, 2, 3], [4, 5, 6]])

# 将 numpy数组 转化为 <class 'bson.binary.Binary'> 类型
# type(my_nparray_bin)---> <class 'bson.binary.Binary'>
# protocol = -1 ，表示用最高等级的 protocol，详情见官方文档
my_nparray_bin = Binary(pickle.dumps(my_nparray, protocol=-1))

# 将 <class 'bson.binary.Binary'> 类型的数据 保存到 mongo 中的 GridFS 中，返回mongo_id
img_name = '二维数组'
mongo_id = gridfs_obj.put(my_nparray_bin, filename=img_name)

# 从 mongodb 中读取 数组数据，得到的是 <class 'bytes'>
# type(my_nparray_bin_result)---> <class 'bytes'>
my_nparray_bin_result = gridfs_obj.get(mongo_id).read()

# 将  <class 'bytes'> 类型的 数组数据，转换回 numpy 数组类型 <class 'numpy.ndarray'>
# type(my_nparray_bin_result_to_nparray)---> <class 'numpy.ndarray'>
# my_nparray_bin_result_to_nparray.shape---> (2, 3)
my_nparray_bin_result_to_nparray = pickle.loads(my_nparray_bin_result)
        
```



#### 9、 用正则去掉 base64 编码后，得到的字符串的 前缀格式信息

```python
import re
# 去掉 img_data_b64 中的开头数据 'data:image/png;base64,'
img_data_b64_result = re.sub(r'^data:.*\,', '', img_data_b64, count=1)
```



#### 10、django，去重

```python
# 方法 distinct()，去重
email_list = Email.objects.values_list('email', flat=True).distinct()
```



#### 11、查询集  ，计算其中元素的个数

[参考官方文档]: https://docs.djangoproject.com/en/2.0/ref/models/querysets/#count

```
count()

Returns an integer representing the number of objects in the database matching the QuerySet. The count() method never raises exceptions.

A count() call performs a SELECT COUNT(*) behind the scenes, so you should always use count() rather than loading all of the record into Python objects and calling len() on the result (unless you need to load the objects into memory anyway, in which case len() will be faster).

Note that if you want the number of items in a QuerySet and are also retrieving model instances from it (for example, by iterating over it), it’s probably more efficient to use len(queryset) which won’t cause an extra database query like count() would.
```



#### 12、django中查询集的问题

```python
# django中用的filter查询集来查询一个对象时，当 用了.values_list("mongo_id", flat=True) 方法之后 如果filter获取的当对象存在，而 对象.属性名 不存在，则返回的查询集不是空，而是  <QuerySet [None]>
mongo_id = ImgInfo.objects.filter(img_id=img_id).values_list("mongo_id", flat=True)
```

![django中的查询集为空时，bool值 并不是 False 而是True](.\images\django中查询集为空时的判断问题.png)



#### 13、django在 url的路径中获取参数

```python
# url匹配语法
urlpatterns = [
    path('traintask/<int:task_id>', views.TrainTask.as_view()),  # 开始训练任务
]

# 错误码类型
如果访问的路径中 traintask/ 后面的 参数不能转换为int类型，或者没有参数。django会报 404 错误码，如下
2018-07-18 15:31:13,921  WARNING module:django.server [line:124] "GET /training/tasks/njlgetimg/asdfasdf HTTP/1.1" 404 3834
```



#### 14、 关于json和get请求中的参数类型

```python
1、前端传的json中，如果键对应的值是 int类型，那么python后台解析后，得到的就是 int类型。
	 {
        "task_id":"2018072049434026765",
        "training_count":1,
        "verifying_count":0,
        "testing_count":9
	}
```

```python
2、get请求的url中的int，后端获取后得到的是 str 类型
	192.168.186.128:8000/training/tasks/sets?task_id=2018072049434026765&set_type_id=0
    2018072049434026765 和 0 到python后台，解析后得到的是 str类型
```

![](.\images\关于json和get请求中的参数类型.png)



#### 15、django分组，聚合查询

注意：

- annotate（），分组；
- Count（），聚合；
- 分组annotate（）中的聚合字段，django会自动添加到 SQL的SELECT中。也会自动添加到values_list（）返回的查询集中的每一个元组中。



**TaskImgRel表**：

![TaskImgRel表](.\images\TaskImgRel.png)

<center>TaskImgRel表</center>
```python
from django.db.models import Count


# 获取 该任务下 图像的分组信息
set_img_count = TaskImgRel.objects.filter(task_id=task_id, is_del=Constants.IS_NOT_DEL) \
.values_list("set_type_id").annotate(set_img_count=Count("set_type_id")).order_by("set_type_id")

print("转换字典之前set_img_count= ", set_img_count)  <QuerySet [(0, 8), (2, 2)]>
print("转换字典之前type(set_img_count)= ", type(set_img_count))  
set_img_count_dict = dict(list(set_img_count))
print("转换字典之后set_img_count= ", set_img_count_dict)
print("转换字典之后type(set_img_count)= ", type(set_img_count_dict))  

# pycharm后台打印的数据
转换字典之前set_img_count=  <QuerySet [(0, 8), (2, 2)]> #元组，(set_type_id,Count("set_type_id"))
转换字典之前type(set_img_count)=  <class 'django.db.models.query.QuerySet'>
转换字典之后set_img_count=  {0: 8, 2: 2}
转换字典之后type(set_img_count)=  <class 'dict'>

# set_img_count 对应的SQL语句
print("set_img_count.query-->", set_img_count.query)
SELECT `TASK_IMG_REL`.`SET_TYPE_ID`, COUNT(`TASK_IMG_REL`.`SET_TYPE_ID`) AS `set_img_count` FROM `TASK_IMG_REL` WHERE (`TASK_IMG_REL`.`IS_DEL` = False AND `TASK_IMG_REL`.`TASK_ID` = 2018072122655062968) GROUP BY `TASK_IMG_REL`.`SET_TYPE_ID` ORDER BY `TASK_IMG_REL`.`SET_TYPE_ID` ASC

```



#### 15、关于django中，DateTimeField类型的字段，取出的值放到json中的问题

```python
要将从mysql中查出来的日期 强转换为 str，否则无法转换为json数据
mysql中默认的格式是："2018-07-21 15:23:45"
```



#### 16、request.GET.get("xxx")

```
url 中如果没有传 xxx这个参数，那么，request.GET.get("xxx") 的返回值为 None
```

