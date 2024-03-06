参考公众号：小徐先生的编程世界
# 一.线程、协程和goroutine
1.线程是运行在操作系统内核的最小调度单元，线程可以实现并行，取决于cpu的数量和核心数。
2.协程是运行在用户态，多个协程依赖于一个线程。协程实际上是并发运行的。如果一个协程阻塞了，依赖于同一个线程的协程会同样阻塞。
3.Goroutine是golang优化后的协程，增加了调度器的概念，将线程、协程解耦。调度器的栈空间大小可动态扩缩，因地制宜。
以下是对比图：
![[Pasted image 20240111192214.png]]
# 二.GMP模型
gmp = goroutine + machine + processor （+ 一套有机组合的机制)
## 2.1 "G"
G就是goroutine，代表golang中的协程
## 2.2"M"
M是machine，golang对线程的抽象
## 2.3 "P"
p 即 processor，是golang中的调度器
## 2.4 GMP

![[Pasted image 20240111192834.png]]

由图可知，调度器P为线程分配协程任务。每个协程都有自己的**本地队列**，这个私人队列是**趋近于无锁**的，所以调度的很快。如果有P调度完了自己的goroutine，就会去**全局队列**中获取G执行。如果全局队列也没有可执行的gouroutine了，P就会窃取其他调度器的协程，即**work-stealing**机制。
## 三.核心数据结构
gmp 数据结构定义为 runtime/runtime2.go 文件中，由于各个类的成员属性较多，那么只摘取核心字段进行介绍.
## 3.1 g
```
type g struct {
  m       *m
  sched   gobuf
  ......
}
```
（1）m：在 p 的代理，负责执行当前 g 的 m；

（2）sched.sp：保存 CPU 的 rsp 寄存器的值，指向函数调用栈栈顶；

（3）sched.pc：保存 CPU 的 rip 寄存器的值，指向程序下一条执行指令的地址；

（4）sched.ret：保存系统调用的返回值；

（5）sched.bp：保存 CPU 的 rbp 寄存器的值，存储函数栈帧的起始位置.
g的生命周期由以下几种状态组成：
![[Pasted image 20240111195418.png]]
```
const(
  _Gidle = itoa // 0
  _Grunnable // 1
  _Grunning // 2
  _Gsyscall // 3
  _Gwaiting // 4
  _Gdead // 6
  _Gcopystack // 8
  _Gpreempted // 9
)
```
（1）_Gidle 值为 0，为协程开始创建时的状态，此时尚未初始化完成；

（2）_Grunnable 值 为 1，协程在待执行队列中，等待被执行；

（3）_Grunning 值为 2，协程正在执行，同一时刻一个 p 中只有一个 g 处于此状态；

（4）_Gsyscall 值为 3，协程正在执行系统调用；

（5）_Gwaiting 值为 4，协程处于挂起态，需要等待被唤醒. gc、channel 通信或者锁操作时经常会进入这种状态；

（6）_Gdead 值为 6，协程刚初始化完成或者已经被销毁，会处于此状态；

（7）_Gcopystack 值为 8，协程正在栈扩容流程中；

（8）_Greempted 值为 9，协程被抢占后的状态.
## 3.2 m
```
type m struct {
    g0      *g     // goroutine with scheduling stack
    // ...
    tls           [tlsSlots]uintptr // thread-local storage (for x86 extern register)
    // ...
}
```
（1）g0：一类特殊的调度协程，不用于执行用户函数，负责执行 g 之间的切换调度. 与 m 的关系为 1:1；

（2）tls：thread-local storage，线程本地存储，存储内容只对当前线程可见. 线程本地存储的是 m.tls 的地址，m.tls[0] 存储的是当前运行的 g，因此线程可以通过 g 找到当前的 m、p、g0 等信息.

## 3.3 p
```
type p struct {
    // ...
    runqhead uint32
    runqtail uint32
    runq     [256]guintptr
    
    runnext guintptr
    // ...
}
```
（1）runq：本地 goroutine 队列，最大长度为 256.

（2）runqhead：队列头部；

（3）runqtail：队列尾部；

（4）runnext：下一个可执行的 goroutine.
## 3.4 schedt
```
type schedt struct {
    // ...
    lock mutex
    // ...
    runq     gQueue
    runqsize int32
    // ...
}
```
sched 是全局 goroutine 队列的封装：

（1）lock：一把操作全局队列时使用的锁；

（2）runq：全局 goroutine 队列；

（3）runqsize：全局 goroutine 队列的容量.
# 四. 调度流程
## 4.1 两种g的转换

如 3.2 小节中所谈及的，goroutine 的类型可分为两类：

1. 负责调度普通 g 的 g0，执行固定的调度流程，与 m 的关系为一对一；

2. 负责执行用户函数的普通 g.

m 通过 p 调度执行的 goroutine 永远在普通 g 和 g0 之间进行切换，当 g0 找到可执行的 g 时，会调用 gogo 方法，调度 g 执行用户定义的任务；当 g 需要主动让渡或被动调度时，会触发 mcall 方法，将执行权重新交还给 g0.

gogo 和 mcall 可以理解为对偶关系，其定义位于 runtime/stubs.go 文件中.
```
func gogo(buf *gobuf)
// ...
func mcall(fn func(*g))
```
## 4.2 调度类型
![[Pasted image 20240111200806.png]]
