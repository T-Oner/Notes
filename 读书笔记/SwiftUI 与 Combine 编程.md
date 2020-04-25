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

类似 rx 的操作符，构成一个由不同 Publisher 构成的链条

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