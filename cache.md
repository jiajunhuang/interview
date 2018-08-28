# 缓存相关

- 缓存算法

    - FIFO: first in first out
    - LRU: least recent used -> 很好用，但是实现很复杂
    - LFU: least frequency used -> 同上

- 缓存更新策略

    - cache aside: 更新数据删缓存，读取数据加缓存/如果缓存有，则直接从缓存读
    - read/write through: 读时由服务端自己将缓存载入，更新时如果没有命中缓存，则直接更新DB，若命中，则更新缓存，由DB自己
    从缓存刷到DB
    - write behind cache：读和写都在cache上，由server自己刷数据回DB。例如：mmap。

> 参考：https://coolshell.cn/articles/17416.html

- redis vs memcached

    - 读写速度两者相当
    - redis更省内存
    - disk i/o dumping: redis更好因为可以配置
    - scaling: 有很多工具
    - redis有更加丰富的数据结构和原子操作
