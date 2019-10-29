# beego笔记

> **author: 牛金龙**



# 一、安装

## 1.1、安装beego和bee工具

go get github.com/astaxie/beego

go get github.com/beego/bee

## 1.2、设置环境变量

- 在终端输入`vim ~/.bash_profile`，在文件最下面添加如下变量

```
# go config
export GOPATH=${HOME}/go
export PATH=${PATH}:${GOPATH}/bin
```





# 二、

## 2.1、新建项目

1. cd到GOPATH\src目录，在终端输入：`${GOPATH}/src`
2. 新建beegoDemo1项目，在终端输入：`bee new beegoDemo1`

## 2.2、启动项目

1. cd到目录，在终端输入：`${GOPATH}/src/beegoDemo1`
2. 启动项项目（热编译），在终端输入：`bee run`





# 三、