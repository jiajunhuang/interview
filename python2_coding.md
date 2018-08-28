# Python2编码问题

python 2中，str就是bytes。存储的是编码后的字符串。

默认是ascii编码，所以不能存储中文。加上 `# coding: utf-8`就变成了utf8
编码。

unicode是字符集，是编码前的字符集，一个unicode占4个字节。

```python
# coding: utf-8

a = "啊哈哈"

print(str == bytes)
print(a.decode("utf8"))
print(a.decode("utf8").encode("utf8"))
print(a.decode("utf8").encode("utf8") == a)
```

执行：

```bash
True
啊哈哈
啊哈哈
True
```
