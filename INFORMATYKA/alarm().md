---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---

---
Utworzono: 2025-01-17 21:03
Tagi: [[INFORMATYKA]], [[LINUX]], [[C]]

---

### **System call `alarm()`**
`alarm()` pozwala nam ustalić czas w sekundach, po jakim sygnał `SIGALRM` zostanie dostarczony do procesu, z którego odbywa się wywołanie funkcji. Jeżeli ustawienie sekund wyniesie 0, wszystkie alarmy są usuwane.

Syntax system calla `alarm()` w języku C:

```c
#include <unistd.h>

unsigned int alarm(unsigned int seconds);
```

Wartości zwracane:
- jeżeli wcześniej został ustawiony jakiś alarm, system call `alarm()` zwróci liczbę sekund, które pozostały do dostarczenia tego alarmu,
- jeżeli żaden alarm nie został wcześniej nastawiony, funkcja zwróci 0.


---
## Źródła i odniesienia:
- https://man7.org/linux/man-pages/man2/alarm.2.html