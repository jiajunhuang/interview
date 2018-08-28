# HTTP(s)协议和HTTP/2

- HTTP并没有规定其下面该用什么协议（TCP，UDP）实现，不过通常是用TCP（因为安全，可靠）

- HTTP方法

    - GET -> 幂等
    - HEAD -> 幂等
    - POST
    - PUT
    - DELETE
    - TRACE
    - OPTIONS
    - CONNECT
    - PATCH

- 历史版本

    - HTTP/1.0
    - HTTP/1.1 相对于1.0增加了 Keep-Alive, Cache-Control, Chunked。所有头部中除了Host
    其他都是可选的
    - HTTP/2 见 [这里](./grpc.md)

- 状态码

    - 100 go on

        - 100 Continue
        - 101 Switching Protocols
        - 102 Processing

    - 200 success

        - 200 OK
        - 201 Created
        - 202 Accepted
        - 204 No Content

    - 300 go away

        - 300 Multiple Choices
        - 301 Moved Permantly
        - 302 Found
        - 303 See Other
        - 304 Not Modified
        - 305 Use Proxy
        - 307 Temporary Redirect，与302不同，不会改变请求方法
        - 308 Permanent Redirect，与301不同，不会改变请求方法

    - 400 your bad

        - 400 Bad Request
        - 401 Unauthorized
        - 402 Payment Required
        - 403 Forbidden
        - 404 Not Found
        - 405 Method Not Allowed
        - 429 Too Many Requests

    - 500 my bad

        - 500 Internal Server Error
        - 501 Not Implemented
        - 502 Bad Gateway
        - 503 Service Unavailabel
        - 504 Gateway Timeout

- 基本的请求体结构：

```http
GET / HTTP/1.1
Host: www.google.com
```

- 基本的响应结构：

```http
HTTP/1.1 200 OK
Content-Length: 599
Server: openresty
Date: 2018...
Content-Type: application/json
Connection: Keep-Alive
```

- Chunked模式

```http
HTTP/1.1 200 OK
Content-Type: text/plain
Transfer-Encoding: chunked

25
blablabla

1c
bladlsjdlksajd

8
kldsajlkds

0 -> 0然后\r\n\r\n就是结束
```

- HTTPs握手流程

    - 浏览器发起请求
    - 服务端下发证书和支持的加密方法
    - 浏览器验证证书是否可信
    - 如果可信，浏览器选择自己也支持的加密方法，生成随机数
    - 浏览器用公钥加密随机数，发送给服务器
    - 开始对称加密通信

- HTTP/2

    - 压缩头部
    - 多路复用(分帧，帧上面带有序号)
    - server push
