---
title: Add init with closure
draft: false
tags:
  - develop
  - init
  - DI
---
Внедряем зависимость через closure.

```
class DataManager { 

static let shared = DataManager()

typealias DirectionFetcher = (HYCPlacemark, HYCPlacemark, @escaping (Result<[MKRoute], Error>) -> ()) -> Void)
private let directionFetcher: DirectionFetcher

init(directionFetcher: @escaping DirectionFetcher = MapManager.calculateDirections) {
self.directionFetcher = directionFetcher
}
}`
```


Источник: https://www.youtube.com/watch?v=9_YD9L4ogSo
