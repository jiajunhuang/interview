# gRPC相关

## HTTP2

- HTTP2比1，改进在哪？为什么会更快？

    - https://www.zhihu.com/question/34074946
    - 多路复用，允许通过单一的HTTP/2连接发起多重的请求-响应消息。而HTTP/1.1协议中，
    浏览器客户端在同一时间内，针对同一域名下的请求数量有一定的数量限制。超过限制数目
    的请求会被阻塞。(解决办法：CDN使用多个域名)
    - 二进制分帧，HTTP/2会将所有传输的信息分割成更小的消息和帧，并对他们采用二进制
    格式的编码，其中HTTP/1.1中的首部信息会被封装到HEADER frame，而body则会封装进
    DATA frame。头部进行压缩，使用[HPACK](https://www.jianshu.com/p/f44b930cfcac)算法。
    - server push。

------------

- https://http2.github.io/http2-spec/

## protobuf

- https://developers.google.com/protocol-buffers/docs/encoding

    - little endian
    - base 128 varints: 每个byte的第一位，代表接下来是否仍然有数据。
    低的7位表示数据，base-128是因为2的7次方是128。例如1，则表示为
    `0000 0001`，300，则表示为 `1010 1100 0000 0010`，怎么还原成300呢？
    首先把高位忽略，变成 `010 1100 000 0010`，然后翻转（因为是little endian），
    所以是 `000 0010 010 1100`，即 `100101100`，算一下即 `256 + 32 + 8 + 4 = 300`
    - 表示类型的byte，低三位表示类型，高三位是field number，即proto message里的tag。

- gRPC vs RESTful

    - 需要传输的数据量更小(数据压缩，例如int32最少只需要一个byte)
    - HTTP头部有很多重复的信息
    - 调用更方便
