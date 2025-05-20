---
title: ASWebAuthenticationSession and ASWebAuthenticationPresentationContextProviding
draft: false
tags:
  - develop
  - delegate
  - auth
  - ASWebAuthenticationPresentationContextProviding
  - ASWebAuthenticationSession
---
При использовании методов атворизации через Web возникает проблема с тем, как передать в `ASWebAuthenticationPresentationContextProviding` в метод `func presentationAnchor(for session: ASWebAuthenticationSession) -> ASPresentationAnchor` какую-то view, как в примере Apple.

Но это делать не обязательно. Можно привзать якорь к любому классу. 

```swift
	func presentationAnchor(
		for session: ASWebAuthenticationSession) -> ASPresentationAnchor { 
	ASPresentationAnchor() 
	}
```


 Source: https://www.andyibanez.com/posts/using-aswebauthenticationaession-swiftui/

