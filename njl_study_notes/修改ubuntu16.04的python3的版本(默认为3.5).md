修改默认的版本 并不是删除你不需要的版本，因为系统的许多底层是依赖python的，删除后可能会导致系统无法正常运行。 



接着需要做的是，删除/usr/bin目录下的python3 link文件 

```
sudo rm -rf /usr/bin/python3 
```



删除后再建立新的链接关系： 

```
sudo ln -s /usr/bin/python3.6  /usr/bin/python3
```



