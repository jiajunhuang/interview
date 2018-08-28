# 操作系统调用

- exit

    - 正常退出的调用有

        - 在main中return
        - 调用exit -> 会执行atexit，flush IO等
        - 调用 _exit, _Exit -> 不会执行上述步骤
        - 在线程的起始函数中执行return
        - 最后一个线程中调用 pthread_exit

    - 非正常退出

        - 调用abort
        - 收到异常信号，无法或者没有避免终止时
        - 最后一个线程收到 `pthread_cancel` 的请求时

其中，正常退出的前三种，由传递的状态值设置程序退出的状态码。非正常退出的三种，由内核设置状态码。

- epoll

    - `epoll_create`, `epoll_create1` -> 返回一个epoll实例
    - `epoll_ctl` -> 增删fd及其要监听的事件
    - `epoll_wait`, `epoll_pwait` -> 等待IO事件

    - 水平触发边缘触发

        - 水平触发：只要没处理完，就会一直提示。这是默认的方式(LT)。
        - 边缘触发：只会提醒一次，不管有没有处理完，都不会再提醒。效率更高一些因为不会有大量重复。Go是用的ET。

    - `epoll_create` 的几个常见返回值

        - `EINVAL` 参数错误
        - `EMFILE` 用户能打开的文件数达到了上限
        - `ENFILE` 系统能打开得文件数达到了上限
        - `ENOMEM` 内存不够用

    - `int epoll_wait(int epfd, struct epoll_event *events, int maxevents, int timeout);`，其中event是一个64bit大小的
    内存块。几个常见的返回值

        - `EBADF` 错误的epoll fd
        - `EFAULT` 无权访问
        - `EINTR` 被信号中断
        - `EINVAL` 参数错误

    - `int epoll_ctl(int epfd, int op, int fd, struct epoll_event *event);`

        - op可选值

            - `EPOLL_ADD`
            - `EPOLL_MOD`
            - `EPOLL_DEL`

        - epoll_event

            - EPOLLIN 可读
            - EPOLLOUT 可写
            - EPOLLERR 发生错误
            - EPOLLET 边缘触发
            - EPOLLONESHOT 收到一次之后就不会再收到

        EPOLLET是收到新的数据会再次触发。但是对于同一份数据，如果没读完，是不会再触发的。而EPOLLONESHOT则收到一次数据之后，
        就会自动从epoll里删掉。需要手动重新注册。http://man7.org/linux/man-pages/man7/epoll.7.html

- epoll vs select

	- `int select(int nfds, fd_set *readfds, fd_set *writefds, fd_set *exceptfds, struct timeval *timeout);`
    - select中，最多可以监听1024个fd。glibc中是这样写死的。
    - select中，需要自行遍历三个数组，而epoll中直接遍历返回结果然后对比flag就行。

- socket

    - `int socket(int domain, int type, int protocol);`

        - domain

            - AF_UNIX, AF_LOCAL 本地通信
            - AF_INET IPv4
            - AF_INET6 IPv6
            - ...

        - type

            - SOCK_STREAM
            - SOCK_DGRAM
            - SOCK_RAW 裸包

        - protocol 一般为0，否则为指定某种协议

    - `int shutdown(int sockfd, int how);` 关闭socket。how可以是 `SHUT_RD`, `SHUT_WR`, `SHUT_RDWR`

        - vs `close`: close只有最后一个fd时才生效，如果我们duplicate了socket，起始并没有作用。另外shutdown可以单方向关闭。

    - big-endian, little-endian。网络序是大端。intel的机器一般都是小端。

        - `uint32_t htonl(uint32_t hostint32);`
        - `uint16_t htons(uint16_t hostint16);`
        - `uint32_t ntohl(uint32_t netint32);`
        - `uint16_t ntohs(uint16_t netint16);`

    - `int bind(int sockfd, const struct sockaddr *addr, socklen_t len);`
    - `int connect(int sockfd, const struct sockaddr *addr, socklen_t len)` `socklen_t len` 是缓冲区长度，上同。
    - `int listen(int sockfd, int backlog);`
    - `int accept(int sockfd, struct sockaddr *restrict addr, socklen_t *restrict len);`
    - `ssize_t send(int sockfd, const void *buf, size_t nbytes, int flags);`
    - `ssize_t sendto(int sockfd, const void *buf, size_t nbytes, int flags, const struct sockaddr *destaddr, socklen_t destlen);`
    - `ssize_t sendmsg(int sockfd, const struct msghdr *msg, int flags);`
    - `ssize_t recv(int sockfd, void *buf, size_t nbytes, int flags);`
