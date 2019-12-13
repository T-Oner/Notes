# Celery

## Celery 是什么

Celery 是一个分布式队列的管理工具，可以用 Celery 提供的接口快速实现并管理一个分布式的任务队列。

## 快速入门

以下是一些 Celery 的基础概念

### Brokers

代理人、中间人的意思。就是指任务队列本身。Celery 是生产者和消费者的角色，此处的 brokers 就是生产者和消费者存放或者拿去产品的队列

### Result Stores/backend

结果存放的地方，队列中的任务运行完成后的结果或者状态需要被任务发送者知道，那么就需要一个地方存储这些结果，就是 Result Stores 了。

常见的 backends 有 redis、Memcached，或者常用的数据库

### Workers

Celery 中的工作者，类似于生产者消费者模型中的消费者，Workers 从队列中取出任务并执行。

### Tasks

### Tasks 就是想在队列中执行的任务，一般由用户、触发器或者其他操作将 Task 入队，然后交由 workers 进行处理。

## 简单实战

采用 Redis 当做 celery 的 broker 和 backend。 先写一个 Task

```python
#tasks.py
from celery import Celery

app = Celery('tasks',  backend='redis://localhost:6379/0', broker='redis://localhost:6379/0') #配置好celery的backend和broker

@app.task  #普通函数装饰为 celery task
def add(x, y):
    return x + y
```

有了 broker、backend 和 task，接下来就该 workers 了，在 py 文件目录下运行 `celery -A tasks worker --loglevel=info` 意思就是运行 tasks 这个任务集合的 worker 进行工作（当然此时broker中还没有任务，worker此时相当于待命状态） 最后一步，就是触发任务，最简单方式就是再写一个脚本然后调用那个被装饰成 task 的函数：

```python
#trigger.py
from tasks import add
result = add.delay(4, 4) #不要直接 add(4, 4)，这里需要用 celery 提供的接口 delay 进行调用
while not result.ready():
    time.sleep(1)
print 'task done: {0}'.format(result.get())
```

delay 返回的是一个 AsyncResult 对象，里面存的就是一个异步的结果，当任务完成时result.ready\(\) 为 true，然后用 result.get\(\) 取结果即可。

### 解析

```python
@app.task  #普通函数装饰为 celery task
def add(x, y):
    return x + y
```

这里的装饰器`app.task`是将一个函数修饰成一个 celery task 对象

