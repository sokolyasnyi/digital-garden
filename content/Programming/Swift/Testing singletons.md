---
title: Testing singletons
draft: false
tags:
  - develop
  - singletone
  - testing
---
Сейчас нельзя потестировать данный класс.

```swift
class NetworkService {
	static let shared = NetworkService
}
``` 


Но если мы изменим `let` на `var`, то теперь я могу в тестах подменить данную реализацию. 

```swift
class NetworkService {
	static var shared = NetworkService
}
``` 

Но есть опасность, что кто-то в прод версии подменит данный экземпляр. 

```swift
class NetworkService {
	#if DEBUG
	static var shared = NetworkService
	#else
	static let shared = NetworkService
	#endif
}
``` 