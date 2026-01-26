---
title: Переопределение Back Button для Coordinator
draft: false
tags:
  - develop
  - pattern
  - coordinator
  - navigation
  - back
---

// Source - https://stackoverflow.com/a
// Posted by Umair, modified by community. See post 'Timeline' for change history
// Retrieved 2025-12-05, License - CC BY-SA 4.0
```swift
override func viewWillDisappear(_ animated: Bool) {
        if self.isMovingFromParent {
            //View Controller Popped
        } else {
            //New view controller pushed
        }
       super.viewWillDisappear(animated)
    }
```
