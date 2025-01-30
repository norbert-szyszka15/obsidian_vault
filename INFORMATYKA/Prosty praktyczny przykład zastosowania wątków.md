---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---

---
Utworzono: 2025-01-23 14:36
Tagi: [[INFORMATYKA]], [[LINUX]], [[C]]

---

### **Opis problemu**
Program ma tylko dwa wątki. Program sumuje wszystkie liczby z tablicy liczb pierwszych. Niech każdy z wątków bierze połowę całej tablicy liczb pierwszych i sumuje liczby tylko tej połowy. Następnie, oba wątki powinny zwrócić swoje wyniki do funkcji `main()`, która połączy dwie otrzymane półsumy w jedną sumę całości.

W tym przypadku do pierwszego wątku należy przekazać indeks pierwszego elementu tablicy, a do drugiego - indeks szóstego elementu tej tablicy.

```c
#include <stdio.h>
#include <stdlib.h>
#include <pthread.h>
#include <unistd.h>

int primes[10] = { 2, 3, 5, 7, 11, 13, 17, 19, 23, 29 };

void* routine(void* arg) {
    int index = *(int*)arg;
	int sum = 0;
	for (int j = 0; j < 5; j++) {
		sum += primes[index + j];
	}
	printf("Local sum = %d\n", sum);
	*(int*)arg = sum;
    return arg;
}

int main(int argc, char* argv[]) {
    pthread_t th[2];
    int i;
    for (i = 0; i < 2; i++) {
        int* a = malloc(sizeof(int));
        *a = i * 5;
        if (pthread_create(&th[i], NULL, &routine, a) != 0) {
            perror("Failed to created thread");
        }
    }
    int global_sum = 0;
    for (i = 0; i < 2; i++) {
	    int* r;
        if (pthread_join(th[i], (void**) &r) != 0) {
            perror("Failed to join thread");
        }
        global_sum += *r;
        free(r);
    }
    printf("Global sum = %d\n", global_sum);
    return 0;
}
```


---
## Źródła i odniesienia:
- https://www.youtube.com/watch?v=Adtrk3PREQI&list=PLfqABt5AS4FmuQf70psXrsMLEDQXNkLq2&index=8