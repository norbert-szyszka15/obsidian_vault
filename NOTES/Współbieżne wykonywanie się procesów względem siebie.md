---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---

---
Utworzono: 2025-01-17 20:22
Tagi: [[INFORMATYKA]], [[LINUX]], [[C]]

---

### **Procesy wykonujące się współbieżnie**
W poniższym programie funkcja `generate_child_processes()` tworzy w pętli określoną jej argumentem liczbę procesów potomnych, wykonujących funkcję `do_child_work()`. W ten sposób wszyscy potomkowie mogą się wykonywać współbieżnie względem siebie i procesu-rodzica. Funkcja `wait_for_children()` jest wykonywana wyłącznie przez proces rodzicielski. W ten funkcji proces rodzicielski w pętlu wywołuje funkcję `do_parent_work()`, której zadaniem jest oczekiwanie na zakończenie procesu potomnego. W ten sposób żaden z nich po zakończeniu procesu macierzystego nie zostanie procesem zombie. Tak zaprojektowany program pozwala na **współbieżnie wykonywanie się procesów potomnych względem siebie i względem rodzica**.

```C
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/wait.h>

#define NUMBER_OF_CHILDREN 10

void do_child_work(void) {
	puts("Jestem potomkiem.");
	exit(0);
}

void do_parent_work(void) {
	puts("Jestem rodzicem.");
	if (wait(0) < 0) {
		perror("wait");
	}
}

void generate_child_processes(unsigned int number_of_children) {
	int i;

	for (i = 0; i < number_of_children; i++) {
		int pid = fork();
		
		if (pid < 0) {
			perror("fork");
		}
		if (pid == 0) {
			do_child_work();
		}
	}
}

void wait_for_children(unsigned int number_of_children) {
	int i;
	
	for (i = 0; i < number_of_children; i++) {
		do_parent_work();
	}
}

int main(void) {
	generate_child_processes(NUMBER_OF_CHILDREN);
	wait_for_children(NUMBER_OF_CHILDREN);
	
	return 0;
}
```



---
## Źródła i odniesienia:
- Lab_3_SO.pdf