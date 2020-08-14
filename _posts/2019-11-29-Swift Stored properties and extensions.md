---
layout:     post
title:      Swift Stored Properties and Extensions
date:       2019-11-29 17:30:00 +0800
categories: original
---

# 概述
通常我们都会遇到在 `swift extension` 中增加 `stored properties` 的问题。

    “Extensions may not contain stored properties.”

在OC中，基于 `objc_getAssociatedObject` 和 `objc_setAssociatedObject` 方法，使用runtime知识能方便的解决这个问题。当然swift中也可以依赖这样的方式来处理这个问题，但这并不swift。

```swift
extension UIView {
    struct Holder {
        static var _computedPropertyKey: UInt8 = 0
    }
    var computedProperty: Bool {
        get {
            guard let value = objc_getAssociatedObject(self, &Holder._computedPropertyKey) as? Bool else {
                return false
            }
            return value
        }
        set(newValue) {
            objc_setAssociatedObject(self, &Holder._computedPropertyKey, newValue, objc_AssociationPolicy.OBJC_ASSOCIATION_RETAIN_NONATOMIC)
        }
    }
}
```

# 方案1
可能比较容易想到的方式是通过 `computed properties`。因为 `swift extensions` 虽然不支持 `stored properties`， 但却支持 `computed properties`。

```swift
extension UIView {
    struct Holder {
        static var _computedProperty:Bool = false
    }
    var computedProperty:Bool {
        get {
            return Holder._computedProperty
        }
        set(newValue) {
            Holder._computedProperty = newValue
        }
    }
}
```

`struct Holder` 将持有这个私有的值，从而看上去让 `extensions` 拥有了添加存储属性的能力。

# 方案2
就方案1看，存在一个严重的问题：`struct Holder` 存在于私有的访问权限之内， 但是每一个instance都将set/get同一份的 `static var _computedProperty`。

```swift
extension UIView {
    struct Holder {
        static var _computedProperty = [String:Bool]()
    }
    var computedProperty:Bool {
        get {
            return Holder._computedProperty[self.debugDescription] ?? false
        }
        set(newValue) {
            Holder._computedProperty[self.debugDescription] = newValue
        }
    }
}
```

`self.debugDescription` 似乎可以巧妙的用在这里，但存在一个更加简洁的方式来处理。

# 方案3

```swift
extension UIView {
    private static var _computedProperty = [String:Bool]()
    
    var computedProperty:Bool {
        get {
            let tmpAddress = String(format: "%p", unsafeBitCast(self, to: Int.self))
            return UIView._computedProperty[tmpAddress] ?? false
        }
        set(newValue) {
            let tmpAddress = String(format: "%p", unsafeBitCast(self, to: Int.self))
            UIView._computedProperty[tmpAddress] = newValue
        }
    }
}
```

### 链接🔗：
https://medium.com/@valv0/computed-properties-and-extensions-a-pure-swift-approach-64733768112c

https://dzone.com/articles/stored-properties-in-swift-extensions-1?source=post_page-----64733768112c----------------------
