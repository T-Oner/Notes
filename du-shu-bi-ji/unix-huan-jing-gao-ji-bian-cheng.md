# Unix 环境高级编程

## 文件 I/O

### 函数 open 和 openat

```c
#include <fcntl.h>

int open(const char *path, int oflag, ... /* mode_t mode */);

int openat(int fd, const char *path, int oflag, ... /* mode_t mode */);
```

两者的函数签名的区别就是 `openat` 多了一个 `fd` ，这个 `fd` 参数是用来实现 `相对路径` 的功能

二者的区别：

1. `openat` 可以实现通过相对路径来打开文件
2. 可以避免 `TOCCTTOU` 错误

> 注：TOCCTTOU 错误的基本思想是，如果有两个基于文件的函数调用，其中第二个调用依赖于第一个调用的结果，那么程序是脆弱的。因为两个调用并不是源自操作，在两个函数调用之间文件可能改变了，这样就造成了第一个调用的接口就不再有效。



