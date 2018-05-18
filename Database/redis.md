## 简单动态字符串(simple dynamic string)
```c
struct sdshdr {
  int len; // buf数组中已使用的字节数量
  int free; // buf数组中未使用的字节数量
  char buf[];
}
```

- 常数复杂度获取字符串长度
- 杜绝缓冲区溢出
- 通过空间预分配和惰性空间释放, 减少修改字符串时带来的内存重分配次数
- 二进制安全(通过len的值而不是空字符串判断字符串是否结束)

## 链表
```c
typedef struct listNode {
  struct listNode *prev;
  struct listNode *next;
  void *value;
} listNode;
```

```c
typedef struct list {
  listNode *head;
  listNode *tail;
  unsigned long len;
  void *(*dup)(void *ptr);
  int (*match)(void *ptr, void *key);
} list;
```

## 字典
字典，又被称为符号表(symbol table), 关联数组或者映射(map)
```c
typedef struct dictht {
  dictEntry **table;
  unsigned long size; // 哈希表大小，即table数组大小
  unsigned long sizemask; // 哈希表大小掩码，总是等于size - 1(用于计算索引值)
  unsigned long used;
} dictht;
```

```c
typedef struct dictEntry {
  void *key;
  union {
    void *val;
    uint64_tu64;
    int64_ts64;
  } v;
  struct dictEntry *next; // 解决键值哈希值相同的情况
} dictEntry;
```

## RDB和AOF(Append Only File)持久化
- 为了方便起见，我们将服务器中的非空数据库以及他们的键值对统称为数据库状态
- RDB持久化通过保存数据库中的键值对来记录数据库状态，AOF持久化则是通过保存Redis服务器执行的写命令来记录数据库状态
- 由于AOF文件的更新频率通常比RDB文件更高，因此如果服务器开启了AOF持久化，将会优先使用AOF文件恢复数据库状态
- BGSAVE命令执行期间，客户端发送的SAVE/BGSAVE命令会被服务器拒绝(避免父子进程同时执行rdbSave调用产生竞争条件)，BGREWRITEAOF会被延迟到BGSAVE命令完成后执行。BGREWRITEAOF命令执行期间，客户端发送的BGSAVE命令会被服务器拒绝
- 为提高文件的写入效率，当用户调用write函数时，现代操作系统会将写入数据暂时保存到内存缓存区中，等到缓存区空间被占满或是超过指定时限后再将数据写入磁盘。为保证数据安全性，操作系统提供了fsync和fdatasync两个同步函数

```c
// RDB
struct saveparam {
  time_t seconds;
  int changes; // 修改数
}

// AOF
struct redisServer {
  sds aof_buf;
}
```

## 事件
- 文件事件处理器(file event handler): I/O多路复用程序监听多个套接字，并通过一个有序同步队列向文件事件分派器(dispatcher)传递那些产生了事件的套接字.文件事件分派器则根据套接字产生的事件类型调用事件处理器
- serverCron(时间事件): 更新服务器各类统计信息, 清理过期键值对和连接失效的客户端，尝试进行持久化操作

## 复制
- 通过`SLAVEOF`命令或者设置slaveof选项, 让一个服务器去复制另一个服务器, 称被复制的服务器为主服务器，进行复制的被成为从服务器。进行复制中的主从服务器双方保持着相同的数据，这种现象被称为数据库状态一致
- Redis的复制功能分为同步和命令传播
- SYNC: 从服务器向主服务器发送`SYNC`命令，主服务器收到`SYNC`命令后执行`BGSAVE`命令,并使用一个缓冲区记录现在开始执行的所有写命令。`BGSAVE`命令执行完成后，主服务器将生成的RDB文件和记录在缓冲区的写命令发送给从服务器。
- PSYNC: 分为完整重同步(SYNC)和部分重同步模式。部分重同步功能由主从服务器的复制偏移量，主服务器的复制积压缓冲区(replication backlog), 服务器的运行ID(40个随机的十六进制字符)三部分组成。
- 复制积压缓冲区: 固定长度(1MB)的先进先出队列。当主服务器进行命令传播时，不仅会将写命令发送给所有从服务器，还会将写命令入队到复制积压缓冲区中，复制积压缓冲区记录了每个字节对应的复制偏移量。如果offset偏移量之后的数据依然存在于复制积压缓冲区中，则进行部分重同步，反之则进行完整重同步.

## 慢查询日志
```c
typedef struct slowlogEntry {
  long long id;
  time_t time; // 命令执行时间
  long long duration; // 执行命令耗时(μs)
  robj **argv;
  int argc;
} slowlogEntry
```
- `CONFIG SET slow-log-slower-than <integer>(μs)`, `CONFIG SET slowlog-max-len <integer>`, `SLOWLOG GET`
