---
title: Lazy var for VC
draft: false
tags:
  - develop
  - compositional
---

You can inject action in composition layer before real create viewController


 ```swift
private func makeFriendsList() -> UIViewController {
	weak var FiendsListViewController?

	let isPremium = User.shared?.isPremium == true

	let api = FriendsAPIItemsServiceAdapter(
	api: FriendsAPI.shared
	cache: isPremium ? frindsCache : NullFriendsCache()
	select: { item in 
		lazyVC?.select(friend: item)
		}
	).retry(2)

	let cache = FriendCacheItemsServiceAdapter(
		cache: frindsCache,
		select: { item in 
			lazyVC?.select(friend: item)
		}
	)

let service = isPremium ? api.fallback(cache) : api

let vc = FiendsListViewController(service: service)
lazyVC = vc
 
}
```


Источник: https://www.youtube.com/watch?v=XJXbOvyV-VY
Lecture #3 - The fastest and simplest way to become a complete Senior iOS Developer