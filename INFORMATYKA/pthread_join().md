---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---

---
Utworzono: 2025-01-18 22:11
Tagi: [[INFORMATYKA]], [[LINUX]], [[C]]

---

### **System call `pthread_join()`**
Ta funkcja systemowa czeka na zakończenie konkretnego wątku, określonego w argumencie jako `thread`. Jeżeli ten wątek został już wcześniej zakończony, `pthread_join()` zwraca natychmiast.  Jeżeli wątek, z którego odbywa się wywołanie `pthread_join()` zostanie anulowany, wątek docelowy nadal pozostanie zdatny do dołączenia. 

Syntax system calla `pthread_join()` w języku C:

```c
#include <pthread.h>

int pthread_join(pthread_t thread, void **retval);
```

Wartości zwracane:
- w przypadku sukcesu, funkcja zwraca 0,
- w przypadku błędu, funkcja zwróci jego kod.

---
## Źródła i odniesienia:
- https://man7.org/linux/man-pages/man3/pthread_join.3.html