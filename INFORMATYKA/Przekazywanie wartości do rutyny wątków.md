---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---

---
Utworzono: 2025-01-24 10:32
Tagi: [[INFORMATYKA]], [[LINUX]], [[C]]

---
### **Używanie argumentów w funkcjach rutyn wątków**
By przekazywać wartości do funkcji rutyn wątków w języku C, należy użyć odpowiednich argumentów w funkcji [[pthread_create()]]. Poniższe zastosowanie nie gwarantuje zachowania kolejności, ponieważ działamy w środowisku wieloprocesowym. Takie zastosowanie przekazań wartości jest bardzo uniwersalne i można je zastosować w niemal każdym problemie.

Opis problemu: utwórz 10 wątków, z których każdy będzie brał tylko jedną liczbę pierwszą z tablicy liczb pierwszych, którą następnie wypisze na ekran.
```c
#include <stdio.h>
#include <stdlib.h>
#include <pthread.h>
#include <unistd.h>

int primes[10] = { 2, 3, 5, 7, 11, 13, 17, 19, 23, 29 };

void* routine(void* arg) {
    int index = *(int*)arg;
    printf("%d ", primes[index]);
    free(arg);
}

int main(int argc, char* argv[]) {
    pthread_t th[10];
    int i;
    for (i = 0; i < 10; i++) {
        int* a = malloc(sizeof(int));
        *a = i;
        if (pthread_create(&th[i], NULL, &routine, a) != 0) {
            perror("Failed to created thread");
        }
    }
    for (i = 0; i < 10; i++) {
        if (pthread_join(th[i], NULL) != 0) {
            perror("Failed to join thread");
        }
    }
    
    return 0;
}
```

---
## Źródła i odniesienia:
- https://www.youtube.com/watch?v=HDohXvS6UIk&list=PLfqABt5AS4FmuQf70psXrsMLEDQXNkLq2&index=8