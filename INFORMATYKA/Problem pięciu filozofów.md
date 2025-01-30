---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---

---
Utworzono: 2025-01-24 21:55
Tagi: [[INFORMATYKA]], [[LINUX]], [[C]]

---
### **Opis problemu**
Przy stole siedzi pięciu filozofów i leży na nim pięć widelców. Każdy filozof przez większość czasu zajmuje się głównie myśleniem, jednak od czasu do czasu musi coś zjeść. Każdy filozof do spożycia posiłku potrzebuje dwóch widelców - jeden trzyma w lewej, a drugi w prawej dłoni. Jedzenie trwa pewną ilość czasu, a filozof po posiłku odkłada widelcem z powrotem na stół. Cykl myślenia i jedzenia przez filozofów trwa w nieskończoność.

---
### **Przykład rozwiązania problemu - rozwiązanie trywialne**
Rozwiązanie trywialne polega na tym, że każdy z filozofów podnosi najpierw prawy, a potem lewy widelec. Rozwiązanie prowadzi do blokady [[Deadlock]] wtedy, gdy wszyscy filozofowie podniosą prawy widelec i czekają na lewy.

##### Logika działania rutyny filozofa w rozwiązaniu trywialnym
Filozof rozpoczyna swój dzień od myślenia, a następnie próbuje zjeść posiłek i zaczyna podnosić widelce. Jeżeli nie jest dostępny żaden widelec, filozof czeka na funkcji [[sem_wait()]] aż inny filozof jakiś zwolni (powtarza się to dwukrotnie dla obu widelców). Po zjedzeniu filozof inkrementuje wartość odpowiednich semaforów widelców opisanych za pomocą tablicy `sem_t forks[]` przy wykorzystaniu funkcji [[sem_post()]] (filozof odkłada widelce z powrotem na stół).

##### Kod programu rozwiązania trywialnego
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <pthread.h>
#include <unistd.h>
#include <time.h>
#include <semaphore.h>

#define NUMBER_OF_PHILOSOPHERS 5

sem_t forks[NUMBER_OF_PHILOSOPHERS];

void* philosopher_routine(void* arg) {
    int id = *(int*)arg;

    while (1) {
        printf("Filozof %d myśli...\n", id);
        sleep(1);

        // Right fork
        sem_wait(&forks[id]);
        printf("Filozof %d podniósł prawy widelec %d.\n", id, id);

        // Left fork
        sem_wait(&forks[(id + 1) % NUMBER_OF_PHILOSOPHERS]);
        printf("Filozof %d podniósł lewy widelec %d.\n", id, (id + 1) % NUMBER_OF_PHILOSOPHERS);

        // Eating
        printf("Filozof %d je.\n", id);
        sleep(2);

        sem_post(&forks[(id + 1) % NUMBER_OF_PHILOSOPHERS]);
        sem_post(&forks[id]);
        printf("Filozof %d odłożył widelce.\n", id);

        // Thinking
        sleep(10);
    }

    return NULL;
}

int main() {
    pthread_t philosophers[NUMBER_OF_PHILOSOPHERS];
    int philosopherID[NUMBER_OF_PHILOSOPHERS];

    for (int i = 0; i < NUMBER_OF_PHILOSOPHERS; i++) {
        sem_init(&forks[i], 0, 1);
    }

    for (int i = 0; i < NUMBER_OF_PHILOSOPHERS; i++) {
        philosopherID[i] = i;
        if (pthread_create(&philosophers[i], NULL, philosopher_routine, &philosopherID[i]) != 0) {
            perror("Nie udało się utworzyć wątków filozofów");
        }
    }

    for (int i = 0; i < NUMBER_OF_PHILOSOPHERS; i++) {
        pthread_join(philosophers[i], NULL);
    }

    for (int i = 0; i < NUMBER_OF_PHILOSOPHERS; i++) {
        sem_destroy(&forks[i]);
    }

    return 0;
}
```

---
### **Przykład rozwiązania problemu - rozwiązanie asymetryczne**
Rozwiązanie asymetryczne zapewnia mechanizm zapobiegający wystąpieniu blokady [[Deadlock]] poprzez zapewnienie, że filozof o nieparzystym numerze ID względem programu zawsze najpierw podnosi lewy widelec, a filozof o ID parzystym - prawy. Pozwala to uniknąć sytuacji, w której każdy filozof weźmie widelec do lewej ręki, w związku z czym na stole nie zostanie już żaden i filozofie się zagłodzą.

##### Logika działania rutyny filozofa w rozwiązaniu asymetrycznym
Główną zmianą względem implementacji rozwiązania trywialnego jest sprawdzenie ID filozofa - jeżeli jest ono parzyste, przyporządkowujemy mu najpierw prawy widelec, a jeżeli nieparzyste - najpierw wkładamy mu w dłonie lewy widelec. 

##### Kod programu rozwiązania asymetrycznego
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <pthread.h>
#include <unistd.h>
#include <time.h>
#include <semaphore.h>

#define NUMBER_OF_PHILOSOPHERS 5

sem_t forks[NUMBER_OF_PHILOSOPHERS];

void* philosopher_routine(void* arg) {
    int id = *(int*)arg;

    while (1) {
        printf("Filozof %d myśli...\n", id);
        sleep(1);

        if (id % 2 == 0) {
            // Even philospher lifts right fork
            sem_wait(&forks[id]);
            printf("Filozof %d podniósł prawy widelec %d.\n", id, id);

            sem_wait(&forks[(id + 1) % NUMBER_OF_PHILOSOPHERS]);
            printf("Filozof %d podniósł lewy widelec %d.\n", id, (id + 1) % NUMBER_OF_PHILOSOPHERS);
        } else {
            // Uneven philosopher lifts left fork
            sem_wait(&forks[(id + 1) % NUMBER_OF_PHILOSOPHERS]);
            printf("Filozof %d podniósł lewy widelec %d.\n", id, (id + 1) % NUMBER_OF_PHILOSOPHERS);

            sem_wait(&forks[id]);
            printf("Filozof %d podniósł prawy widelec %d.\n", id, id);
        }

        printf("Filozof %d je.\n", id);
        sleep(2);

        sem_post(&forks[(id + 1) % NUMBER_OF_PHILOSOPHERS]);
        sem_post(&forks[id]);
        printf("Filozof %d odłożył widelce.\n", id);

        sleep(10);
    }

    return NULL;
}

int main() {
    pthread_t philosophers[NUMBER_OF_PHILOSOPHERS];
    int philosopherID[NUMBER_OF_PHILOSOPHERS];

    for (int i = 0; i < NUMBER_OF_PHILOSOPHERS; i++) {
        sem_init(&forks[i], 0, 1);
    }

    for (int i = 0; i < NUMBER_OF_PHILOSOPHERS; i++) {
        philosopherID[i] = i;
        if (pthread_create(&philosophers[i], NULL, philosopher_routine, &philosopherID[i]) != 0) {
            perror("Nie udało się utworzyć wątków filozofów");
        }
    }

    for (int i = 0; i < NUMBER_OF_PHILOSOPHERS; i++) {
        pthread_join(philosophers[i], NULL);
    }

    for (int i = 0; i < NUMBER_OF_PHILOSOPHERS; i++) {
        sem_destroy(&forks[i]);
    }

    return 0;
}
```

---
## Źródła i odniesienia:
- https://www.youtube.com/watch?v=FYUi-u7UWgw
- Lab_6_Klasyczne_problemy_synchronizacji.pdf
