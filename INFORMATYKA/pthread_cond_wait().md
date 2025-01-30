---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---

---
Utworzono: 2025-01-23 17:37
Tagi: [[INFORMATYKA]], [[LINUX]], [[C]]

---
### **System call `pthread_call_wait()`**
Funkcja, która blokuje się na zmiennej warunkowej i uwalnia mutex. Użytkownik powinien zwrócić szczególną uwagę na wywoływanie tej funkcji z odpowiednią ilością argumentów i by te argumenty były odpowiednio zainicjalizowane. Po zakończeniu się działania funkcji zapewnia ona, że odblokowany przez nią mutex staje się ponownie mutexem zablokowanym.

Syntax system calla `pthread_call_wait()` w języku C:

```c
#include <pthread.h>

int pthread_cond_wait(pthread_cond_t *restrict cond, pthread_mutex_t *restrict mutex);
```

Wartości zwracane:
- w przypadku sukcesu, funkcja zwraca wartość 0;
- w przypadku niepowodzenia, funkcja zwraca numer błędu zakończenia.

---
## Źródła i odniesienia:
- https://man7.org/linux/man-pages/man3/pthread_cond_wait.3p.html
- https://www.youtube.com/watch?v=0sVGnxg6Z3k&list=PLfqABt5AS4FmuQf70psXrsMLEDQXNkLq2&index=13