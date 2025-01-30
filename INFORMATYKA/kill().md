
---
Utworzono: 2025-01-17 21:03
Tagi: [[INFORMATYKA]], [[LINUX]], [[C]]

---

### **`kill()` system call**
System call `kill()` może być wykorzystany do wysłania dowolnego sygnału do grupy procesowej lub jednego procesu. Działanie tej metody zależy od opcji podanych w argumencie funkcji:
- jeżeli PID jest dodatni, wtedy sygnał `sig` zostanie wysłany do procesu o ID sprecyzowanym jako PID,
- jeżeli PID jest równy 0, wtedy `sig` jest wysyłany do każdego procesu z grupy procesowej procesu, z którego funkcja została użyta,
- jeżeli PID jest równy -1, wtedy `sig` jest wysyłany do wszystkich procesów, do których proces, z którego została użyta funkcja, ma prawa dostępu (oczywisty wyjątek stanowi tutaj proces init).

Syntax system calla `kill()` w języku C:

```c
#include <signal.h>

int kill(pid_t pid, int sig);
```

Wartości zwracane:
- jeżeli system call się powiedzie (czyli zostanie wysłany co najmniej jeden sygnał), `kill()` zwraca 0,
- jeżeli system call nie powiedzie się, zwracana jest wartość -1.


---
## Źródła i odniesienia:
- https://man7.org/linux/man-pages/man2/kill.2.html