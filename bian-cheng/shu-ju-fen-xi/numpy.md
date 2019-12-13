# Numpy

## tile 函数

tile 函数签名`tile(A, reps)`，A 几乎所有类型都可以：array, list, tuple, dict, matrix 以及基本数据类型。reps 的类型也很多，可以是 tuple, list, dict, array, int, bool,但不可以是 float, string, matrix 类型 这个函数是创建一个以 A 为元素，reps 为模板的数据类型。

```python
>>> from numpy import *
>>> tile(False, True)
array([False])
>>> tile(False, False)
array([], dtype=bool)
>>> tile(True, False)
array([], dtype=bool)
>>> tile(True, True)
array([ True])
>>> tile(True, [1, 2])
array([[ True,  True]])
>>> tile(True, [2, 2])
array([[ True,  True],
       [ True,  True]])
>>> tile(True, 3)
array([ True,  True,  True])
```

## shape 函数

shape 函数式读取矩阵的长度，比如 shape\[0\]就是读取矩阵第一维度的长度，他的输入参数可以是一个整数表示维度，也可以是一个矩阵

```python
>>> b = eye(3)
>>> b
array([[1., 0., 0.],
       [0., 1., 0.],
       [0., 0., 1.]])
>>> b.shape
(3, 3)
>>> b.shape[0]
3
>>>
```

