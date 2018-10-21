### 问题描述：在代码中用logger记录日志的时候报错如下

--- Logging error ---
Traceback (most recent call last):
  File "/Users/njl/anaconda3/lib/python3.6/logging/__init__.py", line 994, in emit
​    stream.write(msg)
UnicodeEncodeError: 'ascii' codec can't encode characters in position 77-85: ordinal not in range(128)



### 解决办法：

[参考博客1]: https://www.jianshu.com/p/0cc567ec3432
[参考博客2]: http://www.voidcn.com/article/p-mqlyuxvt-d.html



 在handlers中，相应handler字典中，加入'encoding': 'utf8'  字段，  解决mac下写入日志到文件中，报编码错误的问题

```python
import logging.config

LOGGING_CONFIG = None

# 定义变量

# debug级别打印出来的太多了, INFO级别会把请求打印出来
LOGLEVEL = os.environ.get('LOGLEVEL', 'info').upper()

# if DEBUG:
#     # make all loggers use the console.
#     for logger in LOGGING['loggers']:
#         LOGGING['loggers'][logger]['handlers'] = ['console']

logging.config.dictConfig({
    'version': 1,
    'disable_existing_loggers': True,
    'formatters': {
        'console': {
            # exact format is not important, this is the minimum information
            'format': '%(asctime)s  %(levelname)-6s module:%(name)s [line:%(lineno)d] %(message)s',
        },
        'detail': {
            'format': '%(asctime)s  %(levelname)-6s module:%(name)s [line:%(lineno)d] %(message)s'
        },
    },
    'handlers': {
        'console': {
            'class': 'logging.StreamHandler',
            'formatter': 'console',
        },
        'project': {
            'level': 'DEBUG',  # 记录日志类型
            'class': 'logging.handlers.RotatingFileHandler',  # 导出到文件，需要使用的类
            'filename': os.path.join(BASE_DIR, "logs", 'mvp.log'),  # 日志输出到文件
            'maxBytes': 1024 * 1024 * 10,  # 10 MB
            'backupCount': 10,  # 备份份数
            'encoding': 'utf8', # 解决mac下写入日志到文件中，报编码错误的问题
            'formatter': 'detail',  # 使用哪种formatters日志格式
        },
        # Add Handler for Sentry for `warning` and above
        # 'sentry': {
        #     'level': 'WARNING',
        #     'class': 'raven.contrib.django.raven_compat.handlers.SentryHandler',
        # },
    },
    'loggers': {
        '': {
            'level': LOGLEVEL,
            'handlers': ['console', 'project'],
        },
        'project': {
            'level': LOGLEVEL,
            'handlers': ['console', 'project'],
            # required to avoid double logging with root logger
            'propagate': False,
        },
        # 降噪，用于一些不关心的模块日志记录
        'noisy_module': {
            'level': 'ERROR',
            # Only log in the console
            'handlers': ['console'],
            # Don't send this module's logs to Sentry
            'propagate': False,
        },
    },
})
```

