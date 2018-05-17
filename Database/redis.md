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
