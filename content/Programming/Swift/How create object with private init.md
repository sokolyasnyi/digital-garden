---
title: How create object with private init
draft: false
tags:
  - develop
  - objc
  - init
  - private
  - create
---

```swift
func test() throws {
	let sut = ConnectionHandler()
	
	let pClass = NSClassFromString("CBPeripheral") as? NSObject.Type
	let p = try XCTUnwrap(pClass?.init() as? CBPeripheral)
	
	sut.centralManager(CBCentralManager), didConnect: p)
}
```

Source: https://youtu.be/QhEzbCXjjwg
