---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---

---
Utworzono: 2025-01-19 15:16
Tagi: [[INFORMATYKA]], [[LINUX]], [[C]]

---

### **System call `sem_init()`**
Funkcja ta inicjalizuje nienazwany semafor w miejscu w adresie wskazanym przez `sem`. Argument `value` określa wartości początkowe semafora.

Argument `pshared` określa, czy semafor ma być dzielony między wątkami tego samego procesu, czy między procesami:
- jeżeli `pshared` ma wartość 0, wtedy semafor jest dzielony między wątkami procesu i powinien być umiejscowiony w adresie widocznym dla wszystkich wątków (np. zmienna globalna lub zmienna alokowany dynamicznie na stercie),
- jeżeli `pshared` ma wartość niezerową, wtedy semafor jest dzielony między procesami i powinien być umiejscowiony w regionie pamięci dzielonej.

Inicjalizacja semafora, który został już zainicjalizowany, skutkuje zachowaniem nieokreślonym.

Proces potomny względem procesu ma oczywiście dostęp do samaforów swojego rodzica, ponieważ jedną z rzeczy dziedziczonych przez procesy są mapowania pamięci swoich procesów rodzicielskich. W związku z tym, potomkowie procesu rodzicielskiego mogą operować na jego semaforach na pomocą funkcji [[sem_post()]], [[sem_wait()]], itd. 

Syntax system calla `sem_init()` w języku C:

```c
#include <semaphore.h>

int sem_init(sem_t *sem, int pshared, unsigned int value);
```

Wartość zwracana:
- funkcja zwraca 0 w przypadku powodzenia,
- w przypadku błędu funkcja zwraca wartość -1, a zmienna `errno` jest ustawiana na wartość wskazującą na błąd.


---
## Źródła i odniesienia:
- https://man7.org/linux/man-pages/man3/sem_init.3.html