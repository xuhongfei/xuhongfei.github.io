---
layout:     post
title:      Swift & OC -- Optional
date:       2020-08-14 15:30:00 +0800
categories: original
---

## Optional可以解决的问题

### 潜在crash问题

```
[dict setObject: obj forKey: @"key"];
```
当obj为nil时，运行到此直接crash。利用optional可以在编译时发现此bug。

### 空类型不详问题

空值和空得以更清晰的表达

## OC & swift 中定义 Optional

![OC&Swift_optional_compare.webp]({{ site.url }}/assets/images/2020-08-14/OC&Swift_optional_compare.webp)


## OC optional
OC本身并不支持optional，只不过时编译器在编译期间做了检查，会提出警告，可以编译通过，但运行时会crash。Foundation提供了一对宏定义，包在内部的对象默认加上`nonnull`，可以使用`nullable`单独指出相应的对象。

```ObjC
NS_ASSUME_NONNULL_BEGIN

@interface ExampleObject : NSObject

- (nullable MyObject*)objectWithName: (NSString *)name;
- (NSInteger)indexOfObject: (MyObject *)myObject;

@property (copy, nullable) NSString *name;
@property (copy, readonly) NSArray *allObjects;

@end

NS_ASSUME_NONNULL_END
```

## Swift optional
而在Swift中非Optional类型传入了nil，编译器会报错。

请参照[https://docs.swift.org/swift-book/LanguageGuide/TheBasics.html](https://docs.swift.org/swift-book/LanguageGuide/TheBasics.html)
