---
title: Create service with fallback
draft: false
tags:
  - develop
  - pattern
---
 
You can create service with fallback in cache
```swift
struct ItemServiceWithFallback: ItemService {
let primary: ItemService
let fallback: ItemService

func loadItems(completion: @escaping (Result<[ItemViewModel], Error>) -> Void) {
	primary.loadItems { result in 
		switch result {
		case .success:
			completion(result)
		case .failure:
			fallback.loadItems(completion: completion)
		}
	}
}
}
```


Источник: https://www.youtube.com/watch?v=XJXbOvyV-VY
Lecture #3 - The fastest and simplest way to become a complete Senior iOS Developer
