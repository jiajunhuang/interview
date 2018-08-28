# 内存模型

![mem layout](./img/os_mem_layout.png)

从上往下依次是：

- 环境变量和命令行参数等
- 栈
- 堆
- BSS段（存储未初始化的数据，由exec初始化为0）
- DATA段（存储已初始化的数据，如静态变量，全局变量）
- TEXT段（存储代码，也叫代码段）

其中DATA和TEXT段由exec函数从磁盘中的程序文件中读取
