---
title: Actors
draft: true
tags:
  - develop
  - actor
  - concurrency
  - multitreading
  - continuations
---
Что мы изучали ранее:

[Процессы, Потоки, Очереди и зачем нужен Grand Central Dispatch](https://www.notion.so/Grand-Central-Dispatch-1a6b8c8f842b816b8a68dcf7f7d32347?pvs=21)

[**GCD: Основная и глобальная очереди, Async vs Sync, обновление UI, частые ошибки**](https://www.notion.so/GCD-Async-vs-Sync-UI-1a6b8c8f842b81b0a205c4bed208d128?pvs=21)

[Swift Concurrency: Основы](https://www.notion.so/Swift-Concurrency-1a6b8c8f842b81aeb548fa002bb2efd5?pvs=21)

В этой статье мы размеберем:

- Что такое Actors?
- Как работают Actors?
- Разбор классических задач
    - Запрос данных в сеть: одиночный запрос данных о юзере
    - Запрос данных с Continuations

## Что такое Actors?

Представь, что у тебя есть много друзей, и каждый из них может делать что-то особенное. Например, один друг умеет рисовать, другой — играть на гитаре, а третий — делать домашние задания. Если каждый из них будет делать свои дела по очереди и не будет мешать друг другу, у вас получится много классных вещей!

**Актеры** в Swift работают похожим образом. Они помогают организовать работу в программе, чтобы разные части могли выполнять свои задачи, не мешая друг другу. Каждый актер отвечает за свою часть работы и управляет своим состоянием.

### Почему это полезно?

Когда несколько частей программы пытаются одновременно изменить одни и те же данные, это может привести к путанице. Актеры помогают избежать такой путаницы, потому что каждый из них работает по очереди.

Актеры в Swift реализуют концепцию безопасного доступа к состоянию. Каждый актер имеет свою собственную очередь обработки, что означает, что операции с его состоянием могут выполняться только последовательно. Это устраняет необходимость в явной синхронизации, такой как использование семафоров или блокировок, что делает код более чистым и менее подверженным ошибкам.

### Как работают актеры?

1. **Изоляция состояния:** Каждое состояние внутри актера изолировано от других потоков. Это значит, что несколько потоков не могут одновременно изменять данные внутри актера, что уменьшает вероятность возникновения гонок данных.
2. **Асинхронные методы:** Методы акторов автоматически становятся асинхронными. Вызовы к ним требуют использования `await`, что позволяет управлять потоком выполнения и делает код более предсказуемым.
3. **Очереди выполнения:** Актеры используют собственные очереди для обработки задач. Это помогает избегать блокировок, так как операции выполняются последовательно в пределах одного актера.

### Влияние на производительность

Использование акторов может иметь как положительное, так и отрицательное влияние на производительность, в зависимости от конкретного сценария:

1. **Положительное влияние:**
    - **Избежание блокировок:** Поскольку актеры устраняют необходимость в блокировках, они могут значительно улучшить производительность в многопоточных средах, особенно в случаях, когда конкуренция за ресурсы высока.
    - **Чистота кода:** Код, написанный с использованием актеров, легче читать и поддерживать, что снижает вероятность ошибок и уменьшает затраты на отладку.
2. **Отрицательное влияние:**
    - **Дополнительные накладные расходы:** Создание и управление актерами может добавить небольшие накладные расходы. В сценариях с высокой производительностью, где необходимо частое создание и уничтожение акторов, это может стать узким местом.
    - **Латентность:** Поскольку актеры обрабатывают задачи последовательно, могут возникнуть задержки, если один метод блокирует выполнение других. Это особенно важно учитывать при проектировании интерфейсов пользователя, где задержки могут негативно сказаться на пользовательском опыте.

Пример

```swift
actor DataLoader {
    private var data: [String] = []

    // Асинхронный метод для загрузки данных
    func loadData() async {
        // Имитация загрузки данных
        await Task.sleep(1_000_000_000) // 1 секунда
        data = ["Apple", "Banana", "Cherry"]
    }

    // Получение данных
    func getData() -> [String] {
        return data
    }
}

let dataLoader = DataLoader()

// Использование акторов в основном потоке
Task {
    await dataLoader.loadData()
    let fruits = await dataLoader.getData()
    print("Загруженные фрукты: \\(fruits)")
}

```

## Классические задачи

1. Одна из классических задач — это одиночный запрос в сеть

```swift
func fetchUserInfo(
     completionHandler: @escaping (
        _ userInfo: UserInfo?,
        _ error: Error?
     ) -> Void
) {
    let url = URL(string: "www.test.com")!
    let session = URLSession.shared
    let dataTask = session.dataTask(with: url) { data, response, error in
        if let error = error {
            completionHandler(nil, error)
        } else if let data = data {
            do {
               let userInfo = try JSONDecoder().decode(
                  UserInfo.self,
                  from: data
                )
               completionHandler(userInfo, nil)
            } catch {
               completionHandler(nil, error)
            }
        }
    }
    dataTask.resume()
}
```

Это одна из стандартных задач, которая понятна любому разработчику.

Алгоритм очень простой:

- Отправляем запрос в сеть
- Если есть ответ, то возвращаем его
- Если есть ошибка, то возвращаем её

Как бы ты зарефакторил её?

- Ответ
    
    Здесь мы стараемся переписать наш класс на актор
    
    ```swift
    import Foundation
    
    // Определяем структуру UserInfo
    struct UserInfo: Decodable {
        let name: String
        let age: Int
    }
    
    // Актер для загрузки пользовательской информации
    actor UserInfoFetcher {
        // Асинхронный метод для получения информации о пользователе
        func fetchUserInfo() async throws -> UserInfo {
            guard let url = URL(string: "<https://www.test.com>") else {
    		        throw APIError.invalidURL
            }
            let (data, response) = try await URLSession.shared.data(from: url)
            
            // Проверка на корректный ответ
            guard let httpResponse = response as? HTTPURLResponse, (200...299).contains(httpResponse.statusCode) else {
                throw URLError(.badServerResponse)
            }
            
            // Декодирование данных
            let userInfo = try JSONDecoder().decode(UserInfo.self, from: data)
            return userInfo
        }
    }
    
    // Использование актера
    let userInfoFetcher = UserInfoFetcher()
    
    Task {
        do {
            let userInfo = try await userInfoFetcher.fetchUserInfo()
            print("Полученная информация о пользователе: \\(userInfo)")
        } catch {
            print("Ошибка при получении информации о пользователе: \\(error)")
        }
    }
    ```
    

Вопрос: Как бы вы использовали Continuations?

> **Continuations** в Swift — это механизм, который позволяет работать с асинхронным кодом, предоставляя способ "приостановки" выполнения функции до тех пор, пока не будет доступен результат. Continuations являются частью Swift Concurrency и могут быть особенно полезны для интеграции устаревших API (например, основанных на замыканиях) с новой моделью `async/await`.

- Ответ
    
    ```swift
    func fetchUserInfo() async throws -> UserInfo {
        let url = URL(string: "<https://www.test.com>")!
        
        return try await withCheckedThrowingContinuation { continuation in
            let session = URLSession.shared
            let dataTask = session.dataTask(with: url) { data, response, error in
                if let error = error {
                    continuation.resume(throwing: error)
                } else if let data = data {
                    do {
                        let userInfo = try JSONDecoder().decode(UserInfo.self, from: data)
                        continuation.resume(returning: userInfo)
                    } catch {
                        continuation.resume(throwing: error)
                    }
                } else {
                    continuation.resume(throwing: URLError(.badServerResponse))
                }
            }
            dataTask.resume()
        }
    }
    ```

