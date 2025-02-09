---
title: Domain is simple
draft: false
tags:
  - develop
  - DTO
  - Model
---
### Domain is simple

Many things can change from your DTO to your Domain model implementation:

1. 📛 Naming. In your Domain you’re allowed to « fix » naming (for example wrong spelling from the backend)
2. 🧲 Composition/aggregation. You can split your objects into multiple smaller Domain objects. Doing so can greatly improve your code maintenance and avoid [nesting relationships](https://swiftunwrap.com/article/modeling-done-right/).
3. 🦍 Strongly type attributes. Have a finite list of value? Use a enum. A date? Use Date, etc… Strongly typing reduce the numbers of invalid case you could have in your app making it less buggy.
4. ❓Avoid optional properties when not needed. Associated values can be handy

```swift
/// BAD 
/// It's just a 1-1 with DTO with all attributes 
struct User { let id: Int
	let firstname: String
	let lastname: String
	let companyId: Int
	let companyName: String
	let street: String 
	let zipcode: String 
	let country: String
	let preferredLanguage: String
	let darkMode: Bool 
} 
/// BAD 
/// - attributes are nested 
/// - Non useful parameters NOT DEFINING the user identity (useDarkMode, address) 
/// - Some parameters can be unclear purposes (useDarkMode? What's this?) 
struct User: Identifiable { 
	let id: UUID
	let firstname: String
	let lastname: String
	let email: String
	let company: Company
	let useDarkMode: Bool
	let address: Address 
} 

struct Company: Identifiable { 
	let id: UUID
	let name: String
}
```


```swift
/// GOOD 
/// - Very few properties (4)
/// - No nested entities
/// - Everything define the user (it answers the question "what do I need to create a user?") 

struct User: Identifiable { 
	let id: Int 
	let firstname: String 
	let lastname: String 
} 

struct Address { 
	let street: String
	let zipcode: String 
	let country: String 
} 

struct Settings { 
	let preferredLanguage: Locale
	let useDarkMode: Bool 
} 

struct Company { 
	let id: Int 
	let name: String 
} 

// Aggregate 
struct UserAddress { 
	let user: User 
	let address: Address 
} 

// Aggregate 
struct UserSettings { 
	let user: User 
	let address: Address
}
```



Источник: https://swiftunwrap.com/article/data-vs-domain/