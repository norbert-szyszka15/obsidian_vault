---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---

---
Utworzono: 2025-01-19 15:26
Tagi: [[INFORMATYKA]], [[LINUX]], [[C]]

---

### **System call `pthread_mutex_destroy()`**
Ta funkcja systemowa niszczy obiekt mutexa, do którego w poniższym kodzie odwołuję się jako `mutex`, w efekcie czego mutex staje się niezainicjalizowany.

Zniszczony mutex można przywrócić do życia przy użyciu system calla [[pthread_mutex_init()]], w przypadku odwoływanie się do obiektu mutexa będzie traktowane jako zachowanie niezdefiniowane.

Bezpiecznie jest usuwanie mutexów, które są odblokowane. Próba zniszczenia zablokowanego mutexu wiedzie do niezdefiniowanego zachowania.

Funkcja [[pthread_mutex_init()]] inicjalizuje mutex, do którego odnoszę się w kodzie jako `mutex`, przy wykorzystaniu atrybutów opisanych przez `attr`. Jeżeli `attr` jest ustawione na wartość `NULL`, wykorzystane zostaną podstawowe atrybuty mutexa - powstały w ten sposób obiekt powinien być identyczny do obiektu, który można utworzyć poprzez przekazanie adresu domyślnego obiektu atrybutów mutexu. Po pomyślnej inicjalizacji, status mutexu zmienia się na `initialized` i `unlocked`. Próba zainicjalizowania obiektu, który już został zainicjalizowany, wiedzie do zachowania niezdefiniowanego. 

Zachowanie funkcji nie jest ponadto zdefiniowane w przypadkach:
- jeżeli wartość przekazana przez argument `mutex` do `pthread_mutex_destroy()` nie odnosi się do żadnego zainicjalizowanego mutexu,
- jeżeli wartość przekazana przez argument `attr` do `pthread_mutex_init()` nie odnosi się do żadnego istniejącego obiektu atrybutów mutexu.

Syntax system calla `pthread_mutex_destroy()` w języku C:

```c
#include <pthread.h>

int pthread_mutex_destroy(pthread_mutex_t *mutex);
int pthread_mutex_init(pthread_mutex_t *restrict mutex, const pthred_mutexattr_t *restrict attr);
pthread_mutex_t mutex = PTHREAD_MUTEX_INITIALIZER;
```

Wartości zwracane:
- w przypadku sukcesu, obie funkcje zwrócą wartość zero,
- w innym przypadku, obie funkcje zwrócą kod błędu.

---
## Źródła i odniesienia:
- https://man7.org/linux/man-pages/man3/pthread_mutex_destroy.3p.html