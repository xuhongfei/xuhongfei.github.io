---
layout:     post
title:      如何定位AutoLayout有冲突的约束
date:       2019-08-16 10:50:00 +0800
categories: original
---

# 概述
关于Auto Layout，常见的问题是很难进行Debug。经常会出现约束冲突，尽管不影响视图的展示，但是会出现一堆的警告信息。似乎没有很好的办法将这些警告信息定位到具体的代码中。常见冲突如下：
```swift
view.widthAnchor.constraint(equalToConstant: 40).isActive = true  
view.widthAnchor.constraint(equalToConstant: 60).isActive = true
```

警告信息：
```
Will attempt to recover by breaking constraint 

......

Make a symbolic breakpoint at UIViewAlertForUnsatisfiableConstraints to catch this in the debugger.
The methods in the UIConstraintBasedLayoutDebugging category on UIView listed in <UIKitCore/UIView.h> may also be helpful.
```

# 定位并打印信息
 从警告信息中可以看出，设置一个`symbolic breakpoint`标记为`UIViewAlertForUnsatisfiableConstraints`可以获得更多信息。

![图1](https://raw.githubusercontent.com/xuhongfei/xuhongfei.github.io/master/_images/2019_08_16_001.png)

![图2](https://raw.githubusercontent.com/xuhongfei/xuhongfei.github.io/master/_images/2019_08_16_002.png)