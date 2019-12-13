# 自定义装饰器

一个简单的自定义装饰器

```python
def test(f):
    def t(*args, **kwargs):
        print('t')
        return f(*args, **kwargs)
    return t

@test
def tt():
    print('tt')

tt()
```

执行这段代码，输出是

```text
t
tt

Process finished with exit code 0
```

很奇怪是吧，明明 test 里面知识声明了一个函数而已，到底是谁调用了`t`这个函数呢

其实

```python
@test
def tt():
    print('tt')
```

相当于 `test(tt)` 执行`tt()`相当于执行了

```python
tt = test(tt)
tt()
```

就是执行了内部定义的`t`函数。

