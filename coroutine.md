# 协程

ref: https://jiajunhuang.com/articles/2018_04_03-coroutine.md.html

- https://en.wikipedia.org/wiki/Coroutine

协程是用户空间的“线程”，由程序员自己控制代码跳转。

协程分为有栈(stackful)协程和无栈(stackless)协程：

- 有栈协程：

    - 如goroutine和greenlet。每个G都有自己的栈指针SB，SP等。切换到某个G执行时，将SP和SB刷到寄存器，
    使用G自带的栈。
    - 优点是更加灵活，可以整栈切换。

- 无栈协程：

    - 如Python中的generator(带yield的函数就是generator)，只把该函数执行时的帧带上，
    执行时，使用调用者的栈。
    - 无栈协程职能拿最上面的帧。因此优点是要保存的数据较少。缺点是没有有栈协程灵活，
    可以一锅端。通常性能也比有栈协程差一点。

---------------------

- goroutine: https://github.com/jiajunhuang/go/blob/718d6c5880fe3507b1d224789b29bc2410fc9da5/src/runtime/runtime2.go#L330-L400

- greenlet: https://github.com/python-greenlet/greenlet/blob/2032971562ac6d89bc5ef09af7c0d4eeb30a8079/greenlet.h#L16-L32

- Generator: https://github.com/jiajunhuang/cpython/blob/f62415324ce64c5e7a94719f7d5aae93dfc144be/Include/genobject.h#L15-L33
