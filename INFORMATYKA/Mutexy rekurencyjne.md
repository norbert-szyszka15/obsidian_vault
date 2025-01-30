---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---

---
Utworzono: 2025-01-24 14:38
Tagi: [[INFORMATYKA]], [[LINUX]], [[C]]

---
### **Mutexy rekurencyjne w C**
Mutexy rekurencyjne to mutexy, które mogą być zablokowane wielokrotne wewnątrz jednego wątku, co pozwala unikać deadlocków. Aby stworzyć mutex będący mutexem rekurencyjnym, należy najpierw stworzyć nową zmienną typu `pthread_mutex_attrt_t`, a następnie zainicjalizować ten atrybut mutexa za pomocą funkcji [[pthread_mutexattr_init()]] (co także implikuje konieczność zniszczenia tego atrybutu mutexa na końcu wątku głównego. Chociaż tutaj mała ==UWAGA==: w przypadku atrybutów mutexów można je niszczyć właściwie tuż po utworzeniu wszystkich wątków wewnątrz funkcji `main()`). Kolejnym krokiem jest ustawienie typu mutexa atrybutów na `PTHREAD_MUTEX_RECURSIVE` przy pomocy funkcji [[pthread_mutexattr_settype()]]. Następnie mutex należy zainicjować przy pomocy funkcji systemowej [[pthread_mutex_init()]], gdzie zamiast drugiego atrybutu ustawionego na `NULL` należy podać adres utworzonego obiektu o typie `PTHREAD_MUTEX_RECURSIVE`.

Należy pamiętać o tym, że mutex rekurencyjny zablokowany $x$ razy musi być w następnej kolejności odblokowany także tę samą liczbę $x$ razy.

Prosty przykład działania mutexów rekurencyjnych:

```c
#include <pthread.h>
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <string.h>

#define THREAD_NUM 8

pthread_mutex_t mutexFuel;
int fuel = 50;

void* routine(void* args) {
    pthread_mutex_lock(&mutexFuel);
    pthread_mutex_lock(&mutexFuel);
    pthread_mutex_lock(&mutexFuel);
    pthread_mutex_lock(&mutexFuel);
    pthread_mutex_lock(&mutexFuel);
    fuel += 50;
    printf("Incremented fuel to %d\n", fuel);
    pthread_mutex_unlock(&mutexFuel);
    pthread_mutex_unlock(&mutexFuel);
    pthread_mutex_unlock(&mutexFuel);
    pthread_mutex_unlock(&mutexFuel);
    pthread_mutex_unlock(&mutexFuel);
}

int main(int argc, char *argv[]) {
    pthread_t th[THREAD_NUM];
    pthread_mutexattr_t recursiveMutexAttributes;
    pthread_mutexattr_init(&recursiveMutexAttributes);
    pthread_mutexattr_settype(&recursiveMutexAttributes, PTHREAD_MUTEX_RECURSIVE);
    pthread_mutex_init(&mutexFuel, &recursiveMutexAttributes);
    int i;
    for (i = 0; i < THREAD_NUM; i++) {
        if (pthread_create(&th[i], NULL, &routine, NULL) != 0) {
            perror("Failed to create thread");
        }
    }

    for (i = 0; i < THREAD_NUM; i++) {
        if (pthread_join(th[i], NULL) != 0) {
            perror("Failed to join thread");
        }
    }
    printf("Fuel: %d\n", fuel);
    pthread_mutexattr_destroy(&recursiveMutexAttributes);
    pthread_mutex_destroy(&mutexFuel);
    return 0;
}
```

Mutexy rekurencyjne mogą znaleźć zastosowanie np. w przypadkach tworzenia wielowątkowego programu, który będzie posługiwał się funkcjami rekurencyjnymi. W takim wypadku na pewno trzeba będzie nałożyć mutex na jakąś wartość modyfikowaną przez funkcję rekurencyjną. Wtedy może nastąpić sytuacja, że kolejne rekurencyjne wywołanie funkcji w kodzie nastąpi przed odblokowaniem mutexa, co w powtórzeniu spowoduje kolejne nałożenie blokady na mutex. Te blokady będą się nawarstwiać aż do momentu zakończenia się funkcji rekurencyjnej.

===UWAGA===: chociaż na pierwszy rzut oka podobne do mutexów rekurencyjnych, [[Semafory]] pod kątem architektury są czymś zupełnie odmiennym i niezależnym.

---
## Źródła i odniesienia:
- https://www.youtube.com/watch?v=Ot-VR3jzQMY&list=PLfqABt5AS4FmuQf70psXrsMLEDQXNkLq2&index=20
- https://code-vault.net/lesson/ijfdj124r7:1609080733863