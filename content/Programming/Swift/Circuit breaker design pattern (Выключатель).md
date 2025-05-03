---
title: Circuit breaker design pattern
draft: false
tags:
  - develop
  - pattern
---
 Это автоматический выключатель сервисов, если они определенное количество раз получают ошибку. 
```swift
```
```swift
enum CircuitBreakerState {
    case closed
    case open
    case halfOpen
}

class CircuitBreaker {
    private var state: CircuitBreakerState = .closed
    private var failuresCount = 0
    private let maxFailuresThreshold = 3
    
    func execute(service: () throws -> Void) {
        do {
            switch state {
            case .closed, .halfOpen:
                try service()
                state = .closed
                failuresCount = 0
            case .open:
                throw CircuitBreakerError.openState
            }
        } catch {
            handleFailure()
            throw error
        }
    }
    
    private func handleFailure() {
        failuresCount += 1
        
        if failuresCount >= maxFailuresThreshold {
            state = .open
            // Start a timer to transition to half-open state after a certain time interval
            DispatchQueue.main.asyncAfter(deadline: .now() + 5) {
                self.state = .halfOpen
            }
        }
    }
}

enum CircuitBreakerError: Error {
    case openState
}
```

И вот так его можно использовать 
```swift
let circuitBreaker = CircuitBreaker()

do {
    try circuitBreaker.execute {
        // Perform service call here
        // ...
        // Throw an error if the call fails
    }
} catch {
    // Handle the error or fallback response
    // ...
}
```


Source: https://colinchswift.github.io/2023-10-26/10-15-05-565920-circuit-breaker-design-pattern-with-swift/