---
layout:     post
title:      XCUITests-Test Listeners
date:       2020-09-02 16:50:00 +0800
categories: reprint
---

转载自【 https://medium.com/@nshthshah/xcuitests-test-listeners-f09cdb35164b 】

# Test Listeners
在`XCUITests`中，listeners就是Obervers。iOS提供给开发者`XCTestObservation` protocol 来实现，以帮助开发者获得test run过程中的回调。允许开发者自定义`XCUITests`报告和log。  

![XCTests-Linstener.png]({{ site.url }}/assets/images/2020-09-02/XCTests-Linstener.png)

# XCTestObservation
实现`XCTestObservation`协议可以注册后获得测试运行进度的回调。需要在`XCTestObservationCenter`中注册。

`XCTestObservation`协议中的Progress Events:
```swift
 -testBundleWillStart:  // exactly once per test bundle
    -testSuiteWillStart:    // exactly once per test suite
        -testCaseWillStart: // exactly once per test case
        -testCase:didFailWithDescription:...    // zero or more times per test case, any time between test case start and finish
        -testCaseDidFinish: // exactly once per test case
    -testSuite:didFailWithDescription:...   // zero or more times per test suite, any time between test suite start and finish
    -testSuiteDidFinish:    // exactly once per test suite
-testBundleDidFinish:   // exactly once per test bundle
 ```

# 使用

## TestObserver
```swift
import XCTestpublic 
class TestObserver: XCTestObservation {    
    func testBundleWillStart(_ testBundle: Bundle) {}
    func testSuiteWillStart(_ testSuite: XCTestSuite) {}
    func testCaseWillStart(_ testCase: XCTestCase) {}
    func testCase(_ testCase: XCTestCase, didFailWithDescription description: String, inFile filePath: String?, atLine lineNumber: UInt) {}
    func testCaseDidFinish(_ testCase: XCTestCase) {}
    func testSuiteDidFinish(_ testSuite: XCTestSuite) {}
    func testBundleDidFinish(_ testBundle: Bundle) {}
}
```

# 添加`TestObserver`
可以通过`XCTestObservationCenter`类中的`addTestObserver(_:)`方法注册Observer，以及`removeTestObserver(_:)`来移出Observer。并且可以注册多个Observer。但是要记得移除所有的Observer。


如果在test bundle的Info.plist中声明了`NSPrincipalClass`键。当加载测试包时，XCTest会自动创建该类的一个实例。可以使用此实例作为在开始对该包进行测试之前注册观察者或进行其他预测试全局设置的地方。

    Observer必须手动注册，`NSPrincipalClass`实例并不会自动注册成为test run的观察者，尽管这个类已经实现了`XCTestObservation`协议。


# 注册Observer

## `Info.plist`中添加`NSPrincipalClass`
在test target中，找到`Info.plist`文件，添加`Principal class`或者`NSPrincipalClass` key, value可以取值：`$(PRODUCT_NAME).TestObserver`。

![XCUITests-1.png]({{ site.url }}/assets/images/2020-09-02/XCUITests-1.png)

注意！注意！注意！
但是很多时候上面👆的方式并不能正确的添加`NSPrincipalClass`。

另外的方式：
- 在`Build Settings`中找到`Product Module Name` （testUITests_A）
![XCUITests-2.png]({{ site.url }}/assets/images/2020-09-02/XCUITests-2.png)

- 在Info.plist中，添加`testUITests_A.TestObserver`

![XCUITests-3.png]({{ site.url }}/assets/images/2020-09-02/XCUITests-3.png)

## 创建 `TestObserver`
``` swift
class TestObserver: NSObject, XCTestObservation {
     // This init is called first thing as the test bundle starts up and before any test 
     // initialization happens    
    override init() {
        super.init()
        // We don't need to do any real work, other than register for callbacks
        // when the test suite progresses.
        // XCTestObservation keeps a strong reference to observers
        XCTestObservationCenter.shared.addTestObserver(self)
    }    
    
    func testBundleDidFinish(_ testBundle: Bundle) {
        XCTestObservationCenter.shared.removeTestObserver(self)
    }
}
```

# Refer

[https://medium.com/@nshthshah/xcuitests-test-listeners-f09cdb35164b]https://medium.com/@nshthshah/xcuitests-test-listeners-f09cdb35164b

[https://www.iosdev.recipes/os-signpost/]https://www.iosdev.recipes/os-signpost/

[https://developer.apple.com/documentation/bundleresources/information_property_list/nsprincipalclass]https://developer.apple.com/documentation/bundleresources/information_property_list/nsprincipalclass

[https://developer.apple.com/documentation/xctest/xctestobservation]https://developer.apple.com/documentation/xctest/xctestobservation

[https://developer.apple.com/documentation/foundation/bundle/1409048-principalclass]https://developer.apple.com/documentation/foundation/bundle/1409048-principalclass