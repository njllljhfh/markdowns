

# 官网教程

> [官方文档](https://golang.google.cn/doc/)



go特性：

- the convenience of garbage collection 

- the power of run-time reflection

- It's a fast, statically typed, compiled language that feels like a dynamically typed, interpreted language.



## 安装

mac系统，使用.pkg安装包安装go：[安装](https://golang.google.cn/doc/install)

- 安装完成后，go的安装位置：/usr/local/go
- 安装完成后，go的环境变量位置：/etc/paths.d/go

- 卸载go时，删除上述两个文件。[mac系统卸载go](https://golang.google.cn/doc/manage-install#uninstalling)



---



## Tutorial: Get started with Go

[教程地址](https://golang.google.cn/doc/tutorial/getting-started)

```
生成.mod文件
go mod init example.com/hello

mod file stays with your code
```



---



## Tutorial: Create a Go module

[教程地址](https://golang.google.cn/doc/tutorial/create-module)

Go代码被分组到包（packages）中，包被分组到模块（modules）中。

在Go中，名称以***大写字母***开头的函数可以被不在同一包（package）中的函数调用。



```
将没有发布的包（example.com/greetings）替换为本地路径（../greetings）
go mod edit -replace=example.com/greetings=../greetings
```



### 查看 `go install` 命令安装目录

```shell
# 查看 go install 命令，会将 编译好的 hello 应用安装到哪个目录
# 我的mac电脑的目录是：/Users/njl/go/bin/hello
# 注意: 执行给命令时，我在的路径为/Users/njl/Documents/GitHub/my_golang/Create_a_Go_module/hello
#      该路径下有 hello.go、go.mod 文件。
go list -f '{{.Target}}'
```



---



# 问题解决

## 本地获取go官方的模块

举例获取 https://github.com/golang/tour 模块

1. 且换到go的GOPATH路径下的src目录

    ```shell
    cd ${GOPATH}/src
    ```

2. 建立文件夹 `golang.org/x/`，注意现在是在 `${GOPATH}/src` 目录下

    ```shell
    mkdir -vp golang.org/x/
    ```

3. 切换到 `${GOPATH}/src/golang.org/x/` 从 `github` 克隆 `tour` 模块

    ```shell
    git clone git@github.com:golang/tour.git
    # 执行上述命令后，会有 ${GOPATH}/src/golang.org/x/tour 文件夹，tour中的代码就是该包的实现代码
    ```

4. 执行如下命令，使第三方包可以被go程序找到

    ```shell
    go env -w GO111MODULE=auto 
    
    # 执行上述命令，解决报错：
    # no required module provides package : go.mod file not found in current directory or any parent directory；
    ```

5. 然后在 `.go` 文件中就可以导入该模块的包了

    ```go
    // 例如导入该模块中的tree包
    import "golang.org/x/tour/tree"
    ```

    

---



## 关于golang第三方模块的引用报错（no required module provides package）：

运行 `.go` 文件时，报错如下：

```shell
no required module provides package : go.mod file not found in current directory or any parent directory;
```

解决方法，在命令行执行下面的命令：

```shell
go env -w GO111MODULE=auto
```



---



