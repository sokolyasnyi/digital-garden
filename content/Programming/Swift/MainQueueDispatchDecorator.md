---
title: MainQueueDispatchDecorator
draft: false
tags:
  - develop
  - decorator
  - multitreading
---
 ```swift
 final class MainQueueDispatchDecorator<T> {
	private let decoratee: T
	
	init(decoratee: T) {
		self.decoratee = decoratee
	}
	
	func dispatch(completion: @escaping () -> Void) {
		guard Thread.isMainThread else {
			return DispatchQueue.main.async(execute: completion)
		}
		
		completion()
	}
}

extension MainQueueDispatchDecorator: FeedLoader where T == FeedLoader {
	func load(completion: @escaping (FeedLoader.Result) -> Void) {
		decoratee.load { [weak self] result in
			self?.dispatch { completion(result) }
		}
	}
}

extension MainQueueDispatchDecorator: FeedImageDataLoader where T == FeedImageDataLoader {
	func loadImageData(from url: URL, completion: @escaping (FeedImageDataLoader.Result) -> Void) -> FeedImageDataLoaderTask {
		return decoratee.loadImageData(from: url) { [weak self] result in
			self?.dispatch { completion(result) }
		}
	}
}
```


Есть сервис:
```swift
protocol ServiceProtocol {
	func load(wcompletion: @escaping (Result<Void, Error>) -> Void) 
}
final class AsyncService: ServiceProtocol {
	func load(wcompletion: @escaping (Result<Void, Error>) -> Void) {
		DispatchQueue.global().asyncAfter(deadline: .now() + 2) {
			completion(.success(()))
		}
	}
}
```

и я могу использовать декоратор и реализовать протокол
```swift
final class MainQueueDispatchDecorator: ServiceProtocol {
	private let decoratee: ServiceProtocol
	
	init(decoratee: ServiceProtocol) {
		self.decoratee = decoratee
	}
	
	func load(completion: @escaping (Result<Void, Error>) -> Void) {
		decoratee.load { result in 
			guranteeMainThread {
				completion(result)
			}
		}
	}	
}

func guranteeMainThread(_ work: @escaping () -> Void) {
	if Thread.isMainThread {
		work()
	} else {
		DispatchQueue.main.async(execute: work)
	}
}
```


Source: https://github.com/HenestrosaDev/ios-lead-essentials-challenges/blob/main/ui-design-patterns-challenge/MVP%20Module/Source/Feed%20UI/Composers/MainQueueDispatchDecorator.swift
Source: https://www.youtube.com/watch?v=jH7Zfi7TQt8&t=167s

