# Redis 的配置

Redis 的配置文件位于 Redis 的安装路径下，文件名为 `redis.conf` 可以通过CONFIG命令查看或设置配置项

## 语法

Redis CONFIG命令格式如下： `redis 127.0.0.1:6379> CONFIG GET CONFIG_SETTING_NAME`

## 实例

`redis 127.0.0.1:6379> CONFIG GET loglevel` output: 1. 1\) "loglevel" 2. 2\) "notice"

使用 `*`获取所有的配置项

## 编辑配置

可以通过修改redis.conf文件或者使用CONFIG set命令来修改配置

### 语法

CONFIG SET 命令语法 `redis> CONFIG SET CONFIG_SETTING_NAME NEW_CONFIG_VALUE`

## 参数说明

| 参数名 | 值 | 说明 |
| :--- | :--- | :--- |
| daemonize | yesno | Redis默认不是以守护进程的方式运行，可以通过该配置项修改 |
| pidfile | /var/run/redis.pid | 当Redis以守护进程方式运行时，Redis默认会把pid写入/var/run/redis.pid |
| port | 6379 | Redis的监听端口 |
| bind | 127.0.0.1 | 绑定的主机地址 |
| timeout | 300 | 当客户端限制多长时间后关闭连接，如果指定为0，则关闭这个功能 |
| loglevel | verbose | 指定日志记录级别，共支持四个级别：debug、verbose、notice、warning。默认为verbose |
| logfile | stdout | 日志记录方式，默认为标准输出，如果配置Redis为守护进程方式运行，而这里又配置为日志记录方式为标准输出，则日志将会发送给/dev/null |
| databases | 16 | 设置数据库的数量，默认数据库为0，可以使用SELECT \命令在连接上指定数据库id |
| save | \ \ | 指定在多长时间内，有多少次更新操作，就将数据同步到数据文件，可以多个条件配合 |
| rdbcompression | yes | 指定存储至本地数据库时是否压缩数据，默认为yes，Redis采用LZF压缩，如果为了节省CPU时间，可以关闭该选项，但会导致数据库文件变的巨大 |
| dbfilename | dump.rdb | 指定本地数据库文件名，默认值为dump.rdb |
| dir | ./ | 指定本地数据库存放目录 |
| slaveof | \ \ | 设置当本机为slav服务时，设置master服务的IP地址及端口，在Redis启动时，它会自动从master进行数据同步 |
| masterauth | \ | 当master服务设置了密码保护时，slav服务连接master的密码 |
| requirepass | foobared | 设置Redis连接密码，如果配置了连接密码，客户端在连接Redis时需要通过AUTH \命令提供密码，默认关闭 |

