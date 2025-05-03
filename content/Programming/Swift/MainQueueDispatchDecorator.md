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

Source: https://github.com/HenestrosaDev/ios-lead-essentials-challenges/blob/main/ui-design-patterns-challenge/MVP%20Module/Source/Feed%20UI/Composers/MainQueueDispatchDecorator.swift
