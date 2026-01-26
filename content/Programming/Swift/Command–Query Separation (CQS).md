---
title: Command–Query Separation (CQS)
draft: false
tags:
  - develop
  - principle
---
 Command-Query Separation is a programming principle that can help you identify functions/methods that do too much. The idea is simple:

A _Query_ **should only** **return a result and should not have side-effects** (does not change the observable state of the system).

A _Command_ **changes the state of a system (side-effects) but does not return a value**.

“Command” is a generic name used in many contexts, so some people prefer to use “Modifier” instead. _Query-Modifier Separation_. A simple way of looking at it is that of Getters (Query) and Setters (Command/Modifier). For example, `customer.name` is a **getter** and should not change the state of the `Customer` instance. `customer.name = “Mary”` is a **setter** so it should change the state and not return a result.

In our case, asking for the cached feed (query) should **not also delete** an invalid cache (side-effect: altering the state of the system). Instead, we can break this task into two: `Load Cache` (query) and `Invalidate Cache If Needed` (command).

_Command-Query Separation_ is a principle that will help you uncover bloated functions/methods and guide you towards simpler code. However, like all principles, it doesn’t fit all contexts (no silver-bullets). It’s common to see commands/modifiers that, for a good reason, return a result. For example, Swift’s Standard Library `remove(at index: Int)` [method for mutable collections](https://developer.apple.com/documentation/swift/rangereplaceablecollection/3018268-remove) does not comply with the CQS principle because it returns the removed object. But for a good reason: it leads to simpler and more idiomatic code!

Source: Essential Developer Academy Persistence Module Test-driving Cache Invalidation + Identifying Complex (Bloated) Functionality With The Command–Query Separation Principle