
---
Utworzono: 2025-01-17 21:03
Tagi: [[INFORMATYKA]], [[LINUX]], [[C]]

---

### **System call `pause()`**
`pause()` usypia proces, z którego funkcja została wywołana, aż do momentu otrzymania sygnału powodującego zakończenie procesu lub uruchomienie procedury obsługi sygnału.

Syntax system calla `pause()` w języku C:

```c
#include <unistd.h>

int pause(void);
```

`pause()` zwraca tylko i wyłącznie wtedy, gdy sygnał zostanie przechwycony i procedura obsługi tego sygnału zwróci swoją wartość. W takim przypadku, `pause()` zwraca -1.

---
## Źródła i odniesienia:
- https://man7.org/linux/man-pages/man2/pause.2.html