# Redis 安装

## Mac下安装：

`brew install redis`

## 启动

### 后台启动

`brew services start redis`

### 直接启动

`redis-server [config_path]` eg: `redis-server /usr/local/etc/redis.conf`

## 关闭

`redis-cli shutdown`

