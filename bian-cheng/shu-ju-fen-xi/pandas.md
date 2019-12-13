# Pandas

## DataFrame

### 过滤

#### The truth value of a {0} is ambiguous.

如果按照 Python 的语法来写判断条件，就会导致这个问题，比如：

```python
df[(df.A > 0) and (1 > df.A)]
```

正确的写法是这样的：

```python
df[(df.A > 0) & (1 > df.A)]
```

