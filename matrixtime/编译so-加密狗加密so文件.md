# 编译及加密





## 一、在ubuntu1804上将py文件编译为so文件

### 1.1 编译

```shell
# 切换到python3.8虚拟环境（公司服务器上是 conda 默认的 base 环境）
conda activate base

# 执行 编译脚本（在没有编译过的项目根目录下运行， compile.py是自己编写的编译脚本）
python compile.py build_ext --inplace
```





## 二、在ubuntu1804上对so文件加密

### 2.1 加密过程

- ubuntu驱动包解压

  ```shell
  # cd 到 驱动压缩包’Sentinel-LDK 8.4 for linux.tar.gz‘保存的目录
  # 解压
  tar -zxvf 'Sentinel-LDK 8.4 for linux.tar.gz'
  ```
- 将 windows 上的文件 `C:\此电脑\文档\Thales\Sentinel LDK 8.4\VendorCodes\TEOGQ.hvc` 复制到 ubuntu 的目录 `/your/path/to/Sentinel-LDK/VendorTools/Envelope` 中。

  - 注：`TEOGQ.hvc`  文件的名称是跟主锁一一对应的，不同主锁，该文件名称不同（类似于唯一id）。

- adc服务器环境例子：

  ```
  将文件 C:\Users\Administrator\Documents\Thales\Sentinel LDK 8.4\VendorCodes\TEOGQ.hvc
  复制到目录 /home/mc5k/code/Sentinel-LDK/VendorTools/Envelope
  
  ```



### 2.2 将加密过程编写成python脚本

- 执行加密脚本

  ```shell
  # 执行 加密脚本（在编译后的项目根目录下运行）
  python encrypt.py
  ```



