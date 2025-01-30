---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---

---
Utworzono: 2025-01-24 16:25
Tagi: [[INFORMATYKA]], [[LINUX]], [[C]]

---
### **Opis problemu**
Pewna liczba użytkowników próbuje połączyć się z serwerami pewnej gry sieciowej, by móc w nią zagrać, jednak serwer nie jest w stanie udźwignąć wszystkich tych graczy jednocześnie. Napisz program, który zasymuluje kolejkę do logowania powstałą w wyniku łączenia się graczy z serwerami gry.

```c
#include <pthread.h>
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <string.h>
#include <semaphore.h>

#define THREAD_NUM 16

sem_t semaphore;

void* routine(void* args) {
    printf("(%d) Waiting in the login queue\n", *(int*)args);
    sem_wait(&semaphore);
    printf("(%d) Logged in\n", *(int*)args);
    sleep(rand() % 5 + 1);
    printf("(%d) Logged out\n", *(int*)args);
    sem_post(&semaphore);
    free(args);
}

int main(int argc, char *argv[]) {
    pthread_t th[THREAD_NUM];
    sem_init(&semaphore, 0, 32);
    int i;
    for (i = 0; i < THREAD_NUM; i++) {
        int* a = malloc(sizeof(int));
        *a = i;
        if (pthread_create(&th[i], NULL, &routine, a) != 0) {
            perror("Failed to create thread");
        }
    }

    for (i = 0; i < THREAD_NUM; i++) {
        if (pthread_join(th[i], NULL) != 0) {
            perror("Failed to join thread");
        }
    }
    sem_destroy(&semaphore);
    return 0;
}
```

Przykład ten pokazuje najlepszy sposób używania semaforów - powinny mieć one zastosowanie wszędzie tam, gdzie dostęp do jakiegoś limitowanego zasobu musi być ograniczany.

---
## Źródła i odniesienia:
- https://www.youtube.com/watch?v=Q79uEdKNVGY&list=PLfqABt5AS4FmuQf70psXrsMLEDQXNkLq2&index=22
- https://code-vault.net/lesson/bc16ie7r36:1609091934843