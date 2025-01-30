---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---

---
Utworzono: 2025-01-19 15:17
Tagi: [[INFORMATYKA]], [[LINUX]], [[C]]

---

### **System call `sem_wait()`**
Ta funkcja systemowa dekrementuje (blokuje) semafor wskazywany przez argument `sem`. Jeżeli wartość semafora jest większa od zera, zachodzi jej dekrementacja i funkcja natychmiast zwraca. Jeżeli semafor ma obecnie wartość zero, wywołanie funkcji zostaje zablokowane do momentu, aż ta wartość nie wzrośnie powyżej zera, lub dopóki dowolny handler sygnału nie przerwie wywołania.

Funkcja `sem_trywait()` działa analogicznie do `sem_wait()` z tą różnicą, że jeżeli nie jest możliwa natychmiastowa dekrementacja wartości semafora, wywołanie zwraca błąd.

Funkcja `sem_timedwait()` działa tamo samo jak `sem_wait()` z tą różnicą, że argument `abs_timeout` określa limit czasu, przez jaki wywołanie funkcji może być zablokowane w przypadku, gdy natychmiastowa dekrementacja semafora nie jest możliwa. Jeżeli do czasu wywołania funkcji limit czasu zostanie przekroczony, funkcja kończy się błędem timeout, a wartość `errno` jest ustawiania na **ETIMEDOUT**. Jeżeli operacja dekrementacji może być przeprowadzona natychmiast po wywołaniu funkcji, wtedy `sem_timedwait()` nigdy nie zakończy się błędem timeout niezależnie od ustalonej wartości argumentu `abs_timeout` (co więcej, w takim przypadku sprawdzenie tego argumentu zostanie w całości pominięte).

Syntax system calla `sem_wait()` w języku C:

```c
#include <semaphore.h>

int sem_wait(sem_t *sem);
int sem_trywait(sem_t *sem);
int sem_timedwait(sem_t *restrict sem, const struct timespec *restrict abs_timeout);
```

Wartości zwracane:
- wszystkie z powyższych funkcji zwrócą 0 w przypadku sukcesu,
- w przypadku błędu, wartość semafora nie ulega zmianie, funkcje zwracają -1 i `errno` jest ustawiane by wskazywać odpowiedni błąd.

---
## Źródła i odniesienia:
- https://man7.org/linux/man-pages/man3/sem_wait.3.html