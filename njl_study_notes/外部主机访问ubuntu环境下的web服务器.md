1、点击pycharm右上角的运行按钮 右边的配置

![](.\images\配置外部主机访问ubuntu虚拟机中运行的web_1.png)

2、然后设置HOST主机 ip 地址为   0.0.0.0，保存

![](.\images\配置外部主机访问ubuntu虚拟机中运行的web_2.png)

3、设置settings.py文件中       ALLOWED_HOSTS = ['*']

![](.\images\配置外部主机访问ubuntu虚拟机中运行的web_3.png)

4、虚拟机 网络配置 为NET，貌似桥接模式也是可以的

![](.\images\配置外部主机访问ubuntu虚拟机中运行的web_4.png)

5、在主机外部访问 虚拟机ip：虚拟机中web的端口号   

例如   http://192.168.186.128:8000/training/tasks/tasks/

![](.\images\配置外部主机访问ubuntu虚拟机中运行的web_5.png)