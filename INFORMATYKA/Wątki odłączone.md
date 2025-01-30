---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---

---
Utworzono: 2025-01-24 12:38
Tagi: [[INFORMATYKA]], [[LINUX]], [[C]]

---
### **Wątki odłączone w API POSIX w języku C**
Wątki odłączone znajdują zastosowanie wszędzie tam, gdzie jakieś czynności przez nie wykonywane będą się ciągnąć przez długi czas, np. tworzenie backupów. W przypadku takich zastosowań jedynym realnym zadaniem wątku głównego jest stworzenie wątków które będą wykonywały swoje czynności i nadanie im konkretnych właściwości, w związku z czym oczekiwanie na zakończenie przez wątki działań traci sens i dużo lepszym pomysłem staje się zwyczajnie zwolnienie zasobów wykorzystywanych przez wątek główny. Cel ten można w stosunkowo prosty sposób osiągnąć według poniższego kodu (do odłączenia utworzonych wątków od wątku głównego służy funkcja [[pthread_detach()]]):

```c
#include <pthread.h>
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <string.h>
#include <time.h>

#define THREAD_NUM 2

void* routine(void* args) {
    sleep(1);
    printf("Finished execution\n");
}

int main(int argc, char *argv[]) {
    pthread_t th[THREAD_NUM];
    pthread_attr_t detachedThread;
    pthread_attr_init(&detachedThread);
    pthread_attr_setdetachstate(&detachedThread, PTHREAD_CREATE_DETACHED);
    
    int i;
    for (i = 0; i < THREAD_NUM; i++) {
        if (pthread_create(&th[i], &detachedThread, &routine, NULL) != 0) {
            perror("Failed to create thread");
        }
        pthread_detach(th[i]);
    }

    pthread_attr_destroy(&detachedThread);
    pthread_exit(0);
}
```

Po odłączeniu wątków, nie trzeba na nie czekać wewnątrz głównego wątku programu, więc wywołanie [[pthread_join()]] można pominąć.

Jak wiadomo, wywołanie `return 0` jest równoznaczne z zakończeniem się głównego procesu programu, a to przynosi ze sobą skutek w postaci zakończenia się wszystkich wątków utworzonych w ramach tego programu. By tego uniknąć, w miejscu wywołania `return 0` można użyć wywołania funkcji systemowej [[pthread_exit()]] z argumentem 0, co spowoduje zakończenie się głównego wątku programu bez kończenia jego procesu.

==UWAGA==: powyższy kod nie przedstawia procesu tworzenia wątku odłączonego od samego początku. Zamiast tego, przykład powyżej najpierw tworzy wątki dołączone do wątku głównego, a następnie je odłącza przy wykorzystaniu funkcji [[pthread_detach()]].

---
### **Tworzenie wątków w pełni odłączonych**
By stworzyć wątek w pełni odłączony, należy posłużyć się funkcją systemową [[pthread_create()]], a konkretnie wartością jej drugiego argumentu. Użytkownik musi najpierw utworzyć nową zmienną typu `pthread_attr_t`, następnie ją zainicjalizować przy pomocy funkcji [[pthread_attr_init()]] (co jednocześnie wymaga zniszczenia atrybutu za pomocą [[pthread_attr_destroy()]] pod koniec funkcji `main()`), a w kolejnym kroku wykorzystać funkcję [[pthread_attr_setdetachstate()]] do zmiany stanu odłączenia wątku. Wątek w Linuxie może być:
- odłączony od wątku głównego programu (z ang. detached),
- dający się połączyć do wątku głównego programu (z and. joinable).

Przykład tworzenia wątku w pełni odłączonego (czyli takiego, którego nie trzeba odłączać używając funkcji systemowej [[pthread_detach()]]):

```c
#include <pthread.h>
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <string.h>
#include <time.h>

#define THREAD_NUM 2

void* routine(void* args) {
    sleep(1);
    printf("Finished execution\n");
}

int main(int argc, char *argv[]) {
    pthread_t th[THREAD_NUM];
    pthread_attr_t detachedThread;
    pthread_attr_init(&detachedThread);
    pthread_attr_setdetachstate(&detachedThread, PTHREAD_CREATE_DETACHED);
    
    int i;
    for (i = 0; i < THREAD_NUM; i++) {
        if (pthread_create(&th[i], &detachedThread, &routine, NULL) != 0) {
            perror("Failed to create thread");
        }
    }

    pthread_attr_destroy(&detachedThread);
    pthread_exit(0);
}
```

Ponownie nie ma sensu zastosowanie funkcji [[pthread_join()]], ponieważ tworzone wątki są odłączone od samego początku i nie są w stanie dołączyć się do wątku głównego.

---
## Źródła i odniesienia:
- https://www.youtube.com/watch?v=-i8Kzuwr4T4&list=PLfqABt5AS4FmuQf70psXrsMLEDQXNkLq2&index=17
- https://code-vault.net/lesson/18ec1942c2da46840693efe9b52126ba