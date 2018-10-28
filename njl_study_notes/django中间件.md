# 细说Django的中间件

2018年04月18日 17:07:35

 

菲宇

 

阅读数：128

更多

个人分类： [Django](https://blog.csdn.net/bbwangj/article/category/7589692)



版权声明：欢迎交流，菲宇运维！	https://blog.csdn.net/bbwangj/article/details/79993437

分析Django的生命周期,我们知道所有的http请求都要经过Django的中间件.

假如现在有一个需求,所有到达服务端的url请求都在系统中记录一条日志,该怎么做呢?

**Django的中间件的简介**

Django的中间件类似于linux中的管道符

Django的中间件实质就是一个类,类之中有Django已经定义好了一些方法.

每个http请求都会执行中间件中的一个或多个方法

```python
进入Django中的请求都会执行process_request方法;
Django返回的信息都会执行process_response方法;
```

Django内部的中间件注册在`settings.py`文件

```python
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
]
```

导入`from django.middleware.csrf import CsrfViewMiddleware`模块,查看其源码

```python
class CsrfViewMiddleware(MiddlewareMixin)
```

可以看到`CsrfViewMiddleware`是继承`MiddlewareMixin`这个中间件的

再查看`MiddlewareMixin`的源码

```python
    class MiddlewareMixin(object):

        def __init__(self, get_response=None):
            self.get_response = get_response
            super(MiddlewareMixin, self).__init__()

        def __call__(self, request):
            response = None
			if hasattr(self, 'process_request'):
                response = self.process_request(request)

            if not response:
                response = self.get_response(request)

            if hasattr(self, 'process_response'):
                response = self.process_response(request, response)

            return response
```

可以知道`MiddlewareMixin`是调用了其内部的`__call__`方法,`__call__`方法以反射的方式执行`process_request`和`process_response`方法.

**中间件的应用**

新建一个middleware项目,在项目的根目录中新建一个名为`middleware`的包,在这个package中,新建一个`middleware.py`文件.

```
    from django.utils.deprecation import MiddlewareMixin



    



    class middle_ware1(MiddlewareMixin):



    



        def process_request(self,request):



            print("middle_ware1.process_request")



    



        def process_response(self,request,response):



            print("middle_ware1.process_response")



            return response
```

在`settings.py`配置文件的第一行注册这个中间件

```
MIDDLEWARE = [



    'middleware.middleware.middle_ware1'



    'django.middleware.security.SecurityMiddleware',



    'django.contrib.sessions.middleware.SessionMiddleware',



    'django.middleware.common.CommonMiddleware',



    'django.middleware.csrf.CsrfViewMiddleware',



    'django.contrib.auth.middleware.AuthenticationMiddleware',



    'django.contrib.messages.middleware.MessageMiddleware',



    'django.middleware.clickjacking.XFrameOptionsMiddleware',



]
```

现在如果有http请求到达服务端,先执行中间件`middle_ware1`的`process_request`方法,再执行后面的中间件,然后到达视图函数.

定义一个视图函数index,配置好路由映射表

```
def index(request):



 



    print("index page")



    return HttpResponse("index")
```

启动程序,在浏览器中输入`http://127.0.0.1:8000/index`,会在服务端的后台打印:

```
middle_ware1.process_request



index page



middle_ware1.process_response
```

前端的页面为显示为:

```
index
```

通过这个我们知道,所有的请求都会经过`middle_ware1`这个中间件,先执行`process_request`方法,再执行视图函数本身

最后执行`process_response`方法,Django会把`process_response`方法的返回值返回给客户端.

现在再加一个定义一个`middle_ware2`的中间件

```
    from django.utils.deprecation import MiddlewareMixin



    



    class middle_ware2(MiddlewareMixin):



    



        def process_request(self,request):



            print("middle_ware2.process_request")



    



        def process_response(self,request,response):



            print("middle_ware2.process_response")



            return response
```

在`settings.py`配置文件中注册中间件

```
    MIDDLEWARE = [



        'middleware.middleware.middle_ware1',



        'middleware.middleware.middle_ware2',



        'django.middleware.security.SecurityMiddleware',



        'django.contrib.sessions.middleware.SessionMiddleware',



        'django.middleware.common.CommonMiddleware',



        'django.middleware.csrf.CsrfViewMiddleware',



        'django.contrib.auth.middleware.AuthenticationMiddleware',



        'django.contrib.messages.middleware.MessageMiddleware',



        'django.middleware.clickjacking.XFrameOptionsMiddleware',



    ]
```

重启程序,再次刷新网页,服务端打印信息

```
middle_ware1.process_request



middle_ware2.process_request



index page



middle_ware2.process_response



middle_ware1.process_response
```

中间件的依次执行为:

```
先执行middle_ware1的process_request方法,



再执行middle_ware2的类的process_request方法,



最后才执行Django的视图函数.



返回时,先执行middle_ware2的类中的process_response函数,



然后再执行middle_ware1的类中的process_response函数.
```

上面的例子中,`process_response`方法设置了return值.假如`process_response`没有return值,会怎么样呢??

把`middle_ware1`中间件的`process_response`方法中的return注释掉,再次刷新网页,在浏览器的网页上显示报错信息,如下:

```
A server error occurred.  Please contact the administrator.
```

http请求到达Django后,先经过自定义的中间件`middle_ware1`和`middle_ware2`,再经过Django内部定义的中间件到达视图函数

视图函数执行完成后,一定会返回一个`HttpResponse`或者`render`.

通过查看`render`的源码可知,`render`方法本质上也是调用了`HttpResponse`

```
    def render(request, template_name, context=None, content_type=None, status=None, using=None):



    



        content = loader.render_to_string(template_name, context, request, using=using)



        return HttpResponse(content, content_type, status)
```

视图函数的返回值`HttpResponse`先经过Django内部定义的中间件,再经过用户定义的中间件,最后返回给前端网页.

所以用户定义的中间件的`process_response`方法必须设定返回值,否则程序会报错.

同时,`process_response`方法中的形参`response`就是视图函数的返回值.
那么我们是否可以自己定义这个形参的返回值呢??

用户发过来的请求信息是固定的,因为所有的请求信息和返回信息都要经过中间件,中间件有可能会修改返回给用户的信息
,所以有可能会出现用户收到的返回值与视图函数的返回值不一样的情况.

例如,返回给用户的信息包含响应头和响应体,但是开发者在视图函数中没有设置响应头,所以Django会在返回的信息中自动加上响应头.

前面,`process_response`方法设置了返回值,`process_request`中没有设置返回值,如果为`process_response`设置一个返回值,结果会怎么样呢??

为中间件`middle_ware1`的`process_request`方法设置返回值

```
return HttpResponse("request message")
```

刷新浏览器,服务端打印信息:

```
middle_ware1.process_request



middle_ware1.process_response
```

客户端浏览器显示信息为:

```
request message
```

先执行`process_request`方法,因为`process_request`方法有返回值,程序不会再执行后面的中间件,而是直接执行`process_response`方法,然后返回信息给用户.

在实际应用中,`process_request`方法不能直接设定返回值.如果必须在设定返回值,必须在返回值前加入条件判断语句.

常用在设定网站的黑名单的时候可以为`process_request`方法设置返回值.

**基于中间件实现的简单用户登录验证**

比如,在一个博客系统中,所有的后台管理页面都必须有用户登录后才能打开,可以基于中间件来实现用户登录验证

定义中间件

```
    from django.utils.deprecation import MiddlewareMixin



    from django.shortcuts import HttpResponse,redirect



    



    class middle_ware1(MiddlewareMixin):



    



        def process_request(self,request):



            if request.path_info == "/login/":



                return None



    



            if not request.session.get("user_info"):



                return redirect("/login/")



    



        def process_response(self,request,response):



            print("middle_ware1.process_response")



            return response
```

在`settings.py`文件中注册中间件

```
    MIDDLEWARE = [



        'django.middleware.security.SecurityMiddleware',



        'django.contrib.sessions.middleware.SessionMiddleware',



        'django.middleware.common.CommonMiddleware',



        'django.middleware.csrf.CsrfViewMiddleware',



        'django.contrib.auth.middleware.AuthenticationMiddleware',



        'django.contrib.messages.middleware.MessageMiddleware',



        'django.middleware.clickjacking.XFrameOptionsMiddleware',



        'middleware.middleware.middle_ware1',



    ]
```

用户请求index网页,程序执行到`process_request`,会执行if not语句,然后重定向到`login`页面,使用户输入用户名和密码登录进入后台管理页面.

**Django中间件常用方法**

除了上面已经用过的`process_request`方法和`porcess_response`方法,Django的中间件还有以下几种方法.

**process_view方法**

定义两个中间件如下

```
    from django.utils.deprecation import MiddlewareMixin



    from django.shortcuts import HttpResponse,redirect



    



    class middle_ware1(MiddlewareMixin):



    



        def process_request(self,request):



            print("middle_ware1.process_request")



    



        def process_view(selfs,request,view_func,view_func_args,view_func_kwargs):



            print("middle_ware1.process_view")



    



        def process_response(self,request,response):



            print("middle_ware1.process_response")



            return response



    



    class middle_ware2(MiddlewareMixin):



    



        def process_request(self,request):



            print("middle_ware2.process_request")



    



        def process_view(self,request,view_func,view_func_args,view_func_kwargs):



            print("middle_ware2.process_view")



    



        def process_response(self,request,response):



            print("middle_ware2.process_response")



            return response
```

在`settings.py`中的注册中间件

```
MIDDLEWARE = [



    'django.middleware.security.SecurityMiddleware',



    'django.contrib.sessions.middleware.SessionMiddleware',



    'django.middleware.common.CommonMiddleware',



    'django.middleware.csrf.CsrfViewMiddleware',



    'django.contrib.auth.middleware.AuthenticationMiddleware',



    'django.contrib.messages.middleware.MessageMiddleware',



    'django.middleware.clickjacking.XFrameOptionsMiddleware',



    'middleware.middleware.middle_ware1',



    'middleware.middleware.middle_ware2',



]
```

视图函数

```
    def index(request):



    



        print("index page")



        return HttpResponse("index")
```

在浏览器中输入`http://127.0.0.1:8000/index`页面,在服务端打印信息

```
middle_ware1.process_request



middle_ware2.process_request



middle_ware1.process_view



middle_ware2.process_view



index page



middle_ware2.process_response



middle_ware1.process_response
```

在前端页面显示信息:

```
index
```

程序执行流程:

先执行所有中间件中的`process_request`方法-->进行路由匹配-->执行中间件中的的`process_view`方法

-->执行对应的视图函数-->执行中间件中的`process_response`方法

上面的例子里,`process_view`方法里没有设置return值.为`process_view`方法都设定return值,程序又会怎么执行呢??

为`process_view`方法设定返回值

```
    from django.utils.deprecation import MiddlewareMixin



    from django.shortcuts import HttpResponse,redirect



    



    class middle_ware1(MiddlewareMixin):



    



        def process_request(self,request):



            print("middle_ware1.process_request")



    



        def process_view(selfs,request,view_func,view_func_args,view_func_kwargs):



            return HttpResponse("middle_ware1.process_view")



    



        def process_response(self,request,response):



            print("middle_ware1.process_response")



            return response



    



    class middle_ware2(MiddlewareMixin):



    



        def process_request(self,request):



            print("middle_ware2.process_request")



    



        def process_view(self,request,view_func,view_func_args,view_func_kwargs):



            return HttpResponse("middle_ware2.process_view")



    



        def process_response(self,request,response):



            print("middle_ware2.process_response")
```

刷新浏览器,在服务端打印信息如下:

```
middle_ware1.process_request



middle_ware2.process_request



middle_ware2.process_response



middle_ware1.process_response
```

在客户端的浏览器页面显示信息如下:

```
iddle_ware1.process_view
```

可以看到视图函数`index`并没有执行,程序执行两个中间件的`process_request`方法后,其次执行`process_view`方法.

由于`process_view`方法设置了return值,所以程序中的视图函数并没有执行,而是执行了中间件中的`process_response`方法.

**process_exception方法**

修改中间件

```
    from django.utils.deprecation import MiddlewareMixin



    from django.shortcuts import HttpResponse,redirect



    



    class middle_ware1(MiddlewareMixin):



    



        def process_request(self,request):



            print("middle_ware1.process_request")



    



        def process_view(selfs,request,view_func,view_func_args,view_func_kwargs):



            print("middle_ware1.process_view")



    



        def process_exception(self,request,exvception):



            print("middleware1.process_exception")



    



        def process_response(self,request,response):



            print("middle_ware1.process_response")



            return response



    



    class middle_ware2(MiddlewareMixin):



    



        def process_request(self,request):



            print("middle_ware2.process_request")



    



        def process_view(self,request,view_func,view_func_args,view_func_kwargs):



            print("middle_ware2.process_view")



    



        def process_exception(self,request,exception):



            print("middleware2.process_exception")



    



        def process_response(self,request,response):



            print("middle_ware2.process_response")



            return response
```

刷新浏览器,服务端打印信息

```
middle_ware1.process_request



middle_ware2.process_request



middle_ware1.process_view



middle_ware2.process_view



hello,index page



middle_ware2.process_response



middle_ware1.process_response
```

根据服务端的打印信息可以看到,`process_exception`方法没有执行,为什么呢??

这是因为上面的代码没有bug.当代码运行错误,出现报错信息的时候,`process_exception`才会执行

那现在就模拟让程序出现错误,观察`process_exception`方法的执行情况

修改视图函数

```
    def index(request):



    



        print("index page")



        int("aaaa")



        return HttpResponse("index")
```

刷新浏览器,服务端后台打印信息

```
middle_ware1.process_request



middle_ware2.process_request



middle_ware1.process_view



middle_ware2.process_view



index page



middleware2.process_exception



middleware1.process_exception



Internal Server Error: /index



Traceback (most recent call last):



......



ValueError: invalid literal for int() with base 10: 'aaaa'



middle_ware2.process_response



middle_ware1.process_response
```

由服务端的报错信息可知,这次`process_exception`方法果然执行了.

由此我们知道,程序运行错误,中间件中的`process_exception`方法才会执行,而程序正常运行的时候,这个方法则不会执行

刚才的代码里,`process_exception`方法没有设置返回值,如果为`process_exception`方法设置返回值,程序的执行流程会是怎么的呢???

修改中间件,为两个中间件的`process_exception`方法都设置返回值

```
    from django.utils.deprecation import MiddlewareMixin



    from django.shortcuts import HttpResponse,redirect



    



    class middle_ware1(MiddlewareMixin):



    



        def process_request(self,request):



            print("middle_ware1.process_request")



    



        def process_view(selfs,request,view_func,view_func_args,view_func_kwargs):



            print("middle_ware1.process_view")



    



        def process_exception(self,request,exvception):



            print("middleware1.process_exception")



            return HttpResponse("bug1")



    



        def process_response(self,request,response):



            print("middle_ware1.process_response")



            return response



    



    class middle_ware2(MiddlewareMixin):



    



        def process_request(self,request):



            print("middle_ware2.process_request")



    



        def process_view(self,request,view_func,view_func_args,view_func_kwargs):



            print("middle_ware2.process_view")



    



        def process_exception(self,request,exception):



            print("middleware2.process_exception")



            return HttpResponse("bug2")



    



        def process_response(self,request,response):



            print("middle_ware2.process_response")



            return response
```

视图函数

```
    def index(request):



    



        print("index page")



        int("aaaa")



        return HttpResponse("index")
```

刷新页面,服务端打印信息

```
middle_ware1.process_request



middle_ware2.process_request



middle_ware1.process_view



middle_ware2.process_view



index page



middleware2.process_exception



middle_ware2.process_response



middle_ware1.process_response
```

而在浏览器的页面则显信息

```
bug2
```

程序的执行流程为:

客户端发出的http请求到达中间件,执行中间件中的`process_request`方法,然后再执行路由匹配,然后执行`process_view`方法,

然后执行相应的视图函数,最后执行`process_response`方法,返回信息给客户端浏览器.

如果执行视图函数时出现运行错误,中间件中的`process_exception`方法捕捉到异常就会执行,后续的`process_exception`方法就不会再执行了.

`process_exception`方法执行完毕,最后再执行所有的`process_response`方法.

**process_template_response方法**

process_template_response方法默认也不会被执行,

修改中间件

```
    from django.utils.deprecation import MiddlewareMixin



    from django.shortcuts import HttpResponse,redirect



    



    class middle_ware1(MiddlewareMixin):



    



        def process_request(self,request):



            print("middle_ware1.process_request")



    



        def process_view(selfs,request,view_func,view_func_args,view_func_kwargs):



            print("middle_ware1.process_view")



    



        def process_exception(self,request,exvception):



            print("middle_ware1.process_exception")



            return HttpResponse("bug1")



    



        def process_response(self,request,response):



            print("middle_ware1.process_response")



            return response



    



        def process_template_response(self,request,response):



            print("middle_ware1.process_template_response")



            return response



    



    class middle_ware2(MiddlewareMixin):



    



        def process_request(self,request):



            print("middle_ware2.process_request")



    



        def process_view(self,request,view_func,view_func_args,view_func_kwargs):



            print("middle_ware2.process_view")



    



        def process_exception(self,request,exception):



            print("middleware2.process_exception")



            return HttpResponse("bug2")



    



        def process_response(self,request,response):



            print("middle_ware2.process_response")



            return response



    



        def process_template_response(self,request,response):



            print("middle_ware2.process_template_response")



            return response
```

修改视图函数,使视图函数正常执行

```
    def index(request):



    



        print("index page")



        return HttpResponse("index")
```

刷新浏览器,服务端后台打印信息如下:

```
middle_ware1.process_request



middle_ware2.process_request



middle_ware1.process_view



middle_ware2.process_view



index page



middle_ware2.process_response



middle_ware1.process_response
```

可以看到,程序运行正常,`process_template_response`方法没有执行.

事实上,`process_template_response`方法的执行取决于视图函数的返回的信息,

视图函数如果使用render方法返回信息,中间件里的`process_template_response`方法就会被执行.

修改视图函数,定义一个类,返回其参数response

```
    class MyResponse(object):



        def __init__(self,response):



            self.response=response



    



        def render(self):



            return self.response



    



    def index(request):



    



        response=HttpResponse("index page")



        return MyResponse(response)
```

MyResponse类返回的是自定义的对象,这个对象里边调用了`render`方法.

index视图函数里,先调用了`HttpResponse`方法返回信息,再使用`MyResponse`类中的`render`方法来返回`HttpResponse`.

执行程序,服务端后台打印信息如下:

```
middle_ware1.process_request



middle_ware2.process_request



middle_ware1.process_view



middle_ware2.process_view



middle_ware2.process_template_response



middle_ware1.process_template_response



middle_ware2.process_response



middle_ware1.process_response
```

可以看到,`process_template_response`方法已经执行.

`process_template_response`的用处

```
如果要对返回的HttpResponse数据进行处理,可以把要处理的信息封装在一个类里



只要类里定义了render方法,process_template_response方法才会执行.
```

**总结**

- 中间件里本质上就是一个类,
- 对全局的http请求做处理的时候可以使用中间件
- 中间件中的方法不一定要全部使用,需要哪个用哪个
- process_request方法不能有return,一定要使用return的时候,要配合条件判断语句执行
- process_response方法一定要有return,否则程序会运行错误
- process_view方法不能有return,否则视图函数不会执行
- process_exception方法只有在程序出现运行错误的时候才会执行
- process_exception方法设定return时,程序不会再执行后续中间件中的process_exception
- process_template_response方法只有在视图函数中使用render方法返回信息的时候才会执行

