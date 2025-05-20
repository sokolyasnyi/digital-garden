---
title: Disable launch app with AppDelegate
draft: false
tags:
---
```swift
let appDelegateClass: AnyClass = NSClassFromString("TestingAppDelegate") ?? AppDelegate.self
```


```swift
@objc(TestingAppDelegate) class TestingAppDelegate: UIResponder, UIApplicationDelegate { var window: UIWindow? func applicationDidFinishLaunching(_ application: UIApplication) { window = nil } }
```

```swift
import UIKit let appDelegateClass = AppDelegate.self UIApplicationMain( CommandLine.argc, CommandLine.unsafeArgv, nil, NSStringFromClass(appDelegateClass) )
```

Source: https://qualitycoding.org/ios-app-delegate-testing/
