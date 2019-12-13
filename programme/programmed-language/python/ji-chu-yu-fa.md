# 基础语法



> 最近需要弄一点机器学习，学点 Python

Python 是一门动态语言，言下之意就是弱类型

## 基本类型

### 布尔值

布尔值为首字母大写 `True` `False`

#### 布尔值运算

与或非

`and` `or` `not`

## list 和 tuple

### list 就是普通的 list

* 初始化
  * ```python
    test = ['1', '2', '3']
    ```
* 相当于没有泛型的 list，什么都能放
* 索引从0开始，负值索引代表倒序：-1，倒数第一个
* 访问 `test[1]`
* 追加 `test.append('11')`
* 插入 `test.insert('231')`
* 移除末尾的元素 `test.pop()`
* 移除指定位置的元素 `test.pop(3)`
* 替换 `test[1] = "333"`

### tuple 叫元组，和 list 十分相似，但是一旦初始化就不能改变

不知容量不能改变，元素也不能改变

* 初始化 `tuple("11", "33")`

### 不同

两者声明的时候一个是 `[]`, 一个是 `()`

## 逻辑判断

```python
age = 3
if age >+ 18:
    print('adult')
elif age >= 6:
    print('teenager')
else:
    print('kid')
```

即：

```python
if <条件判断1>:
    <执行1>
elif <条件判断2>:
    <执行2>
elif <条件判断3>:
    <执行3>
else:
    <执行4>
```

`elif` 是 `else if` 的缩写

### 简写

```python
if x:
    print('True')
```

只要`x`是非零数值、非空字符串、非空list等，就判断为`True`，否则为`False`。

## 循环

两种循环：`for` `while`

### for

for 循环是 for in 格式，`Kotlin`就是这种格式

```python
names = ["Danny", "Bob"]
for name in names:
    prinf(name)
```

所以`for x in ...`循环就是把每个元素代入变量`x`，然后执行缩进块的语句。

还有一种情况，我们需要循环 n 次

```python
for i in range(10):
    print(i)
```

`range(n)`可以生成一个 n-1的整数序列

说到这里就想起 **Kotlin** 里

是这样的

```kotlin
for(i in 0 until 10) {
  // 0 ~ 9
}
for(i in 0..10) {
  // 0~10
}
```

### while 循环

就是普通的 while 循环了

## 接口

### enumerate\(\)

enumerate 是 python 的内置函数

enumerate 的含义是枚举、列举的意思

对于一个可迭代的/可遍历的对象，enumerate 将其组成一个索引序列，利用它可以同时获取索引值和值

enumerate 多用于在 for 循环中得到计数

```python
for index, item in enumerate(items):
    print('current is %d line, value is %s.' % (index, item))
```

## 函数式编程

### 高阶函数

> * 变量可以指向函数
> * 函数名也是变量
>   * 函数名其实就是指向函数的变量
> * 传入函数
>   * 变量可以接受函数，函数的参数也能接受函数

#### map/reduce

**map**

函数接收两个参数，一个是`函数`，一个是 `Iterable`，`map`将传入的函数依次作用到序列的每个元素，并把结果作为新的`Iterator`返回。 eg: 有一个函数 f\(x\)=x^2，将这个函数作用到 list`[1, 2, 3, 4, 5, 6, 7, 8, 9]`

```python
def f(x):
    return x * x
r = map(f, [1, 2, 3, 4, 5, 6, 7, 8, 9])
print(list(r))
```

**reduce**

reduce 把一个函数作用在一个序列上，这个函数必须接收两个函数，reduce 把结果继续和序列的下一个元素做累计运算 eg:

```python
reduce(f, [x1, x2, x3]) = f(f(x1, x2), x3)
```

#### filter

#### sorted

### 返回函数

### 匿名函数

### 装饰器

### 偏函数

## with 语句

语法格式

```python
with context_expression [as target(s)]:
    with-body
```

contextexpression 返回一个上下文管理器对象，该对象并不赋给 target，如果指定了 target，会将上下文管理器的 `_enter()` 方法的返回值赋给 target，target 可以是单个变量，也可以是元组。 使用 with 语句，不管在代码过程中是否发生异常，都能保证 with 语句执行完毕后已经关闭了打开的文件句柄。

with 语句执行过程 

1. 执行 contextexpression，生成上下文管理器 contextmanager 
2. 调用上下文管理器的enter\(\)方法；如果使用了 as 子句，则将enter\(\)的返回值赋值给 as 子句中的 target 
3. 执行语句体 with-body 
4. 不管执行过程中是否出现异常，执行上下文管理器的exit\(\)方法，exit\(\)方法负责执行清理工作。 
5. 出现异常时，如果\_\_exit\(type, value, traceback\)返回 False，则会重新抛出异常，让 with 之外的语句逻辑来处理异常，这也是通用做法；如果返回 True，则忽略异常。

