# Coverity 编程质量

## IO

### 编码

StreamReader 需要指定编码格式

## 运算

### 溢出

当两个 int 值相乘，又把结果赋值给 long 类型，此时可能会数据溢出。 因为两个 int 相乘得出的结果还是 int，此时转为 long 值数据已经溢出了。 **解决方案**

> 将其中一个因数转为 long 类型即可，这样算出来的结果就是 long 的，不会溢出

## 效率问题

### Map 遍历

### FB.WMI\_WRONG\_MAP\_ITERATOR

`WMI：无效 Map Iterator (FB.WMI_WRONG_MAP_ITERATOR)`

这个问题是因为遍历 Map 的时候采用了低效的遍历方法：先拿 keySet，然后遍历 keySet 去取 value。

实际上有更高效的方法：

```java
for (Map.Entry<String, String> keyEntry : keys.entrySet()) {
    bundle.putString(keyEntry.getKey(), keyEntry.getValue());
}
```

直接使用 `entrySet` 接口

