# SwiftUI

## PropertyWrapper

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

### zip

```swift
zip([1, 2, 3, 4, 5], ["A", "B", "C", "D"])
// [(1, "A"), (2, "B"), (3, "C"), (4, "D")]
```

`zip` 经常被用在合并多个异步事件的结果，比如同时发出了多个网络请求，希望在它们**全部完成**的时候把结果合并在一起。

### combineLatest

不论是哪个输入 `Publisher`，只要发生了新的事件，`combineLatest` 就把新发生的事件值和另一个 `Publisher` 中的最新值合并。

在实践中，`combineLatest` 被用来处理多个可变状态，在其中某一个状态发生变化时，获取这些全部状态的最新值。比如你的 UI 上有多个 TextField，你想要在其中某一个值变动时获取到所有 TextField 中的值进行检查 (没错，我说的就是用户注册)。

---

对于 `zip` 和 `combineLatest`，它们有一个共同特点，那就是结合后的新 `Publisher` 所发出的是数据的多元组。对这两种操作，一种常见的模式是将结果的发出多元组数据的 `Publisher` 沿着响应链继续传递，使用我们之前看到过的各类 `Operator` 来获取能实际驱动 UI 和 app 状态的 `Publisher`。

### map 的变种

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


## 操作符熔断

比如将 `map` 操作符进行重写，在编译期间直接将 `map` 操作作用在输入上，而不需要等待每次事件发生时再去操作。这种将操作符的作用时机提前到创建 Publisher 时的方式被称为**操作符熔断**。

## 响应式和指令式的桥梁

下面介绍将 指令式 转化为 响应式 的转换 API

### Future

`Future` 只能提供一次性的 `Publisher`：对于 `promise`，只能发送一个值并让 `Publisher` 正常结束，或者发送一个错误。

对于可能不发送任何一个值，或者会发送两个或者更多的值的情况，Future 就不适用了。这种情况需要使用 `Subject`

### Subject

## Connectable

`ConnectablePublisher` 不同于普通的 `Publisher`，你需要明确地调用 `connect()` 方法，它才会开始发送事件。

---

那当我们不再关心这个事件流的时候，应该本着资源使用的“谁创建，谁释放”的原则，让这个事件流停止发送。

`connect()` 会返回一个 `Cancellable` 值，我们需要在合适的时候调用 `cancel()` 来停止事件流并释放资源。

> 对于普通的 `Publisher`，当 `Failure` 是 `Never` 时，就可以使用 `makeConnectable()` 将它包装为一个 `ConnectablePublisher`。这会使得该 `Publisher` 在等到连接 (调用 `connect()`) 后才开始执行和发布事件。在某些情况下，如果我们希望延迟及控制 `Publisher` 的开始时间，可以使用这个方法。
>
> 对 `ConnectablePublisher` 的对象施加 `autoconnect()` 的话，可以让这个 `ConnectablePublisher` “恢复”为被订阅时自动连接。

## @Published

`@Published` 类似 `@State` 和 `@ObservedObject`。

`@Published` 把一个 class 的属性值转变为 `Publisher`，它同时提供了值的存储和对外的 `Publisher` (通过投影符号 `$` 获取)。在被订阅时，当前值也会被发送给订阅者，它的底层其实就是一个 `CurrentValueSubject`

## 订阅和绑定

### 通过 `sink` 订阅 `Publisher` 事件

## `Publisher` 的引用共享

如果对一个 Publisher 分开做两次变换，比如：

```swift
let p1 = Just(1)
let p2 = p1.map { String($0) }
let p3 = p1.map { String("A\($)")}
```

这样其实是会生成两个 Publisher 的（p2 和 p3）。

这是一个严重的 Bug，如果想要共享同一个 Publisher 的话，需要将初始的 Publisher 转换为引用语言。我们只要在初始的 Publisher 后加上 `share()` 即可。

## Combine 内存管理

满足 `Cancellable` 协议的对象，必须要主动调用 `cancel()` 才能完结，如果没有调用 `cancel` 的话，就会造成内存泄漏。

`Cancellable` 的实现类 `AnyCancelable`，是一个 `class`，有对自身的生命周期进行管理的能力，可以在自己的 `deinit` 中进行 `cancel()`。也就是说，当 AnyCancelable 被释放的时候，它对应的订阅操作也将停止。

这个行为和 Rx 中的 `DisposeBag` 很类似。可以为 Combine 自定义一个类似 `DisposeBag` 类型来管理内存释放。

针对上面 Combine 中常见的内存资源相关的操作，可以总结几条常见的规则和实践：

1. 对于需要 `connect` 的 `Publisher`，在 `connect` 后需要保存返回的 `Cancellable`，并在合适的时候调用 `cancel()` 以结束事件的持续发布。
2. 对于 `sink` 或 `assign` 的返回值，一般将其存储在实例的变量中，等待属性持有者被释放时一同自动取消。不过，你也完全可以在不需要时提前释放这个变量或者明确地调用 `cancel()` 以取消绑定。
3. 对于 1 的情况，也完全可以将 `Cancellable` 作为参数传递给 `AnyCancellable` 的初始化方法，将它包装成为一个可以自动取消的对象。这样一来，1 将被转换为 2 的情况。