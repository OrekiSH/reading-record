## 进程管理
- 进程不局限于代码段(text section),通常还要包含打开的文件，挂起的信号，内核内部数据，处理器状态，1到多个具有内存映射的内存地址空间, 1到多个执行线程，存放全局变量的数据段等
- 执行线程: 简称线程，是在进程中活动的对象。每个线程都拥有一个独立的程序计数器、进程栈和一组进程寄存器。内核调度的对象是线程而不是进程
- Linux内核将进程的列表存放在任务队列中(双向循环链表), 链表中的每一项都是类型为`task_struct`，被称为进程描述符(process descriptor)的结构
- 进程状态: `TASK_RUNNING`, `TASK_INTERRUPTIBLE`, `TASK_UNINTERRUPTIBLE`, `__TASK_TRACED`, `__TASK_STOPPED`, `set_task_state`
- 进程上下文: 当程序执行了系统调用或触发了某个异常，便从用户空间进入了内核空间, 此时我们称内核处于进程上下文中
- 进程家族树: 所有进程都是PID为1的init进程的后代
- fork: 使用写时拷贝(copy-on-write)页, 内核不直接复制整个进程地址空间, 而是在需要写入的时候复制资源，在写入之前父子进程共享一份只读的拷贝
- `copy_process`: 调用`dup_task_struct`为新进程创建一个内核栈,`thread_info`,`task_struct` -> 检查用户拥有的进程数未超出分配资源的限制 -> 子进程的状态被设置为`TASK_UNINTERRUPTIBLE` -> 调用`copy_flags`更新`task_struct`的flags -> 调用`alloc_pid` -> 根据传递给`clone`的参数标志, 拷贝或共享打开的文件，文件系统信息，信号处理函数，进程地址空间和命名空间 -> 返回指向子进程的指针
- vfork: 除了不拷贝父进程的页表项外，vfork和fork的功能相同
- Linux内核并没有定义特别的调度算法或是数据结构表征线程, 线程仅被视为一个与其他进程共享某些资源的进程。线程的创建也与进程类似，不过在调用`clone`时需要传递一些参数标志来表明需要共享的资源
- 内核线程: 由`kthreadd`内核进程创建, 没有独立地址空间, 独立运行在内核空间的进程
- `do_exit`: 将`task_struct`中的标志成员设为`PF_EXITING` -> 调用`del_timer_sync`删除任一内核定时器 -> 调用`exit_mm`释放进程占用的`mm_struct` -> 调用`sem__exit` -> 调用`exit_files`和`exit_fs`递减文件描述符和文件系统数据的引用计数 -> 将`task_struct`中的`exit_code`设置为`exit`提供的退出代码 -> 调用`exit_notify`向父进程发送信号，在当前线程组内寻找一个新的父进程, 并将进程状态设置为`EXIT_ZOMBIE` -> 调用`schedule`切换到新的进程

## 进程调度
- Linux提供了抢占式多任务(preemptive multitasking)的模式，调度程序负责决定那个进程投入运行，何时运行以及运行多少时间。强制进程挂起的动作被称为抢占(preemption)，进程主动挂起的动作被称为让步(yielding), 进程在被抢占之前能够运行的时间被称为时间片(timeslice)
- Linux为了保证交互式应用和桌面系统的性能，相比于CPU消耗型进程更倾向于优先调度I/O消耗型进程
- 进程优先级: nice值(-20到19, 值越大优先级越低，默认值为0)和实时优先级(0到99, 实时进程的优先级都高于普通进程)
- Unix的进程调度的缺陷: 将nice值映射到时间片引发否固定切换频率给公平性造成了很大的变数
- 时间记账
- 进程选择
- 调度器入口 
- 睡眠和唤醒
- 上下文切换: `schedule`调用`context_switch`将虚拟内存和处理器状态(保存、恢复栈信息和寄存器信息)从一个可执行进程切换到另一个可执行线程

```c
struct sched_entity {
  struct load_weight load;
  struct rb_node run_node;
  struct list_head group_node;
  unsigned int on_rq;
  u64 exec_start;
  u64 sum_exec_runtime;
  u64 vruntime;
  u64 prev_sum_exec_runtime;
  u64 last_wakeup;
  u64 avg_overlap;
  u64 nr_migrations;
  u64 start_runtime;
  u64 avg_wakeup;
}
```

## 系统调用
- 系统调用的作用: 作为用户空间进程和硬件设备之间的中间层，为用户空间提供了硬件的抽象接口, 保证了系统的稳定和安全
- Linux的系统调用和大多数Unix系统一样，作为C库的一部分提供
- 系统调用号一旦分配就不能再有变更，被删除后也不会被回收利用
