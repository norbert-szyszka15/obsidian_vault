
---
Utworzono: 2025-01-17 21:03
Tagi: [[INFORMATYKA]], [[LINUX]], [[C]]

---

### **Czym jest system call `sleep()` w systemie operacyjnym**
`Sleep` to coś na kształt programu komputerowego, który zmusza obecnie wykonywany proces do czekania przez określoną w argumencie liczbę sekund. Parametr podawany do `sleep()` to zawsze **minimalny** czas, przez który proces pozostanie uśpiony przez wznowieniem swojego wykonywania. 

---

### **Przykład nr 1**
Poniższy kod w nieskończoność tworzy proces potomny, a następnie w nieskończoność wypisuje PIDy rodziciela i potomka. Program pomiędzy wypisaniami czeka określoną przez system calle `sleep()` liczbę sekund.

```c
#include <stdio.h>
#include <unistd.h>
#include <sys/types.h>

int main() {
	if (fork() == 0) {
		while (1) {
			printf("Proces potomny %d\n", getpid());
			sleep(3);
		}
	} else {
		while (1) {
			printf("Proces rodzicielski %d\n", getpid());
			sleep(5);
		}
	}
	return 0;
}
```

---
## Źródła i odniesienia:
- https://www.geeksforgeeks.org/implementation-of-sleep-system-call-in-os/