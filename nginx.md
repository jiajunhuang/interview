# Nginx相关问题

- 性能瓶颈可能出现在哪

    - CPU太弱
    - worker数量比CPU核心数大太多，导致频繁上下文切换
    - 连接数，包括最大连接数，和当前是否由太多连接占着茅坑不拉屎

    - 解决方案

        - [CPU affinity](http://nginx.org/en/docs/ngx_core_module.html#worker_cpu_affinity)
        - 提高连接数
        - 正确设置worker数

- 惊群问题及其解决方案

    - https://en.wikipedia.org/wiki/Process_management_(computing)
    - 解决方案：没有解决方案。accept惊群已经被内核解决了。但是epoll惊群没有完全解决。
