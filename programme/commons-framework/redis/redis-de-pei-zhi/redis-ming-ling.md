# Redis 命令

## 语法

客户端的基本语法 启动命令 `redis-cli`

### 在远程服务上执行命令

如果需要在远程redis服务上执行命令，同样是用的是`redis-cli`命令。

#### 语法

### `redis-cli -h host -p post -a password`

如果中文有乱码，要在 redis-cli后面加上 --raw `redis-cli --raw`

