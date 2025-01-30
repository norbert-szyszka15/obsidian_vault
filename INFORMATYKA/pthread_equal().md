---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---

---
Utworzono: 2025-01-18 22:11
Tagi: [[INFORMATYKA]], [[LINUX]], [[C]]

---

### **System call `pthread_equal()`**
Ta funkcja systemowa porównuje identyfikatory dwóch wątków.

Syntax system calla `pthread_equal()` w języku C:

```c
#include <pthread.h>

int pthread_equal(pthread_t t1, pthread_t t2);
```

Wartości zwracane:
- jeżeli ID obu porównywanych wątków są identyczne, funkcja zwraca dowolną wartość niezerową,
- jeżeli ID obu porównywanych wątków są różne, funkcja zwraca wartość 0.



---
## Źródła i odniesienia:
- https://man7.org/linux/man-pages/man3/pthread_equal.3.html