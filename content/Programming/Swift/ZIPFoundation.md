---
title: ZIPFoundation
draft: false
tags:
  - develop
  - framework
  - library
  - zip
  - compression
---
Библиотека для сжатия
```swift

import ZIPFoundation
import Foundation

enum LogsUploadError: Error {
    case archiveCreationFailed
    case invalidResponse
    case serverError(statusCode: Int)
}

// MARK: - 1. Создание zip-архива из нескольких файлов

func createLogsArchive(logFileURLs: [URL]) throws -> URL {
    let fileManager = FileManager.default
    let tempDir = fileManager.temporaryDirectory
    let archiveURL = tempDir.appendingPathComponent("logs_\(Date().timeIntervalSince1970).zip")
    
    if fileManager.fileExists(atPath: archiveURL.path) {
        try fileManager.removeItem(at: archiveURL)
    }
    
    guard let archive = Archive(url: archiveURL, accessMode: .create) else {
        throw LogsUploadError.archiveCreationFailed
    }
    
    for fileURL in logFileURLs {
        try archive.addEntry(
            with: fileURL.lastPathComponent,
            fileURL: fileURL,
            compressionMethod: .deflate
        )
    }
    
    return archiveURL
}

// MARK: - 2. Отправка zip через multipart/form-data

func uploadLogsArchive(
    logFileURLs: [URL],
    to endpoint: URL,
    fieldName: String = "file", // уточни у бекенда, как называется поле
    additionalFields: [String: String] = [:] // если нужно передать доп. данные (например, deviceId)
) async throws {
    let archiveURL = try createLogsArchive(logFileURLs: logFileURLs)
    defer { try? FileManager.default.removeItem(at: archiveURL) }
    
    let archiveData = try Data(contentsOf: archiveURL)
    let boundary = "Boundary-\(UUID().uuidString)"
    
    var request = URLRequest(url: endpoint)
    request.httpMethod = "POST"
    request.setValue("multipart/form-data; boundary=\(boundary)", forHTTPHeaderField: "Content-Type")
    
    var body = Data()
    
    // Доп. текстовые поля, если бекенд их ожидает
    for (key, value) in additionalFields {
        body.append("--\(boundary)\r\n".data(using: .utf8)!)
        body.append("Content-Disposition: form-data; name=\"\(key)\"\r\n\r\n".data(using: .utf8)!)
        body.append("\(value)\r\n".data(using: .utf8)!)
    }
    
    // Сам файл
    body.append("--\(boundary)\r\n".data(using: .utf8)!)
    body.append("Content-Disposition: form-data; name=\"\(fieldName)\"; filename=\"logs.zip\"\r\n".data(using: .utf8)!)
    body.append("Content-Type: application/zip\r\n\r\n".data(using: .utf8)!)
    body.append(archiveData)
    body.append("\r\n".data(using: .utf8)!)
    
    body.append("--\(boundary)--\r\n".data(using: .utf8)!)
    
    let (_, response) = try await URLSession.shared.upload(for: request, from: body)
    
    guard let httpResponse = response as? HTTPURLResponse else {
        throw LogsUploadError.invalidResponse
    }
    
    guard (200...299).contains(httpResponse.statusCode) else {
        throw LogsUploadError.serverError(statusCode: httpResponse.statusCode)
    }
}
```

Source: https://github.com/weichsel/ZIPFoundation
