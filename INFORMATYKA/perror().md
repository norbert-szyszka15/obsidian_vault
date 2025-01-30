
---
Utworzono: 2025-01-17 21:03
Tagi: [[INFORMATYKA]], [[LINUX]], [[C]]

---

### **`perror()` system call**
Funkcja produkująca wiadomość zwrotną przy wystąpieniu błędu standardowego lub wyjątku, opisującą ostatni błąd napotkany przy odwołaniu do funkcji systemowych lub bibliotekowych. Najpierw wypisywany jest string *s*, a następnie opis błędu odpowiadający obecnej wartości zmiennej *errno*. W celu zachowania dużej skuteczności, string *s* definiowany przez użytkownika powinien zawierać nazwę funkcji, w której `perror()` został użyty. Kiedy odwołanie do funkcji systemowej nie powiedzie się, `perror()` zwróci wartość -1.

Syntax system calla `perror()` w języku C:

```c
#include <stdio.h>

void perror(const char ***_s_**);

#include <errno.h>

int errno;
```
 


---
## Źródła i odniesienia:
- https://man7.org/linux/man-pages/man3/perror.3.html