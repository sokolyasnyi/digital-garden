---
title: Repository health
draft: false
tags:
  - develop
  - metrics
  - tools
---
```
find EssentialFeed/EssentialFeedTests -iname '*.swift' -type f -print0 | xargs -0 wc -l
```

Find total lines of code.


Среднее количество строк:
```
find EssentialFeed/EssentialFeedTests -iname '*.swift' -type f -print0 \
| xargs -0 wc -l \
| awk '$2 != "total" { total += $1; count++ } END { print total / count }'
```

```
find ThemoviedbOne . -name "*.swift" -type f | xargs wc -l | awk 'END { print "Average LOC per file:", int($1 / NR - 1) }'
```
Количество файлов:

```
find ThemoviedbOne . -name "*.swift" -type f | wc -l
```

Количество TODO
```
find ThemoviedbOne . -name "*.swift" -type f | xargs grep -n "TODO"
```

Топ 10 самых больших файлов:
```
find ThemoviedbOne . -name "*.swift" -type f -exec wc -l {} + | sort -nr | head -10
```

Massive View Controllers

```
find ThemoviedbOne . -name "*.swift" -type f | xargs grep -l "class .*ViewController" | xargs wc -l | sort -nr | head -10
```

Динамика роста кодовой базы:

```
git log --pretty=format:%H --reverse \
  | while read commit; do
      echo -n "$(git show -s --format=%ad --date=short $commit) "
      git show $commit --name-only --pretty="" \
        | grep "\.swift$" \
        | xargs wc -l 2>/dev/null \
        | awk 'END {print $1}'
    done
```


```bash
#!/bin/bash

EXCLUDES='! -path "./Pods/*" ! -path "./Carthage/*"'

FILES=$(eval find DrunkOrSmallTest . -name "*.swift" $EXCLUDES -type f | wc -l)
LINES=$(eval find DrunkOrSmallTest . -name "*.swift" $EXCLUDES -type f | xargs wc -l | awk 'END{print $1}')
AVG=$((LINES / FILES))
TODOS=$(eval find DrunkOrSmallTest . -name "*.swift" $EXCLUDES -type f | xargs grep -n "TODO" | wc -l)

echo "📱 iOS Codebase Health Report"
echo "============================="
echo "Swift files:              $FILES"
echo "Total LOC:                $LINES"
echo "Average LOC per file:     $AVG"
echo "TODO comments:            $TODOS"

echo
echo "🚨 Top 5 largest Swift files:"
eval find DrunkOrSmallTest . -name "*.swift" $EXCLUDES -type f -exec wc -l {} + | sort -nr | head -5

echo
echo "🧾 Top TODO files:"
eval find DrunkOrSmallTest . -name "*.swift" $EXCLUDES -type f \
  | xargs grep -n "TODO" \
  | awk -F: '{count[$1]++} END {for (f in count) print count[f], f}' \
  | sort -nr \
  | head -5
```


 Source: https://academy.essentialdeveloper.com/community/threads/dGVhY2hhYmxlLWxlY3R1cmUtMzIwOTc0Mw==#message_dGVhY2hhYmxlLWxlY3R1cmUtbWVzc2FnZS0zMjA5NzQz
