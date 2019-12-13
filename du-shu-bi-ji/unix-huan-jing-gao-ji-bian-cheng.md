# Unix 环境高级编程

## Unix 基础知识

### 出错处理

Unix 中的出错处理分为两部分，一个是函数的返回，一个是 `errno` 。

* 函数一般返回 `-1` 或者 `null` 表示函数出错
* 但是具体的出错原因表示在 `errno`  中

#### errno

每个线程都有属于自己的局部 errno。每个函数出错后，这个变量会被赋予一个值，这个就是出错的具体原因。

errno 有两个规则

1. 如果没有出错，他的值不会被清除。所以只有当函数的返回值指明错误的时候，才应该检查他的值
2. 任何函数都不会将 errno 的值设置为 0

接下来附一示例代码，帮助理解：

```c
#include <stdio.h>
#include <errno.h>
#include <fcntl.h>//在centos6.0中只要此头文件就可以
#include <sys/types.h>
#include <sys/stat.h>

int main()
{
    int f = open("test", O_WRONLY);
    printf("%d %d", f, errno);
}
```

 这段程序的输出就是 `-1 2` 。因为这个文件根本就不存在。

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



