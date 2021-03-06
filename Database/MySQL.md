- mysql的逻辑架构: 第1层包括连接处理，授权认证, 第2层查询引擎: 包括查询解析，分析，优化，缓存，内置函数，存储过程等的实现, 第3层包含了存储引擎
- 每个客户端连接在服务器进程中拥有一个线程, 服务端负责缓存线程不需要为每个连接创建/销毁线程
- 在文件系统中，MySQL将每个数据库(schema)保存为数据目录下的一个子目录。创建表时在数据库子目录下创建一个表同名的.frm文件保存表的定义.

## 并发控制
- 共享锁(读锁)和排他锁(写锁): 写锁的优先级高于读锁
- 表锁和行级锁: 服务器会为诸如ALTER TABLE之类的语句使用表锁，而忽略存储引擎的锁机制; 行级锁只在存储引擎层面实现
- 事务: 事务就是一组原子性的SQL查询。原子性，一致性，隔离性，持久性(ACID)
- 原子性(atomicity): 整个事务中的所有操作要么全部提交成功，要么全部失败回滚
- 一致性(consistency): 总是从一个一致性的状态转换到另一个一致性的状态
- 隔离性(isolation): 一个事务所做的修改在提交之前，对其他事务是不可见的
- 持久性(durability): 一旦事务提交所做修改就会永久保存到数据库中
- 死锁: 两个及以上事务在同一资源上相互占用，并请求锁定对方占用的资源(循环依赖)
- 事务日志: 存储引擎在修改表的数据时只需要修改其内存拷贝，再把该修改行为append到磁盘中
- 多版本并发控制(MVCC): 非阻塞的读操作, 只锁定必要行的写操作

### 隔离级别
- 未提交读(READ UNCOMMITTED): 事务中的修改即使没有提交对其他事务也是可见的，事务可以读取未提交的数据(脏读)
- 提交读(READ CIMMITED): 满足隔离性, 但不保证同一事务在多次读取同样的记录的结果是一致的(不可重复读)
- 可重复读(REPEATABLE READ): 保证同一事务在多次读取同样的记录的结果是一致的
- 可串行化(SERIALIZABLE): 强制事务串行执行，避免了前三种隔离级别可能出现的幻读问题
