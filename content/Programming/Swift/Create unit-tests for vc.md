---
title: Create unit-tests for vc
draft: false
tags:
  - develop
  - llm
  - script
---
 
Можно взять для Cursor и сделать тест, который он будет делать только с помощью данного скрипта.

```yaml
task:

  - id: "create_viewcontroller_tests"

    action: "Создание Unit-тестов для ViewController"

    instruction: |

      Данный скрипт отвечает за автоматическое создание структуры и файла Unit-тестов

      для указанного ViewController. Достаточно передать имя контроллера <ViewController>

      и путь к нему <path_to_viewcontroller>. В тестовом таргете будет создана папка и

      файл с тестовым классом, включая базовые тесты.

    steps:

  

      - action: "Определение пути тестового таргета"

        instruction: |

          В проекте должен быть тестовый таргет, например `mcd_iosTests`.

          Путь к тестам обычно:

          `<project_root>/mcd_iosTests/`

          Если тестовый таргет находится в другом месте, укажи его путь вручную.

  

      - action: "Создание папки под тесты для ViewController"

        instruction: |

          Создай папку:

          `<tests_target_path>/Modules/<ViewController>/`

          Папка должна иметь то же название, что и тестовый файл, но без `.swift` и без Tests.

          Если папка существует — пропусти шаг.

  

      - action: "Создание файла тестов"

        instruction: |

          Создай файл `<ViewController>Tests.swift` внутри папки:

          `<tests_target_path>/Modules/<ViewController>/`

          Файл должен содержать корректное имя класса `<ViewController>Tests`

          и импорт тестируемого модуля.

        template: |

          import XCTest

          @testable import mcd_ios

          final class <ViewController>Tests: XCTestCase {

  

              // MARK: - Основные тесты

              func test_canInit() {

                  let sut = makeSUT()

                  XCTAssertNotNil(sut)

              }

  

              func test_viewDidLoad_initialState() {

                  let sut = makeSUT()

                  sut.loadViewIfNeeded()

                  XCTAssertEqual(sut.title, "")

              }

  

              // MARK: - Helpers

              private func makeSUT(file: StaticString = #filePath, line: UInt = #line) -> <ViewController> {

                  let sut = <ViewController>()

                  trackForMemoryLeaks(sut, file: file, line: line)

                  return sut

              }

          }

  

      - action: "Добавление файла в проект"

        instruction: |

          - Добавь созданный файл в тестовый таргет через Xcode:

            Xcode → Add Files to “mcd_iosTests”

      - action: "Проверка корректности"

        instruction: |

          Выполни команду:

          `⌘ + U`

          Убедись, что:

          - Xcode видит новый файл тестов

          - Тесты компилируются

          - Тесты запускаются
```


также похожий есть для snapshot-tests

```yaml
task:

  - id: "create_viewcontroller_tests"

    action: "Создание снапшот-тестов для ViewController"

    instruction: |

      Данный скрипт отвечает за автоматическое создание структуры и файла снапшот-тестов

      для указанного ViewController. Достаточно передать имя контроллера <ViewController>

      и путь к нему <path_to_viewcontroller>. В тестовом таргете будет создана папка и

      файл с тестовым классом, включая базовые тесты.

    steps:

  

      - action: "Определение пути тестового таргета"

        instruction: |

          В проекте должен быть тестовый таргет, например `mcd_iosTests`.

          Путь к тестам обычно:

          `<project_root>/mcd_iosTests/`

          Если тестовый таргет находится в другом месте, укажи его путь вручную.

  

      - action: "Создание папки под тесты для ViewController"

        instruction: |

          Создай папку:

          `<tests_target_path>/Modules/<ViewController>/`

          Папка должна иметь то же название, что и тестовый файл, но без `.swift` и без SnapshotTests.

          Если папка существует — пропусти шаг.

  

      - action: "Создание файла тестов"

        instruction: |

          Создай файл `<ViewController>SnapshotTests.swift` внутри папки:

          `<tests_target_path>/Modules/<ViewController>/`

          Файл должен содержать корректное имя класса `<ViewController>SnapshotTests`

          и импорт тестируемого модуля.

        template: |

          import XCTest

          import SnapshotTesting

          @testable import mcd_ios

          final class <ViewController>Tests: XCTestCase {

  

              // MARK: - Основные тесты

              func test_canInit() {

                  let sut = makeSUT()

                  XCTAssertNotNil(sut)

              }

  

              func test_viewDidLoad_initialState() {

                  let sut = makeSUT()

                  sut.loadViewIfNeeded()

                  assertSnapshot(as: sut, of: .image)

              }

  

              // MARK: - Helpers

              private func makeSUT(file: StaticString = #filePath, line: UInt = #line) -> <ViewController> {

                  let sut = <ViewController>()

                  trackForMemoryLeaks(sut, file: file, line: line)

                  return sut

              }

          }

  

      - action: "Добавление файла в проект"

        instruction: |

          - Добавь созданный файл в тестовый таргет через Xcode:

            Xcode → Add Files to “mcd_iosTests”

      - action: "Проверка корректности"

        instruction: |

          Выполни команду:

          `⌘ + U`

          Убедись, что:

          - Xcode видит новый файл тестов

          - Тесты компилируются

          - Тесты запускаются
```