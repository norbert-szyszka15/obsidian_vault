---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---

---
Utworzono: 2025-01-19 15:16
Tagi: [[INFORMATYKA]], [[LINUX]], [[C]]

---

### **System call `sem_post()`**
Ta funkcja systemowa inkrementuje (odblokowuje) semafor wskazany przez argument `sem`. Jeżeli w konsekwencji tego działania wartość semafora staje się większa od zera, wtedy kolejny proces albo wątek zablokowany przez [[sem_wait()]] zostanie wybudzony w celu zablokowania semafora.

Syntax system calla `sem_post()` w języku C:

```c
#include <semaphore.h>

int sem_post(sem_t *sem)
```

Wartości zwracane:
- w przypadku sukcesu, funkcja zwraca 0,
- w przypadku niepowodzenia, funkcja zwraca -1, a zmienna `errno` dostaje obecny kod błędu.


---
## Źródła i odniesienia:
- https://man7.org/linux/man-pages/man3/sem_post.3.html