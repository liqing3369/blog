# goroutine调度器

goroutine调度器本身也是一个goroutine，它负责实现：

- 哪个goroutine应该获取资源开始执行
- 哪个goroutine应该停止执行让出资源
- 哪个goroutine应该被唤醒恢复执行，等等。 

上面说的资源，指的就是系统线程资源。

![y](http://tonybai.com/wp-content/uploads/goroutine-scheduler-model.png)

在开始解读go scheduler之前，我们需要先了解go runtime的三个抽象模型G、P、M。

G，就是goroutine。这个结构体用来跟踪goroutine的栈和状态。

P，是逻辑处理器。通过`runtime.GOMAXPROCS`设置P的数量。对于G来说，P就是运行它的“CPU” 。

M，代表一个真正的操作系统线程。当P和M绑定时，也就是P拿到物理CPU时间片时，goroutine才能真正执行。

## 抢占式调度

