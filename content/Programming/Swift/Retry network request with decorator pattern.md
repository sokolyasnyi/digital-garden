---
title: Retry network request with decorator pattern
draft: false
tags:
  - develop
  - pattern
  - decorator
  - network
---
Для того, чтобы реализовать поведение повторных запросов необходимо обернуть `ApiClient` в декоратор с данным поведением.

```swift

class RetryApiClientDecorator: HTTPClient {
    private let decoratee: APIClient
    private let maxRetries: Int

    init(decoratee: APIClient, maxRetries: Int) {
        self.decoratee = decoratee
        self.maxRetries = maxRetries
    }

    func request(url: String, completion: @escaping (Result<Data?, any Error>) -> Void) {
        request(url: url, retriesLeft: maxRetries, completion: completion)
    }

    private func request(url: String, retriesLeft: Int, completion: @escaping (Result<Data?, any Error>) -> Void) {
        decoratee.request(url: url) { [weak self] result in
            switch result {
            case .success(let data):
                completion(.success(data))
            case .failure(let error):
                if retriesLeft > 0 {
                    print("Request failed, retrying... (\(retriesLeft) retries left)")
                    self?.request(url: url, retriesLeft: retriesLeft - 1, completion: completion)
                } else {
                    completion(.failure(error))
                }
            }
        }
    }
}

  

protocol HTTPClient {
    func request(url: String, completion: @escaping (Result<Data?, Error>) -> Void)
}

class APIClient: HTTPClient {
    private let networkService: NetworkService
    
    init(networkService: NetworkService) {
        self.networkService = networkService
    }

    func request(url: String, completion: @escaping (Result<Data?, Error>) -> Void) {
        let url = URL(string: url)!
        let task = networkService.dataTask(with: url) { (data, response, error) in
            if let error = error {
                completion(.failure(error))
            }

            if let data = data {
                completion(.success(data))
            }
        }
        task.resume()
    }
}

protocol NetworkServiceProtocol {
    func dataTask(with url: URL, completionHandler: @escaping (Data?, URLResponse?, Error?) -> Void) -> URLSessionDataTask
}

class NetworkService: NetworkServiceProtocol {
    private let urlSession: URLSession

    init(urlSession: URLSession = .shared) {
        self.urlSession = urlSession
    }

    func dataTask(with url: URL, completionHandler: @escaping (Data?, URLResponse?, Error?) -> Void) -> URLSessionDataTask {
        return urlSession.dataTask(with: url) { data, response, error in
            completionHandler(data, response, error)
        }
    }
}
```



И чтобы использовать это мы передаем при создании сервисов объект оборачивая его в декоратор. 

```swift
     let networkService = NetworkService()
     let apiClient = APIClient(networkService: networkService)
     let retryApiClient = RetryApiClientDecorator(decoratee: apiClient, maxRetries: 3)
     
	 retryApiClient.request(url: "https://jsonplaceholsder.typicode.com/posts/2/comments") { result in
            switch result {
            case .success(let data):
                break
            case .failure(let error):
                print("Error:", error)
            }
        }
```


Source: https://medium.com/@tushar.sharma0214/title-implementing-http-retry-strategy-in-swift-using-the-decorator-pattern-9f5ec4facf00
