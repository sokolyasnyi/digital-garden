---
title: Disable launch app for SwiftUI
draft: false
tags:
  - develop
  - swiftui
  - testing
---
 ```swift
 struct TestApp: App {
	 var body: some Scene {
		 WindowGroup {
			 Text("I'm running tests")
		 }
	 }
 }
 
 @main
 struct TestDriver {
	 static func main() {
		 if NSClassFromString("XCTestCase") != nil {
			 TestApp.main()
		 } else {
			 MyRealApp.main()
		 }
	 }
 }
```

Source: https://www.youtube.com/watch?v=FvAP2mdUGaw&list=PL-NrXVEjQ-o8N2opeVDjRFNeXp588C35H&index=3
https://qualitycoding.org/bypass-swiftui-app-launch-unit-testing/