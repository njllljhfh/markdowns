- 镜像导入、导出

```
导出镜像
语法：docker save -o [导出后的名字] [要导出的镜像名]
例子：docker save -o nginx.tar sswang-nginx

导入镜像
语法：docker load < [image.tar_name]
例子：docker load < nginx.tar
```

