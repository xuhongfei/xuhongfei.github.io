---
layout:     post
title:      介绍 Swift Combine
date:       2020-09-18 09:00:00 +0800
categories: origin
---

WWDC 2019 Session 722  
[Introducting Combine](https://developer.apple.com/videos/play/wwdc2019/722/)

# 异步编程

## Asynchronous Interfaces
    Target/Action  
    Notification Center  
    URLSession  
    Key-value observing  
    Ad-hoc callbacks  

# 介绍 Combine
    A unified, declarative API for processing values over time.

根据时间处理值的统一的声明式API。

## Combine Feature

### Generic
Combine 是由 Swift 编写的框架，也是为 Swift 而写。可以利用像范型这样的 Swift 语言特性。

### Type safe
Combine 可以在编译时就能捕获错误而不是运行时。
    
### Composition first
Combine 的主要设计点是组合优先。这意味着核心概念是简单易懂的。
    
### Request driven
Combine 是请求驱动的，能让你更加仔细地管理app的内存使用情况。

# Combine 核心概念
## Publishers
描述了值和错误是如何产生的。

```swift
public protocol Publisher {
    associatedtype Output
    associatedtype Failure : Error

    public func subscribe<S>(_ subscriber: S) where S.Input == Output, S.Failure = Failure
}
```

Publisher 可以适配到现有的很多异步操作接口中：

`NotificationCenter` 适配 Publisher。
```swift
extension NotificationCenter {
    struct Publisher: Combine.Publisher {
        typealias Output = Notification
        typealias Failure = Never
        init(center: NotificationCenter, name: Notification.Name, object: Any? = nil)
    }
}
```

## Subscribers
与 Publisher 相对应，用于接收值。Subscribers 在接收到值后通常会执行并改变现有状况。

```swift
public protocol Subscriber : CustomCombineIdentifierConvertible {
    associatedtype Input
    associatedtype Failure : Error

    func receive(subscription: Subscription)
    func receive(_ input: Input) -> Subscribers.Demand
    func receive(completion: Subscribers.Completion<Failure>)
}
```

Subscriber 提供三种能力：  
- 接收订阅  
- 接收输入  
- 接收来自完成或故障的完成程序  

Subscriber `Assign`
```swift
extension Subscribers {
    final public class Assign<Root, Input>: Subscriber, Cancellable {
        public typealias Failure = Never
        public init(object: Root, keyPath: ReferenceWritableKeyPath<Root, Input>)
    }
}
```

## Operators

### The Pattern
![Combine-the-pattern.png]({{ site.url }}/assets/images/2020-09-18/Combine-the-pattern.png)

- Subscriber 附加到 Publisher 上
- Publisher 发布一个订阅消息给 Subscriber
- Subscriber 请求数据
- Publisher 发送数据
- Publisher 发送完成

Publisher 和 Subscriber 之间往往存在数据类型不匹配的情况。需要有 Operator 来作为桥梁，通过定义一些便捷操作，使得 Publisher 和 Subscriber 可以很好的搭配起来，降低他们之间的耦合度，以便更加通用。可以通过一个或多个 Operator 来适配复用的 Publisher 和 Subscriber。

Operator 遵循 Publisher 协议。从上游的 Publisher 处订阅消息，生成新的发布者，通过处理后发送给下游的 Subscriber。

### Map Operator
```swift
extension Publishers {
    struct Map<Upstream: Publisher, Output>: Publisher {
        typealias Failure = Upstream.Failure

        let upstream: Upstream
        let transform: (Upstream.Output) -> Output
    } 
}
```

### Combine Operator API
  
#### Functional transformations - 函数式转换
`map` `flatMap` `filter` `reduce` 等

#### List operations - 列表操作
`first` `drop` `append` 等

#### Error handling  - 错误处理
`catch` `retry` 等

#### Thread or queue movement  - 线程/队列移动
`subscribeOn` `reveiveOn` 等

#### Scheduling and time - 调度和时间
`delay` `debounce` `throttle` 等

# Combine 设计理念
**优先使用组合**

对应 Combine 的设计思想到同步编程就是下图所表达的：
![Combine-composition-first.png]({{ site.url }}/assets/images/2020-09-18/Combine-composition-first.png)

Publisher 可以用的 Operator ，大部分和 Foundation 框架中的 Collection 类型的操作符类似。通过链式方法，组合操作符，让异步操作类似同步操作。

## 组合多个 Publisher
`Zip` 和 `CombineLastest`

### Zip (Zip3 / Zip4)

当组合的每一个 Publisher 都产生值的时候，才会将值合并成元组发送给 Subscriber 。

![Combine-zip.png]({{ site.url }}/assets/images/2020-09-18/Combine-zip.png)

### CombineLastest (CombineLastest3 / CombineLastest4)

当多个 Publisher 中任意一个 Publisher 产生值时，都会执行 transform 闭包的操作并将结果发送给 Subscriber 。

![Combine-combineLatest.png]({{ site.url }}/assets/images/2020-09-18/Combine-combineLatest.png)