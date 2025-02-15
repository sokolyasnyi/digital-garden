---
title: Показ preview для UI
draft: false
tags:
  - develop
  - UI
---
Показ preview до ios 17
```swift
struct DemoViewController_Previews: PreviewProvider {
	static var previews: some View {
	DemoViewControllerRepresentable().edgesIgnoringSafeArea(.vertical)

   }
}
```