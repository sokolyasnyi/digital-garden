---
title: Retry design pattern
draft: false
tags:
  - develop
  - pattern
  - python
---
```python
import time

def retry_operation(operation, max_attempts=3, delay=1):
    attempts = 0
    while attempts < max_attempts:
        try:
            result = operation()
            return result
        except Exception as e:
            print(f"Operation failed: {e}")
            attempts += 1
            time.sleep(delay)
    raise Exception("Operation failed after maximum retry attempts")

# Usage example:
def network_request():
    # Perform network request here
    # ...

retry_operation(network_request, max_attempts=5, delay=3)
```

Source: https://colinchswift.github.io/2023-10-26/10-06-03-564763-retry-design-pattern/#why-use-the-retry-design-pattern
