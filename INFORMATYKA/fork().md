
---
Utworzono: 2025-01-17 21:03
Tagi: [[INFORMATYKA]], [[LINUX]], [[C]]

---

### **Zastosowanie, parametry i wartości zwracane**
Funkcja `fork()` w systemach Linux służy do utworzenia procesu zwanego procesem potomnym, który działa współbieżnie do procesu-rodzica. Po utworzeniu potomka, oba procesy wykonują kolejne instrukcje, zapisane po funkcji `fork()`. Proces potomny wykorzystuje te same rejestry i otwarte pliki, co proces-rodzic. Funkcja nie pobiera żadnych parametrów i zwraca wartość integer.

Syntax system calla `fork()`:

```c
#include <sys/types.h>  
#include <unistd.h>  
  
pid_t fork();
```

Wartości zwracane przez funkcję `fork()`:
- **Wartość negatywna** (najczęściej -1) - utworzenie procesu potomnego zakończyło się niepowodzeniem,
- **0** - wartość zwracana do świeżo utworzonego procesu,
- **Wartość pozytywna** (PID nowo powstałego procesu) - wartość zwracana do procesu rodzicielskiego.

---
### **Przykład nr 1**
Utworzenie dwóch procesów, które po utworzeniu uruchamiają ten sam program:

```c
#include <stdio.h>
#include <sys/types.h>
#include <unistd.h>
#include <stdlib.h>

int main() {
    // make two process which run same
    // program after this instruction
    pid_t p = fork();
    if (p < 0) {
      perror("fork fail");
      exit(1);
    }
    
    printf("Hello world!, process_id(pid) = %d \n", getpid());
    return 0;
}
```

---
### **Przykład nr 2**

```c
#include <stdio.h>
#include <sys/types.h>
#include <unistd.h>

int main() {
    fork();
    fork();
    fork();
    printf("hello\n");
    return 0;
}
```

Całkowita liczba wypisań słowa "hello" na ekran jest równa ilości utworzonych procesów. **Całkowita liczba utworzonych przez program procesów jest równa $L_{p} = 2^n$**, gdzie $n$ to liczba użytych w kodzie system calli `fork()`. 

---
### **Przykład nr 3**
W poniższym kodzie tworzone są dwa procesy, rodzicielski i potomny. Możliwe są dwa wyjścia dlatego, że procesy działają współbieżnie, więc nie można określić, któremu procesowi najpierw przydzieli dostęp do procesora.

```c
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <unistd.h>

void forkexample() {
    pid_t p;
    p = fork();
    if (p < 0) {
      perror("fork fail");
      exit(1);
    }
    // child process because return value zero
    else if (p == 0) {
        printf("Hello from Child!\n");
	} else {
    // parent process because return value non-zero.
        printf("Hello from Parent!\n");
    }
}

int main() {
    forkexample();
    return 0;
}
```

Proces rodzica i proces potomka uruchamiają ten sam program, jednak nie znaczy to, że są identyczne. System operacyjny każdemu z nich przydziela inne dane i stany dostępu do CPU, a co za tym idzie - przepływ danych przez oba te procesy może być różny.

---

### **Przykład nr 4**

```c
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <unistd.h>

void forkexample() {
    int x = 1;
    pid_t p = fork();
      if (p < 0) {
      perror("fork fail");
      exit(1);
    }
    else if (p == 0) {
        printf("Child has x = %d\n", ++x);
    }
    else { 
        printf("Parent has x = %d\n", --x);
	}
}
int main() {
    forkexample();
    return 0;
}
```

W tym programie, zmiana zmiennej globalnej w jednym procesie w żadne sposób nie wpływa na dwa pozostałe procesy, ponieważ zestaw danych i stan tych dwóch procesów jest inny i niezależny od procesu nr 1. Dodatkowo, dziecko i rodzic działają w tym samym czasie, więc możliwe są dwa wyjścia.

---

### **`fork()` vs [[exec]]**
System call `fork()` tworzy nowy proces, który jest kopią obecnie działającego procesu, z wyjątkiem wartości zwracanej. Rodzina system calli ``exec`` zastępuje obecnie uruchomiony proces zupełnie nowym programem. 


---
## Źródła i odniesienia:
- https://www.geeksforgeeks.org/fork-system-call/