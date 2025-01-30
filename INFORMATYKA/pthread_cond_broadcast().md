---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---

---
Utworzono: 2025-01-23 17:37
Tagi: [[INFORMATYKA]], [[LINUX]], [[C]]

---

### **System call `pthread_cond_broadcast()`**
Poniższe funkcje odblokowują wątki zablokowane przy pomocy zmiennych warunkowych. 

Funkcja `pthread_cond_broadcast()` odblokowuje wszystkie wątki, które przed jej wywołaniem są zablokowane na konkretnej zmiennej warunkowej `cond`.

Funkcja [[pthread_cond_signal()]] odblokowuje co najmniej jeden spośród wątków obecnie zablokowanych przy pomocy konkretnej zmiennej warunkowej `cond`. 

Jeżeli więcej niż jeden wątek jest zablokowany przez zmienną warunkową, kolejność odblokowywania wątków jest określona przez tzw. scheduling policy. Jeżeli każdy odblokowany wątek zwróci zwróci swoją wartość poprzez odwołanie do [[pthread_cond_wait()]], wątek ten staje się na ten moment właścicielem mutexa, który był jednym z argumentów koniecznych do wywołania [[pthread_cond_wait()]]. Odblokowane wątki ustawiają się w kolejce do przejęcia mutexa w odniesieniu do swojego scheduling policy i w taki sposób, jakby każdy z nich wywołał funkcję [[pthread_mutex_lock()]].

Obie funkcje mogą być wywołane bez względu na to, czy wątek je wywołujący jest obecnie właścicielem mutexa, który jest powiązany z warunkiem w funkcji [[pthread_cond_wait()]]. 

Jeżeli żaden wątek nie jest zablokowany w oparciu o zmienną warunkową `cond`, obie funkcje przynoszą żadnego rezultatu. Jeżeli argument `cond` nie odwołuje się do **zainicjalizowanej zmiennej warunkowej, zachowanie obu funkcji jest nieokreślone**. 

Syntax system calla `pthread_cond_broadcast()` w języku C:

```c
#include <pthread.h>

int pthread_cond_broadcast(pthread_cond_t *cond);
int pthread_cond_signal(pthread_cond_t *cond);
```

Wartości zwracane:
- w przypadku sukcesu, obie funkcje zwracają 0,
- w przypadku niepowodzenia, obie funkcje zwrócą numer błędu, który wskazuje na błąd zaistniały w trakcie wywoływania.

---
## Źródła i odniesienia:
- https://man7.org/linux/man-pages/man3/pthread_cond_broadcast.3p.html