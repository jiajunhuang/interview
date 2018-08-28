# Redis相关问题

- 过期删除策略

    - 被动删除：当读/写一个已经过期的key时，会触发惰性删除，直接删除掉这个过期的key
    - 主动删除：由于惰性删除无法保证所有的过期key都被删掉，所以Redis会定期删除过期的key
    - 当当前内存超过maxmemory限定时，触发主动清理策略

- Redis提供的数据结构

    - list
    - hash
    - set
    - sorted set -> 由skip list实现

- AOF和RDB

    - http://redisbook.readthedocs.io/en/latest/internal/aof.html

        AOF是Append Only File，一个文件，保存了所有执行的命令。redis server执行完命令之后，会把命令转成redis的协议
        追加到 `redis.h/redisServer` 结构的 `aof_buf` 的末尾。AOF有三种保存模式：

            - AOF_FSYNC_NO: 不保存。服务器断电，数据丢失。只有在AOF关闭或者redis关闭时执行。
            - AOF_FSYNC_EVERYSEC: 每一秒保存一次，由子线程执行，不过还与server的状态有关。处于1和3的中间态。
            - AOF_FSYNC_ALWAYS: 每执行一个命令保存一次，由主线程执行。数据安全，但是写入的时候会阻塞主线程，故性能差。

    - AOF重写: 时间久了之后，AOF会变的很大，当中有很多命令例如 `LPUSH l 1`, `LPUSH l 2`，此时可以使用一条 `LPUSH l 1 2`
    替代，从而缩小AOF文件大小。AOF重写时，fork自身，借用COW机制，主进程开始写入到aof重写缓存里，子进程重写时，主进程
    继续处理命令请求，将命令追加到现有的AOF中，将命令追加到AOF重写缓存。当子进程完成重写之后，会向父进程发送一个完成信号，
    父进程会调用信号处理函数，将AOF重写缓存中的内容全部重写到新的AOF中，将新的AOF重命名，覆盖老的AOF。

	- http://redisbook.readthedocs.io/en/latest/internal/rdb.html

    RDB以二进制的方式将redis中的内容写到磁盘。类似于一个快照。
