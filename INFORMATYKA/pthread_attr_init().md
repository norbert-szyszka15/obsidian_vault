---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---

---
Utworzono: 2025-01-18 22:11
Tagi: [[INFORMATYKA]], [[LINUX]], [[C]]

---

### **System call `pthread_attr_init()`**
Ta funkcja systemowa inicjalizuje obiekt atrybutów wątku, wskazywany przez argument `attr`, wartościami podstawowymi. Po tym callu, różne atrybuty zamknięte w obiekcie mogą być zmieniane za pomocą spokrewnionych funkcji. Wywołanie `pthread_attr_init()` na już zainicjalizowanym obiekcie prowadzi do nieokreślonych zachowań.

 Jeden obiekt atrybutów może być wykorzystany do stworzenia więcej niż jednego wątku.

Kiedy obiekt atrybutów wątku nie jest już dłużej potrzebny, powinien on zostać zniszczony przy wykorzystaniu system calla [[pthread_attr_destroy()]]. **Zniszczenie obiektu atrybutów wątku nie ma wpływu na wcześniej utworzone za jego pomocą wątki.** Po zniszczeniu obiektu, można go przywrócić do życia jeszcze raz wywołując funkcję `pthread_attr_init()`.

Syntax system calla `pthread_attr_init()` w języku C:

```c
#include <pthread.h>

int pthread_attr_init(pthread_attr_t *attr);
int pthread_attr_destroy(pthrad_attr_t *attr);
```

Wartości zwracane:
- w przypadku sukcesu, funkcja zwraca wartość 0,
- w przypadku błędu, funkcja zwróci jego kod.

---
## Źródła i odniesienia:
- https://man7.org/linux/man-pages/man3/pthread_attr_init.3.html