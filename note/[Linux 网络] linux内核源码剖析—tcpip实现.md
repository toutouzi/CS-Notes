**参考资料**

- [Linux源码在线](https://elixir.bootlin.com/linux/v5.15.106/source)



# 1 预备知识

## 1.1 应用层配置诊断工具

### 1.1.1 **iputils** 

### 1.1.2 **net-tools**

### 1.1.3 **iproute2**



## 1.2 内核空间与用户空间接口

​	内核提供接口给用户空间程序，以便用户进程进行信息的读取或配置。**procfs**和**sysctl**可以导出内核内部信息，但是procfs主要用于导出只读数据，而sysctl导出的信息是可写的。

### 1.2.1 procfs

​	`procfs` 是一个虚拟文件系统，通常挂在`/proc` 目录下。网络代码注册文件一般位于`/proc/net` 下。大多数网络功能初始化的时，都会在`/proc/net` 中注册一个或多个这样的文件。

​	创建`proc_net_fops_create()` 和删除文件`proc_net_remove` ，这两个函数分别包装了`create_proc_entry` 和 `remove_proc_entry()`。创建文件的的同时也会初始化这个文件的操作函数。例如：

​	这是IP组播在`/proc/net`目录下注册`ip_mr_vif`文件的例子：

<img src="../img/[linux网络] linux内核源码剖析—tcpip实现/1/1.2.1.procfs_1.png" style="zoom:100%;" />

​	



### 1.2.2 sysctl

### 1.2.3 sysfs

### 1.2.4 ioctl

### 1.2.5 netlink套接口

## 1.3 网络I/O加速

## 1.4 其他



