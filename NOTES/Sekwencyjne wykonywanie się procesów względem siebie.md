---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---

---
Utworzono: 2025-01-17 20:16
Tagi: [[INFORMATYKA]], [[LINUX]], [[C]]

---

### **Procesy wykonujące się sekwencyjnie**
W poniższym kodzie z każdą iteracją pętli wykonywane są dwie czynności: tworzony jest potomek i proces rodzicielski czeka na jego zakończenie. To znaczy, że procesy potomne wykonują się **współbieżnie z procesem rodzicielskim**, ale **sekwencyjnie względem siebie**.

```c
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

int main(void) {
	int i;

	for (i = 0; i < NUMBER_OF_CHILDREN; i++) {
		int pid = fork();
		
		if (pid < 0) {
			perror("fork");
		}
		if (pid == 0) {
			do_child_work();
		} else {
			do_parent_work();
		}
	}
	
	return 0;
}
```


---
## Źródła i odniesienia:
- Lab_3_SO.pdf