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

## 中断
- 中断本质是由硬件设备产生的电信号，直接送入中断控制器的输入引脚。中断控制器收到中断信号后通过管线将CPU发送信号，CPU检测到信号后停止当前工作(不需要考虑处理器时钟同步)，通知操作系统已经产生中断。
- 不同设备产生的中断都有唯一的数字标志，这个数值被称为中断请求(IRQ)线。在响应中断时，内核会执行一个被称为中断处理程序(interrupt handler)或中断服务例程(interrupt service routine, ISR)的函数, 一个设备的中断处理程序是它设备驱动程序的一部分。
- 中断处理被分为上半部(top half)和下半部(bottom half)，上半部即中断处理程序(有严格时限)，允许稍后完成的工作会被推迟到下半部。例如网卡收到来自网络的数据包时，向内核发送中断信号，内核则通过执行网卡注册的中断处理程序做出应答: 通知硬件拷贝最新的网络数据包到内存然后读取网卡更多的数据包，这些操作被归为中断处理的上半部; 而处理和操作数据包的其他工作被归为下半部。
- 硬件 -> 中断控制器 -> CPU -> CPU中断内核 -> `do_IRQ()` -> 该线上有中断处理程序 -> `handle_IRQ_event()` -> 在该线上运行所有中断处理程序 -> `ret_from_intr()` -> 返回内核运行中断的代码
- 硬件 -> 中断控制器 -> CPU -> CPU中断内核 -> `do_IRQ()` -> 该线上没有中断处理程序 -> `ret_from_intr()` -> 返回内核运行中断的代码
- `watch -n1 "cat /proc/interrupts"`

```c
// 注册中断处理程序(<linux/interrupt.h>)
// 成功执行返回0
int request_irq(
  unsigned int irq, // 中断号
  irg_handler_t handler,
  // IRQF_DISABLED: 禁止所有其他的中断
  // IRQF_SAMPLE_RANDOM: 来自该设备的中断间隔时间将作为熵填充到内核熵池(entropy pool), 内核熵池负责提供从各种随机事件导出真正的随机数
  // IRQF_TIMER: 系统定时器
  // IRQF_SHARED: 共享中断线
  unsigned long flags,
  const char *name,
  void *dev, // 提供共享中断线的唯一标识
)
```

## 内存管理
- 内核将物理页作为内存管理的基本单位，尽管CPU的最小寻址单位通常为字/字节, 内存管理单元(MMU)通常以页为单位管理系统中的页表
- 由于硬件的限制(只能用某些特定内存地址执行DMA, 物理寻址范围远大于虚拟寻址范围), 内核将具有相似特性的页划分为不同的区(zone)
- `ZONE_DMA`, `ZONE_DMA32`, `ZONE_NORMAL`(能够正常映射的页), `ZONE_HIGHEM`(高端内存)
- 内核操作中通常使用空闲链表来进行数据的分配和回收，而空闲链表的问题在于无法全局控制(内核无法知道有那些空闲链表), 因此需要引入一层通用数据结构缓存层,slab层(或者叫slab分配器)
- 每个进程都有1到2页的内核栈(32位和64位的页大小分别为4kb和8kb)
- 高端内存中的页不能永久映射到内核地址空间上，因此通过`alloc_pages`以`__GFP_HIGHMEM`标志获得的也不可能有逻辑地址

```c
// 系统物理页, <linux/mm_types.h>
struct page {
  unsigned long flags;
  atomic_t _count; // 页的引用计数, 计数值为-1时表示内核未引用该页。然而内核代码不应直接检查_count的值，而应该调用page_count函数
  atomic_t _mapcount;
  unsigned long private;
  struct address_space *mapping;
  pgoff_t index;
  struct list_head lru;
  void *virtual; // 页的虚拟地址
}
```

```c
struct slab {
  struct list_head list;
  unsigned long colouroff;
  void *s_men; // slab中的第一个对象
  unsigned int inuse; // slab中已分配的对象数
  kmem_bufctl_t free; // 第一个空闲对象
}
```

```c
struct kmem_cache * kmem_cache_create(
  const char *name,
  size_t size, // 高速缓存中每个元素的大小
  size_t align, // slab中第一个对象的偏移量, 0即标准对齐
  
  // SLAB_HWCAHE_ALIGN: slab内所有对象按照高速缓存行对齐
  // SLAB_POISON: slab按照a5a5a5a5填充slab
  // SLAB_RED_ZONE: slab在已分配内存周围插入"红色警戒区"以探测缓冲越界
  // SLAB_PANIC: 分配失败时通知slab层
  // SLAB_CACHE_DMA: slab层使用可以执行DMA的内存进行分配
  unsigned long flags,
  void (*ctor)(void *),
)
```
