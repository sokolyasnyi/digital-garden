---
title: Использование адаптера для улучшения тестирования закрытых фреймворков или API
draft: false
tags:
  - develop
  - pattern
  - adapter
  - testing
  - appleID
---
 
```swift
protocol AuthController {
    func authenticate()

}


**class** AuthAppleIDController {

    **func** authenticate(_ controller: ASAuthorizationController) {

        controller.delegate = **nil**

        controller.performRequests()

    }

}

  

**final** **class** AuthAppleIDAdapterAuthController: AuthController {

    **private** **let** controller: AuthAppleIDController

    **init**(controller: AuthAppleIDController) {

        **self**.controller = controller

    }

  

    **func** authenticate() {

        **let** request = makeRequest()

        **let** authController = ASAuthorizationController(authorizationRequests: [request])

        controller.authenticate(authController)

  

    }

  

    **private** **func** makeRequest() -> ASAuthorizationRequest {

        **let** request = ASAuthorizationAppleIDProvider().createRequest()

        request.requestedScopes = [.email, .fullName]

        request.nonce = UUID().uuidString

        **return** request

  

    }

}
```


Тесты: 
```swift

```

Source: https://disk.yandex.com/d/hDD876oIM9273g/%5Bessentialdeveloper.com%5D/iOS%20Dev%20Mentoring%20Sessions/012_Andrey_Chirkov_Sign_in_with_Apple_Firebase_Combine.mp4
