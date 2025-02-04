---
title: Показ preview для UI
draft: false
tags:
  - develop
  - UI
---
 
```
struct DemoViewController_Previews: PreviewProvider {
	static var previews: some View {
	DemoViewControllerRepresentable().edgesIgnoringSafeArea(.vertical)

   }
}
```