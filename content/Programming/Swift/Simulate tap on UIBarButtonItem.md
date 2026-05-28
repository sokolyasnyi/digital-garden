---
title: Simulate tap on UIBarButtonItem
draft: false
tags:
  - develop
  - testing
  - simulate
  - UIBarButtonItem
---
```swift
private extension UIBarButtonItem {
    func simulateTap() {
        target!.performSelector(onMainThread: action!, with: nil, waitUntilDone: true)
    }
}
``` 

Source: Essential Developer Community