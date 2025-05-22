**Forwarded from [Stanislav Sokolov](https://t.me/sokol_yasnyi)**

extension XCTestCase {

    func assert(
        _ request: URLRequest,
        scheme: String = "https",
        host: String,
        path: String,
        isExistHttpBody: Bool = false,
        queryItems: [String]? = nil,
        file: StaticString = #filePath,
        line: UInt = #line
    ) {

        XCTAssertEqual(request.url?.scheme, scheme, "scheme")
        XCTAssertEqual(request.url?.host, host, "host")
        XCTAssertEqual(request.url?.path, path, "path")

        if isExistHttpBody {
            XCTAssertNotNil(request.httpBody, "httpBody")
        } else {
            XCTAssertNil(request.httpBody, "httpBody")
        }

        queryItems?.forEach { item in
            XCTAssertEqual(request.url?.query?.contains(item), true, "query doesn't contain - \(item)")

        }
    }
}