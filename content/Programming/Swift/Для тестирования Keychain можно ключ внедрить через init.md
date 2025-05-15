---
title: Для тестирования Keychain можно ключ внедрить через init
draft: false
tags:
  - develop
  - keychain
  - testing
---
 Если нужно протестировать Keychain, то для того, чтобы продовые данные не мешались с тестовыми, то необходимо внедрить ключ через init. 

```swift
class KeychainTokenStore {

	private let key: String

	init(key: String = "com.sokolysanyi.accessToken") {
		self.key = key
	}
}
```

Source: Essential Developers iOS Dev Mentoring 010 with Danil Lokhin