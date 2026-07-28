---
title: Сбор результатов тестирования через fastlane
draft: false
tags:
  - develop
  - testing
  - continuations
  - integration
---
 ```ruby
 desc "Собирает метрики стабильности и отправляет их в хранилище"
lane :collect_metrics do
  start_time = Time.now

  scan(scheme: "MyApp", code_coverage: true, result_bundle: true)
  build_duration = Time.now - start_time

  xcov(scheme: "MyApp", json_report: true)
  coverage = JSON.parse(File.read("fastlane/report/coverage/report.json"))["coverage"]

  gym(scheme: "MyApp", export_method: "ad-hoc", output_directory: "build")
  ipa_size = (File.size("build/MyApp.ipa").to_f / 1_048_576).round(2)

  metrics = {
    commit: last_git_commit[:abbreviated_commit_hash],
    branch: git_branch,
    build_time_sec: build_duration.round(1),
    ipa_size_mb: ipa_size,
    coverage_pct: coverage,
    timestamp: Time.now.utc.iso8601
  }

  File.write("metrics.json", metrics.to_json)

  # отправка — например, в Google Sheets, S3 или свою БД
  sh("curl -X POST https://your-metrics-endpoint.com/ingest " \
     "-H 'Content-Type: application/json' " \
     "-d @../metrics.json")
end
 ```

Source: Claude Code
