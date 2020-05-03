# SwiftUI

被 @ 符号修饰的变量，直接访问可以访问到原始值，如果想要访问被包装的值，则需要用 $ 修饰，比如

```swift
@State var test: String
```

直接使用 `test` 可以访问到` String` 类型

如果使用 `$test` 则可以访问 `Binding<String>` 类型。

具体可以查看 propertyWrapper

## ViewModel 的概念

希望 UI 上需要显示的内容能和某个类型的属性一一对应，这种驱动 View 的 Model，称为 ViewModel

# Combine-and-Async

## 总结

Combine 中最核心的两个角色：`Publisher` 和 `Operator`。Combine 编程中开发者的核心工作就是组合各种 `Operator` 来生成满足最终逻辑的 `Publisher`。

## 传统的异步 API

- 闭包回调
- 最简单的异步回调，只关注结果，不关注过程状态

- Delegate
- 可以获取详细的过程状态

- Notification
- 主要用来处理不确定的长期行为，比如 EventBus

## 响应式异步编程模型

异步其实关注的就是事件，

所以抽象出所有的异步 api，可以找到三个角色：发布者、处理者、接收者

![]()

异步操作在合适的时机发布事件，这些事件带有数据，使用一个或多个操作来处理这些事件以及内部的数据。在末端，使用一个订阅者来“消化”这个事件和数据，并进一步驱动程序的其他部分 (比如 UI 界面) 的运行。

## Combine 基础

![]()

### Publisher

最主要的工作其实有两个：发布新的事件及其数据，以及准备好被 Subscriber 订阅。

publisher 可以发送三种事件

1. 类型为 Output 的新值：代表事件流中出现了新的值
2. 类型为 Failure 的错误：代表事件流中发生了问题，事件流到此终止
3. **完成**事件：表示事件流中所有的元素都已经发布结束，事件流到此终止

我们将最终会终结的事件流称为**有限事件流**，而将不会发出 failure 或者 finished 的事件流称为**无限事件流**。

### Operator

类似 rx 的操作符，构成一个由不同 Publisher 构成的链条。

`Operator` 是 `Publisher` 的 extension 中所提供的一些方法，比如 `flatMap` 和 `map`，他们都作用于 `Publisher`，而且会返回另一个 `Publisher`

### Subscriber

常用的 

- sink
- 提供闭包

- assign
- 直接赋值，摆脱指令写法

### 其他角色

#### Subject

Subject 也是一个 Publisher。提供了将传统指令式异步 API 里的事件和信号转换到响应式的世界中

#### Scheduler

# Publisher

## 基础 Publisher

`Empty`、`Just`、`Sequence`

## Publisher 基本操作

`map`、`reduce`、`scan`、`removeDuplicates`

**reduce: **可以将数组中的元素按照某种规则进行合并，并得到一个最终的结果。

```swift
[1,2,3,4,5].reduce(0, +)
// 15
```

**scan: **将中途的过程保存下来；`scan` 一个最常见的使用场景是在某个下载任务执行期间，接受 `URLSession` 的数据回调，将接收到的数据量做累加来提供一个下载进度条的界面。

**removeDuplicates: **过滤掉重复的事件。

```swift
check("Remove Duplicates") {
    ["S", "Sw", "Sw", "Sw", "Swi", 
    "Swif", "Swift", "Swift", "Swif"]
        .publisher
        .removeDuplicates()
}

// 输出：
// ----- Remove Duplicates -----
// receive subscription: (["S", "Sw", "Swi", "Swif", "Swift", "Swif"])
// request unlimited
// receive value: (S)
// receive value: (Sw)
// receive value: (Swi)
// receive value: (Swif)
// receive value: (Swift)
// receive value: (Swif)
// receive finished
```



## map 的变种

`compactMap` 和 `flatMap`

**compactMap: ** 将 `map` 结果中那些 `nil` 的元素去除掉

```swift
check("Compact Map") {
    ["1", "2", "3", "cat", "5"]
        .publisher
        .compactMap { Int($0) }
}

// 输出：
// ----- Compact Map -----
// receive subscription: ([1, 2, 3, 5])
// request unlimited
// receive value: (1)
// receive value: (2)
// receive value: (3)
// receive value: (5)
// receive finished
```

**flatMap: **`flatMap` 的变形闭包里需要返回一个 `Publisher`。也就是说，`flatMap` 将会涉及两个 `Publisher`：一个是 `flatMap` 操作本身所作用的外层 `Publisher`，一个是 `flatMap` 所接受的变形闭包中返回的内层 `Publisher`。`flatMap` 将外层 `Publisher` 发出的事件中的值传递给内层 `Publisher`，然后汇总内层 `Publisher` 给出的事件输出，作为最终变形后的结果。

举个例子：flatMap 可以将多个层次的数据结构展平

```swift
check("Flat Map 1") {
    [[1, 2, 3], ["a", "b", "c"]].publisher
        .flatMap {
            return $0.publisher
    }
}

// 输出：
// ----- Flat Map 1 -----
// receive subscription: (FlatMap)
// request unlimited
// receive value: (1)
// receive value: (2)
// receive value: (3)
// receive value: (a)
// receive value: (b)
// receive value: (c)
// receive finished
```

## 嵌套的泛型类型和类型摸消

