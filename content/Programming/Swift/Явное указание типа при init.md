---
title: Явное указание типа при init
draft: false
tags:
  - develop
  - swiftlint
  - rule
  - optimize
  - xcode
  - compile
---

The [explicit_init](https://realm.github.io/SwiftLint/explicit_init.html) and [explicit_type_interface](https://realm.github.io/SwiftLint/explicit_type_interface.html) rules can indeed help streamline your code and potentially reduce build times. Ensuring clarity in your code’s initialization and type interfaces can prevent unnecessary ambiguity that might slow down the build process.

```
 init_with_name:
    name: "Init With Name"
    message: "Prefer let object = Class() instead of let object: Class = .init()"
    included: ".*.swift"
    regex: '(?<!self|super)\.init\('
    match_kinds:
      - identifier
      - keyword
    severity: warning
```
Source: https://manu.show/2023-08-18-improve-build-times-in-spm-packages-and-in-your-apps/#improve-compile-time-in-xcode-projects