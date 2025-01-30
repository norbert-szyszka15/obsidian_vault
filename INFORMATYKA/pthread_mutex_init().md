---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---

---
Utworzono: 2025-01-19 15:16
Tagi: [[INFORMATYKA]], [[LINUX]], [[C]]

---

### **System call `pthread_mutex_init()`**
Opis działania: patrz [[pthread_mutex_destroy()]].

Syntax system calla `pthread_mutex_init()` w języku C:

```c
#include <pthread.h>

int pthread_mutex_init(pthread_mutex_t *restrict mutex, const pthread_mutexattr_t *restrict attr);
pthread_mutex_t mutex = PTHREAD_MUTEX_INITIALIZER;
```


---
## Źródła i odniesienia:
- https://man7.org/linux/man-pages/man3/pthread_mutex_init.3p.html