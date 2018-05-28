## Oracle体系结构
- 在不同的操作系统上， Oracle的物理体系结构有所不同。Unix上Oracle每个主要功能由一个进程负责， Windows上Oracle则实现为一个多线程的进程
- 数据库与实例: 实例由一组操作系统进程及一个共享内存区域构成， 而数据库只是文件集合(数据文件，临时文件，重做日志文件，控制文件), 在任何时刻，一个实例只能装载和打开一组相关的文件(与一个数据库关联), Real Application Clusters(RAC)中，多个实例可以同时装载和打开一个数据库
- SQL Server对每条执行的并发语句打开一个数据库连接，Oracle则希望最多打开一个连接
- 硬解析(hard parse): `select * from emp where empno = 123;`, 使用字面量，每个查询都将是一个全新的查询，需要对查询进行解析，命名解析，安全性检查，优化等.
- 软解析: `select * from emp where empno = :empno;`, 使用绑定变量(也可能产生硬解析)，变量值在查询执行时提供，该查询只编译一次，随后将查询计划存储在共享池(库缓存)中

## 文件
- 参数文件(parameter file): 告诉实例在哪里可以找到控制文件，并指定初始化参数。SID是站点标识符(site identifier), 在Unix中，SID和ORACLE_HOME一同进行散列运算，创建一个唯一键名附到SGA中
- 服务器参数文件(SPFILE): 保证了数据库参数设置的唯一来源, 减少了手动维护参数文件(init.ora)的影响
- 跟踪文件(trace file): 一个服务器进程对某种异常错误条件做出响应时创建的诊断文件
- Oracle的可测量性: V$视图，审计命令，DBMS_RESOURCE_MANAGER, Oracle事件, DBMS_TRACE, 数据库事件触发器, SQL_TRACE/DBMS_MONITOR, 自动工作存储库(Automatic Workload Repository), Active Session History, 自动诊断存储库(Automatic Diagnostic Repository), SQL性能分析工具(SQL Perforinane Analyzer)
- 警告文件(alert file): 记录数据库被创建直到数据库被删除期间所有活动的文本文件
- 控制文件: 告知实例数据库和在线重做日志文件的位置
- 在线重做日志: 每个Oracle数据库至少有两个在线重做日志文件组，每个重做日志文件组都包含一个或多个重做日志成员。在线重做日志文件的大小是固定的，并以循环方式使用。从一个日志文件组切换到另一个组的动作被称为日志切换,日志切换时会建立一个检查点
- 归档重做日志: ARCHIVELOG模式与NOARCHIVELOG模式

## Oracle的存储层次体系
- 数据库由一个或多个表空间组成
- 表空间由一个或多个数据文件组成，这些文件可以是文件系统中的Cooked OS文件，原始分区(raw partitions)，ASM管理的数据库文件, 集群文件系统上的文件
- 段(segment)是占用存储空间的数据库对象, 由一个或多个区段组成。例如创建一个表时会创建一个表段(若是创建表时声明了索引， 也会创建一个索引段), 每个段都只属于一个表空间
- 区段(extent)是磁盘上一组逻辑连续的块, 文件本身在磁盘上并不是连续的
- 块是数据库中最小的分配单元和I/O单位

## 内存结构
- 系统全局区(System Global Area, SGA): `select pool, name, bytes from v$sgastat order by pool, name`
- 进程全局区(PGA): PGA不会在SGA中分配
- 用户全局区(UGA)


## 内存结构
- 系统全局区(System Global Area, SGA): `select pool, name, bytes from v$sgastat order by pool, name`
- 进程全局区(PGA): PGA不会在SGA中分配
- 用户全局区(UGA)

## 进程
- 服务器进程(专用/共享): 解析SQL查询并将查询放在共享池中，提出查询计划，必要时执行查询计划
- 连接与会话: 连接是客户进程与Oracle实例间的一条物理连接(例如客户端和实例的网络连接); 会话则是数据库的一个逻辑实体，客户进程可以在会话上执行SQL。一条连接上可以有0到多个会话，每个会话都是独立的，即使他们共享一条数据库连接
- Oracle Net: 远程执行，地址空间执行 `select a.spid, b.process from v$process a, v$session b where a..addr = b.paddr and b.sid = (<select>)`
- 后台进程: `select paddr, name, description from v$bgprocess where paddr <> '00' order by paddr desc;`
- 从属进程: I/O从属进程(为不支持异步I/O的设备模拟异步I/O)，并行查询从属进程(对于`SELECT`, `CREATE TABLE`, `CREATE INDEX, UPDATE`等创建一个执行计划，其中包含可以同时完成的多个子执行计划)

## 中心后台进程
- 进程监视器(PMON): 出现异常终止的连接后恢复或撤销未提交的工作，并释放失败进程分配的SGA资源; 监控其他的Oracle后台进程, 必要时重启这些进程; 向Oracle TNS监听器注册实例
- 系统监视器(SMON): 清理临时空间，合并表空间中互相连续的空闲区段，恢复由于文件不可用导致的失败事务，执行RAC中失败结点的实例恢复，清理OBJ$, 收缩和离线回滚段
- 分布式数据库恢复(RECO)
- 检查点进程(CKPT)
- 数据库块写入器(DBWn): 将缓冲区中的脏块刷新输出到磁盘
- 日志写入器(LGWR): 将SGA中重做日志缓冲区的内容刷新输出到磁盘(每3秒会刷新输出一次，任何事务发出一个提交，重做日志缓冲区使用超过1/3， 或是已包含1MB的缓冲数据)
- 归档进程: ARCn
- 诊断性进程: DIAG

## 数据库表
