---
title: Очистка null из project.xbproj
draft: false
tags:
  - develop
  - xcode
  - script
---
```shell
#!/bin/sh
sed -i '' '/(null) in Sources /d' mcd-ios.xcodeproj/project.pbxproj
sed -i '' '/(null) in Resources /d' mcd-ios.xcodeproj/project.pbxproj
sed -i '' '/(null) in Frameworks /d' mcd-ios.xcodeproj/project.pbxproj
```


