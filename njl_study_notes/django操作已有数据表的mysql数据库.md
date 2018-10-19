1、在settings.py中配置对应的数据库

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'mvp',
        'USER': 'root',
        'PASSWORD': 'xxxxxx',
        'HOST': '127.0.0.1',  # mysql主服务器地址
        'PORT': '3306',
    }
}
```

2、针对已有数据库自动生成新的models 

```python
python manage.py inspectdb
```

3、导出模型到一个指定的文件，如models.py

```python
python manage.py inspectdb > models_mvp.py
```

4、默认配置下生成不可修改/删除的models，修改meta class中的managed＝True则告诉django可以对数据库进行操作 

```python
from django.db import models
# Create your models here.

class ImgInfo(models.Model):
    img_id = models.BigIntegerField(db_column='IMG_ID', primary_key=True)  # Field name made lowercase.
    img_name = models.CharField(db_column='IMG_NAME', max_length=128, blank=True, null=True)  # Field name made lowercase.
    img_type = models.IntegerField(db_column='IMG_TYPE', blank=True, null=True)  # Field name made lowercase.
    img_path = models.CharField(db_column='IMG_PATH', max_length=128, blank=True, null=True)  # Field name made lowercase.
    img_size = models.BigIntegerField(db_column='IMG_SIZE', blank=True, null=True)  # Field name made lowercase.
    width = models.IntegerField(db_column='WIDTH', blank=True, null=True)  # Field name made lowercase.
    height = models.IntegerField(db_column='HEIGHT', blank=True, null=True)  # Field name made lowercase.
    create_oper = models.BigIntegerField(db_column='CREATE_OPER', blank=True, null=True)  # Field name made lowercase.
    # 添加参数 auto_now_add=True 向数据库插入数据时，自动生成时间。
    create_time = models.DateTimeField(db_column='CREATE_TIME', blank=True, null=True,auto_now_add=True)  # Field name made lowercase.
    # bit类型对应的字段由 TextField 改成 BooleanField
    is_del = models.BooleanField(db_column='IS_DEL',default=1)  # Field name made lowercase. This field type is a guess.

    class Meta:
        managed = False
        db_table = 'IMG_INFO'
```

5、是不是真的可以操作原有数据库了呢？进行验证即可 。在视图中创建视图类代码。运行项目

```python
from django.shortcuts import render
from django.http import HttpResponse
from django.views.generic import View
from trains.files.models import ImgInfo  # trains.files.models前面不要添加mvp.


class Hello(View):

    def get(self, request):
        ImgInfo.objects.create(img_id=5,
                               img_name='2',
                               img_type=2,
                               img_path='/201806192',
                               img_size=256,
                               width=234,
                               height=456,
                               create_oper=211112222,
                               )
        return HttpResponse('hello world')
```

