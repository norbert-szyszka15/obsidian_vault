---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---

---
Utworzono: 2025-01-19 15:17
Tagi: [[INFORMATYKA]], [[LINUX]], [[C]]

---

### **System call `sem_destroy()`**
Ta funkcja systemowa usuwa nienazwany semafor pod adresem wskazanym przez argument `sem`. Jedynie semafor wcześniej zainicjalizowany przy użyciu [[sem_init()]] powinien być niszczony korzystając z `sem_destroy()`.

Zniszczenie semafora , którym inne wątki lub procesy są obecnie zablokowane (przy użyciu [[sem_wait()]]), prowadzi do niezdefiniowanych zachowań.

Podobnie użycie jako argumentu semafora, który został już zniszczony, prowadzi do niezdefiniowanych zachowań dopóty, dopóki nie zostanie on przywrócony przy wykorzystaniu [[sem_init()]].

Syntax system calla `sem_destroy()` w języku C:

```c
#include <semaphore.h>

int sem_destroy(sem_t *sem);
```

Wartości zwracane:
- w przypadku sukcesu, funkcja zwraca wartość 0,
- w przypadku niepowodzenia zwracane jest -1, a `errno` jest ustawanie na wskazywanie błędu.

---
## Źródła i odniesienia:
- https://man7.org/linux/man-pages/man3/sem_destroy.3.html