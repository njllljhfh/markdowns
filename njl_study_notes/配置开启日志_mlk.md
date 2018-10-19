# 配置开启日志

官方文档：https://docs.djangoproject.com/en/2.0/

官方文档日志版块：https://docs.djangoproject.com/en/2.0/topics/logging/

日志功能，包含发送错误日志到指定邮箱

### 1、日志配置

官方文档 模块：https://docs.djangoproject.com/en/2.0/topics/logging/#configuring-logging

Django日志配置在项目的settings.py文件中，具体配置如下

```python
#导入模块
import logging
import django.utils.log
import logging.handlers

# 日志配置
LOGGING = {
    'version': 1,
    'disable_existing_loggers': True,	# 禁用django默认logging配置中的logger
    
    # 定义日志输出格式
    'formatters': {
        'detail': {
            'format': '%(asctime)s %(levelname)s %(module)s %(process)d %(thread)d %(pathname)s[line:%(lineno)d] %(message)s'
        },
        'standard': {
            'format': '%(asctime)s %(levelname)s %(message)s'
        },
    },
    
    #过滤器，handlers用不到就不要定义
    'filters': {
        #'require_debug_false': {
        #    '()': 'django.utils.log.RequireDebugFalse',
        #}
     },
    
    # 处理器
    'handlers': {
        # 控制台输出打印的日志
        'console': {
            'level': 'DEBUG',
            'class': 'logging.StreamHandler',
            'formatter': 'standard',
        },
        # 输出到本地文件，文件夹路径必须存在
        'project': {
            'level': 'INFO',	# 记录日志类型
            'class': 'logging.handlers.RotatingFileHandler',	# 导出到文件，需要使用的类
            'filename': os.path.join(BASE_DIR, "logs",'mvp.log'),	# 日志输出到文件
            'maxBytes': 1024 * 1024 * 10,  # 10 MB
            'backupCount': 10,	# 备份份数
            'formatter': 'detail',	# 使用哪种formatters日志格式
        },
     	
        'debug': {
            'level':'DEBUG',
            'class':'logging.handlers.RotatingFileHandler',
            'filename': os.path.join(BASE_DIR, "logs",'debug.log'),	# 日志输出文件
            'maxBytes':1024*1024*5,	# 文件大小 10MB
            'backupCount': 10,	# 备份份数
            'formatter':'detail',	# 使用哪种formatters日志格式
         },
    },
    
    # logging管理器，日志处理器
    'loggers': {
        # 使用console处理器，将信息输出。在开发的时候就用这个处理器
        'django': {
            'handlers': ['console'], # 列表中是，使用上面 'handlers'中定义的哪个处理器。
            'level': 'DEBUG',  # 控制 'handlers': ['console'],中记录的日志的等级
            'propagate': False,  # 用于控制是否将日志传递给父层级记录
        },
        # django的request发生error会自动记录
        'django.request': {
            'handlers': ['project'],
            'level': 'ERROR',  
            'propagate': True,
        },
        
    },
}
```





### 2、日志使用

官方文档 模块：https://docs.djangoproject.com/en/2.0/topics/logging/#using-logging

在对应视图中（view.py），使用日志功能

```python
logger = logging.getLogger('sourceDns.webdns.views')    # 刚才在setting.py中配置的logger

try:
    mysql= connectMysql('127.0.0.1', '3306', 'david')
except Exception,e:
            logger.error(e)        # 直接将错误写入到日志文件
```

最后，不要忘了给日志的路径响应的权限。比如Apache2服务器，就需要给www-data写权限：

```
sudo chown -R [yourname]:www-data [log]
sudo chmod -R g+s [log]
```

### 3、flask中日志的使用

```python
import logging
from logging.handlers import RotatingFileHandler

'''
开发中使用DEBUG级别, 来输出丰富的调试信息.
发布时使用WARN以上级别, 来显示异常信息
log文件存满, 会自动叠加序号, 并产生新的log文件. 如果文件存满了, 就覆盖原先的文件
'''
# 设置日志的记录等级
logging.basicConfig(level=logging.DEBUG)  # 调试debug级
# 创建日志记录器，指明日志保存的路径、每个日志文件的最大大小、保存的日志文件个数上限
file_log_handler = RotatingFileHandler("logs/log", maxBytes=1024 * 1024 * 100, backupCount=10)
# 创建日志记录的格式		日志等级    输入日志信息的文件名		行数    日志信息
formatter = logging.Formatter('%(levelname)s %(filename)s:%(lineno)d %(message)s')
# 为刚创建的日志记录器设置日志记录格式
file_log_handler.setFormatter(formatter)
# 为全局的日志工具对象（flask app使用的）添加日志记录器
logging.getLogger().addHandler(file_log_handler)

--------------------------------------------------------------
# 使用
import logging

logging.error(e)
app.logger.error() # logger模块默认已经集成到了app中, 但是没有智能提示不好用
current_app.logger.error(e)

```





参考资料：

1、《[Django日志配置](https://www.cnblogs.com/zhangqunshi/p/6641173.html)》 -- 博客园

2、《[[转\]django 日志logging的配置以及处理](https://www.cnblogs.com/luohengstudy/p/6890395.html)》 -- 《[django 日志logging的配置以及处理](http://blog.51cto.com/davidbj/1433741)》

