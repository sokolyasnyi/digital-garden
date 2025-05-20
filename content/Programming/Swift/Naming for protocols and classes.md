---
title: Naming for protocols and classes
draft: false
tags:
  - develop
  - naming
  - protocol
  - class
---

Лучше всего называть вот так:
```swift
protocol UserRepository {
	func getUser(_ id: Int) -> User
}

class CoreDataUserRepository: UserRepository {}
class RealmUserRepository: UserRepository {}
class InMemoryUserRepository: UserRepository {}
```

Можно еще так:
```swift
protocol UserRepository {
	func getUser(_ id: Int) -> User
}

class UserRepositoryImpl: UserRepository {}
```

Или вот так

```swift
protocol IUserRepository {
	func getUser(_ id: Int) -> User
}

class UserRepository: IUserRepository {}
```

Source: https://www.youtube.com/watch?app=desktop&v=Xj_IZFmty6g&list=PLyjgjmI1UzlRGYfvsHRjhDnKyEj2v3m9w&index=22