# Django2.0 配置类视图 的 URL

项目的路径

![](C:\workfile\markdowns\njl_study_notes\images\mvikit项目初始路径.png)

###### 1、在settings中，将apps文件夹添加到 *系统导包路径*

```python
# 把 apps 添加到 导包路径
import sys
sys.path.insert(1, os.path.join(BASE_DIR, 'apps'))
```



###### 2、在与项目根目录同名的文件夹下的urls（mvikit/mvikit/urls）中，添加应用labels的urls。

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('labels/', include('labels.urls')),  # 添加应用labels文件夹中的urls
]
```



###### 3、在文件 apps/labels/views.py 中添加 类视图函数

```python
from django.shortcuts import render
from django.http import HttpResponse
from django.views.generic import View

# Create your views here.

class Hello(View):

    def get(self, request):
        return HttpResponse('hello world')
```



###### 4、在文件 apps/labels/urls.py 中添加 视图文件（views.py）中的类视图

```python
from django.urls import path
from labels import views

urlpatterns = [
    path('hello/', views.Hello.as_view()),  # 添加views.py中的类视图
]
```

