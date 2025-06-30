**Forwarded from [EasySwift iOS🍏](https://t.me/swift_ioss/553)**

[**When the Swift Debugger Lies: The Hidden Cost of Compiler Optimizations**](https://www.debuglife.blog/post/when-the-swift-debugger-lies-1)****

👀 Интересный кейс произошел у автора при отладке приложения: перед выходом из функции массив оказался пустой. 

```
func getUserListForAdmin() async -> NetworkResponse<[OtherUser]> {
    async let type4Request = RestClient.shared.request(Constants.Network.ENDPOINT_USERS, parameters: ["userType": 4])
    async let type2Request = RestClient.shared.request(Constants.Network.ENDPOINT_USERS, parameters: ["userType": 2])

    let (result4, result2) = await (type4Request, type2Request)

    switch (result4, result2) {
    case (.success(let users4), .success(let users2)):
        for user in users4 {
            user.canSeeThisUsersRequests = users2.contains(where: { $0.id == user.id })
        }
        return .success(users4) // At this breakpoint, users2 appears EMPTY 🤯
    case (.error(let error), _), (_, .error(let error)):
        return .error(error)
    }
}
`````

❓ Swift использует оптимизацию управления памятью, что приводит к преждевременному освобождению переменных, таких как `users2`, когда они больше не нужны.

💡 Чтобы сохранить переменные, такие как `users2`, используйте `withExtendedLifetime(users2)` или извлеките их из `switch`-выражения заранее, чтобы избежать их удаления.