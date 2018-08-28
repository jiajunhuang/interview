# Goroutine 和 Golang runtime

ref: https://jiajunhuang.com/articles/2018_02_02-golang_runtime.md.html
ref: https://jiajunhuang.com/articles/2017_02_24-goroutine_and_work_stealing.md.html

![GMP](./img/golang_gmp.jpg)

此外，还有：

- netpoller负责等待网络事件并且将能唤醒的G重新丢回去
- 一个全局G队列
- work stealing算法保证每个M都不会闲着
