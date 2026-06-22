---
title: Replace with regex in Xcode
draft: false
tags:
  - develop
  - regex
  - xcode
---
 find: `class (.*)Tests` - найдет все тесты
 replace: ```
 ```
 @MainActor
 class $1Tests
 ``` 
Source: Complete Swift Concurrency Checking