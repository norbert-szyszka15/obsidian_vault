---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---

---
Utworzono: 2025-02-03 11:29
Tagi: [[INFORMATYKA]], [[LINUX]], [[C]]

---

### **Wielokrotne wywołania system calla [[fork()]] i drzewo procesów**
```c
#include <stdio.h>
#include <string.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/wait.h>

int main() {
	int id1 = fork();
	int id2 = fork();
	if (id1 == 0) {
		if (id2 == 0) {
			printf("We are process y\n");
		} else {
			printf("We are process x\n");
		}
	} else {
		if (id2 == 0) {
			printf("We are process z\n");
		} else {
			printf("We are the parrent process\n");
		}
	}
	while(wait(NULL) != -1 || errno != ECHILD);
	return 0;
}
```

Wywołanie funkcji [[fork()]], jeżeli nie jest umieszczone w żadnych warunkach przy pomocy słowa kluczowego `if`, spowoduje utworzenie nowego procesu **od każdego już istniejącego procesu** (co na rysunku jest równoznaczne z narysowaniem strzałki od prostokątów reprezentujących procesy). Schemat drzewa procesów z opisem PID dla każdego z nich ($x$, $y$, i $z$ to przykładowe identyfikatory procesów): ![[Pasted image 20250203114818.png | center]]

---
### **Czyszczenie pamięci i oczekiwanie na zakończenie się procesów o wielu  potomkach**
Tak jak opisano w notatce funkcji [[wait()]], czeka ona na zakończenie się tylko jednego potomka, a jeżeli tych jest wielu, program przestanie czekać na pozostałe po procesie, który jako pierwszy zakończy swoje działanie. By temu zapobiec, można posłużyć się następującym przykładem (wymaga załączenia nagłówka `errno.h`):

```c
#include <stdio.h>
#include <string.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/wait.h>
#include <errno.h>

int main() {
	int id1 = fork();
	int id2 = fork();

	while(wait(NULL) != -1 || errno != ECHILD) {
		printf("Waited for the child to finish\n");
	}
	return 0;
}
```

---
### **Ilość procesów tworzonych przez wielokrotne wywołanie system calla [[fork()]]**
**Całkowita liczba utworzonych przez program procesów jest równa $L_{p} = 2^n$**, gdzie $n$ to liczba użytych w kodzie system calli `fork()`. Dlatego też, jeżeli chcemy utworzyć określoną liczbę procesów, należy odpowiednio zadbać o to, by były one tworzone w sposób kontrolowany tylko z procesu, który na pewno będzie ich jedynym rodzicem (np. poprzez zastosowanie słowa kluczowego `for`).

```c
for (int i = 0; i < 10; i++) {
	fork();
}
```

Dla przykładu, wywołanie funkcji [[fork()]] jak powyżej utworzy 1024 procesy.

---
## Źródła i odniesienia:
- https://www.youtube.com/watch?v=94URLRsjqMQ&list=PLfqABt5AS4FkW5mOn2Tn9ZZLLDwA3kZUY&index=5