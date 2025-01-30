---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---

---
Utworzono: 2025-01-18 22:11
Tagi: [[INFORMATYKA]], [[LINUX]], [[C]]

---

### **System call `pthread_self()`**
Ta funkcja systemowa zwraca ID wątku, z którego została wywołana. To ta sama wartość, która jest zwracana w zmiennej `*thread` w wywołaniu [[pthread_create()]], które utworzyło wątek.

Syntax system calla `pthread_self()` w języku C:

```c
#include <pthread.h>

pthread_t pthread_self(void);
```

Wartości zwracane: ta funkcja zawsze kończy się powodzeniem, zwracając ID procesu wywołania.


---
## Źródła i odniesienia:
- https://man7.org/linux/man-pages/man3/pthread_self.3.html