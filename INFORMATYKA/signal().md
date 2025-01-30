
---
Utworzono: 2025-01-17 21:03
Tagi: [[INFORMATYKA]], [[LINUX]], [[C]]

---

### **System call `signal()`**
`signal()` ustawia dyspozycje sygnału (**SIG_IGN**, **SIG_DFL** albo adres funkcji obsługującej sygnał napisanej przez użytkownika) `signum` do `handler`. Jeżeli sygnał `signum` zostanie pomyślnie dostarczony do procesu, zachodzi jedno z poniższych zdarzeń:
- jeżeli dyspozycja jest ustawiona na **SIG_IGN**, sygnał jest ignorowany,
- jeżeli dyspozycja jest ustawione na **SIG_DFL**, zachodzi domyślnie zdarzenie związane z odebranym sygnałem,
- jeżeli dyspozycja jest ustawiona na funkcję użytkownika, wtedy kolejno:
	- dyspozycja jest resetowana do **SIG_DFL** lub sygnał jest blokowany,
	- funkcja `handler` jest wywoływana z argumentem `signum`.

==UWAGA==: sygnałów **SIGKILL** oraz **SIGSTOP** nie da się obsłużyć ani zignorować.

Syntax system calla `signal()` w języku C:

```c
#include <signal.h>

typedef void (*sighandler_t)(int);

sighandler_t signal(int signum, sighandler_t handler);
```


---
## Źródła i odniesienia:
- https://man7.org/linux/man-pages/man2/signal.2.html