---
title: Avoid red blended with cornerRadius
draft: false
tags:
  - develop
  - UI
  - performance
---
 ![[Pasted image 20250220224224.png]]

Для того, чтобы увеличить перформанс и избегаать red blended, необходимо сделать вызов не так:
```swift
imageView.layer.cornerRadius = 150.0
imageView.layer.maskedToBounds = true
```

А необходимо создать контейнер и в контейнере задать скругление. 

```swift
containerView.addSubviews(imageView)
containerView.layer.cornerRadius = 150.0
containerView.layer.maskedToBounds = true
```

Таким образом не будет подсвечивания

Источник: https://www.youtube.com/watch?v=k81HRmMZLXc

