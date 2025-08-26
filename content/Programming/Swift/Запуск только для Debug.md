---
title: Untitled
draft: false
tags:
  - develop
  - xcode
  - optimize
---
 

### Only run a build phase if needed

If you have a build script you want to run only for debugging or release builds, you can include the configuration check:

![Build phase optimization by only running for debug builds.](https://www.avanderlee.com/wp-content/uploads/2018/07/build_phase_optimization-1024x241.png)

Build phase optimization by only running for debug builds.

In this case, we’re only running the SwiftLint script for debug builds. You can obviously do the same by checking for “Release” builds if you want to run a script only for release builds.

Source: https://www.avanderlee.com/optimization/analysing-build-performance-xcode/

