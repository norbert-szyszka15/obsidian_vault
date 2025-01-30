---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---

---
Utworzono: 2025-01-18 22:11
Tagi: [[INFORMATYKA]], [[LINUX]], [[C]]

---

### **System call `pthread_attr_destroy()`**
Funkcja ta służy do usuwania obiektów atrybutów wątku, które wcześniej zostały utworzone przy wykorzystaniu system calla [[pthread_attr_init()]]. Zniszczony obiekt atrybutów może zostać przywrócony przy wykorzystaniu wspomnianej funkcji; odwołanie się do nieistniejącego obiektu atrybutów powoduje zachowanie nieokreślone.

Syntax system calla `pthread_attr_destroy()` w języku C:

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
- https://man7.org/linux/man-pages/man3/pthread_attr_destroy.3p.html