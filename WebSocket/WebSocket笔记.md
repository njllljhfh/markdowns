# 一、

self.scope 中的数据格式

```python
scope = {'type': 'websocket',
         'path': '/ws/chat/lobby/',
         'headers': [(b'host', b'127.0.0.1:8000'),
                     (b'connection', b'Upgrade'),
                     (b'pragma', b'no-cache'),
                     (b'cache-control', b'no-cache'),
                     (b'upgrade', b'websocket'),
                     (b'origin', b'http://127.0.0.1:8000'),
                     (b'sec-websocket-version', b'13'),
                     (b'user-agent', b'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_3) 							  AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 								  Safari/537.36'),
                     (b'accept-encoding', b'gzip, deflate, br'),
                     (b'accept-language', b'zh-CN,zh;q=0.9'),
                     (b'sec-websocket-key', b'x1MhwSWFf+N+mu7VXv9/Lg=='),
                     (b'sec-websocket-extensions', b'permessage-deflate;       									  client_max_window_bits')],
         'query_string': b'',
         'client': ['127.0.0.1', 50696],
         'server': ['127.0.0.1', 8000],
         'subprotocols': [],
         'cookies': {},
         'session': '<django.utils.functional.LazyObject object at 0x1112b56d8>',
         'user': '<channels.auth.UserLazyObject object at 0x1112b5710>',
         'path_remaining': '',
         'url_route': {'args': (), 'kwargs': {'room_name': 'lobby'}}
         }
```

