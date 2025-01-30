
---
Utworzono: 2025-01-17 21:03
Tagi: [[INFORMATYKA]], [[LINUX]], [[C]]

---

### **System call `getppid()`**
Ta funkcja systemowa zwraca ID procesu rodzicielskiego względem procesu, z którego została wywołana. Jeżeli proces potomny został utworzony przy użyciu `fork()` i nadal w momencie odwołania się do `getppid()` nadal istnieje, funkcja zwróci ID procesu rodzica. W przeciwnym wypadku funkcja zwróci wartość 1, czyli ID procesu init.

Syntax system calla `getppid()` w języku C:

```c
pid_t getppid(void);
```

Wartości zwracane:
- ID procesu rodzicielskiego względem procesu, z którego funkcja została wywołana,
- 1, czyli ID procesu init w przypadku, gdy rodzic zakończył już swoje działanie.

==UWAGA==: `getppid()` nigdy nie zwraca żadnego błędu!

---

### **Przykład nr 1 - `getppid()`**

```c
#include <stdlib.h>
#include <stdio.h>
#include <unistd.h> 

int main() { 
	int pid;
	pid_t pProcess;
	pid_t cProcess;
	
	pid = fork();
	cProcess = getpid();
	pProcess = getppid();

	if (pid == 0) { 
		printf("\nParent Process id : %d\n", pProcess);
		printf("\nChild Process with parent id : %d\n", cProcess);
	} 
	return 0; 
} 
```

---

### **System call `getpid()`**
Ten system call zwraca ID procesu, w którym funkcja została wywołana.

Syntax system calla `getpid()` w języku C:

```c
pid_t getpid(void);
```

==UWAGA==: `getpid()` nigdy nie zwraca żadnego błędu!

---

### **Przykład nr 2 - `getpid()`**

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>  

int main() { 
	int pid = fork(); 
	if (pid == 0) {
		printf("\nCurrent process id of Process : %d \n", getpid());
	} 
	return 0; 
} 

```

---
## Źródła i odniesienia:
- https://www.geeksforgeeks.org/getppid-getpid-linux/
