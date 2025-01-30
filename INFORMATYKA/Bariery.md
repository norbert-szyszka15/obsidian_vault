---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---

---
Utworzono: 2025-01-24 10:33
Tagi: [[INFORMATYKA]], [[LINUX]], [[C]]

---
### **Bariery z API `pthread`**
Idea bariery jest następująca: zakładając, że na poniższej wizualizacji strzałki przedstawiają wątki, bariera jest obiektem, który zatrzymuje wykonywanie się wątków trafiających na barierę do momentu, kiedy tych wątków jest wystarczająco dużo, by znieść barierę i umożliwić działanie uprzednio zatrzymanych na niej wątków (liczba wątków koniecznych do przekroczenia bariery jest ustalana przez użytkownika).
![[Pasted image 20250124002104.png | center]]

Kiedy bariera zostanie zniesiona dla jednego określonego zestawu wątków, dla kolejnych wątków zostanie ona na nowo wzniesiona. Bariery świetnie nadają się do zapewnienia, że pewna grupa wątków wykona swoje czynności w tym samym czasie.

Tak jak każdy inny obiekt w API wątków POSIX w języku C na Linuxie, bariery muszą zostać odpowiednio zainicjalizowane i zniszczone przy wykorzystaniu funkcji systemowych `pthread_barrier_init()` oraz `pthread_barrier_init()`, a sama zmienna typu `pthread_barierr_t` musi być zmienną globalną.  

Przykład wykorzystania bariery:

```c
#include <stdio.h>
#include <stdlib.h>
#include <pthread.h>
#include <unistd.h>

pthread_barrier_t barrier;

void* routine(void* arg) {
	while (1) {
		printf("Waiting at the barrier...\n");
		sleep(1);
		pthread_barrier_wait(&barrier);
		printf("We passed the barrier\n");
		sleep(1);
	}
}

int main(int argc, char* argv[]) {
    pthread_t th[3];
    int i;
    pthread_barrier_init(&barrier, NULL, 7);
    for (i = 0; i < 3; i++) {
        int* a = malloc(sizeof(int));
        *a = i;
        if (pthread_create(&th[i], NULL, &routine, a) != 0) {
            perror("Failed to created thread");
        }
    }
    for (i = 0; i < 3; i++) {
        if (pthread_join(th[i], NULL) != 0) {
            perror("Failed to join thread");
        }
    }
	pthread_barrier_destroy(&barrier)
    return 0;
}
```

==UWAGA==: drugim argumentem w funkcji inicjalizacyjnej bariery jest pewien zestaw jej cech, natomiast trzecim z nich jest liczba wątków koniecznych do zniesienia bariery.

---
### **Praktyczny przykład zastosowania bariery w programie wielowątkowym**
Opis problemu: każdy wątek rzuca sześcienną kością do gry, zapisując wylosowaną wartość do tablicy. Wątek główny oblicza, kto jest zwycięzcą gry, a następnie każdy z wątków, które rzucały kością, wyprowadza na ekran wiadomość informującą nas o swojej wygranej lub porażce.

```c
#include <pthread.h>
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <string.h>
#include <time.h>

#define THREAD_NUM 8

int dice_values[THREAD_NUM];
int status[THREAD_NUM] = { 0 };

pthread_barrier_t barrierRolledDice;
pthread_barrier_t barrierCalculated;

void* roll(void* args) {
    int index = *(int*)args;
    while (1) {
        dice_values[index] = rand() % 32 + 1;
        pthread_barrier_wait(&barrierRolledDice);
        pthread_barrier_wait(&barrierCalculated);
        if (status[index] == 1) {
            printf("(%d rolled %d) I won\n", index, dice_values[index]);
        } else {
            printf("(%d rolled %d) I lost\n", index, dice_values[index]);
        }
    }
    free(args);
}

int main(int argc, char *argv[]) {
    srand(time(NULL));
    pthread_t th[THREAD_NUM];
    int i;
    pthread_barrier_init(&barrierRolledDice, NULL, THREAD_NUM + 1);
    pthread_barrier_init(&barrierCalculated, NULL, THREAD_NUM + 1);
    for (i = 0; i < THREAD_NUM; i++) {
        int* a = malloc(sizeof(int));
        *a = i;
        if (pthread_create(&th[i], NULL, &roll, (void*) a) != 0) {
            perror("Failed to create thread");
        }
    }

    while (1) {
        pthread_barrier_wait(&barrierRolledDice);
        // Calculate winner
        int max = 0;
        for (i = 0; i < THREAD_NUM; i++) {
            if (dice_values[i] > max) {
                max = dice_values[i];
            }
        }

        for (i = 0; i < THREAD_NUM; i++) {
            if (dice_values[i] == max) {
                status[i] = 1;
            } else {
                status[i] = 0;
            }
        }
        sleep(1);
        printf("==== New round starting ====\n");
        pthread_barrier_wait(&barrierCalculated);
    }

    for (i = 0; i < THREAD_NUM; i++) {
        if (pthread_join(th[i], NULL) != 0) {
            perror("Failed to join thread");
        }
    }
    pthread_barrier_destroy(&barrierRolledDice);
    pthread_barrier_destroy(&barrierCalculated);
    return 0;
}
```

Wszystkie wątki działają równolegle do siebie. 8 wątków tworzonych w trakcie działania programu rzuca kośćmi do gry i dociera do bariery, której ograniczeniem jest liczba o 1 większa od łącznej liczby wątków grających w grę (co pozwala uwzględnić w barierze wątek główny). Po przekroczeniu bariery `barrierRolledDice`, wątki grające dochodzą do kolejnej bariery o nazwie `barrierCalculated`, a wątek główny w międzyczasie oblicza, kto została zwycięzcą gry. Po dotarciu wszystkich wątków, bariera `barrier calculated` zostaje zniesiona, wątki grające wypisują na ekran swoje komunikaty, a następnie rozpoczyna się nowa runda.

Poniższy diagram przedstawia działanie wątków programu w połączeniu z barierami:
![[Pasted image 20250124111247.png | center]]

---
## Źródła i odniesienia:
- https://www.youtube.com/watch?v=_P-HYxHsVPc&list=PLfqABt5AS4FmuQf70psXrsMLEDQXNkLq2&index=15
- https://www.youtube.com/watch?v=MDgVJVIRBnM&list=PLfqABt5AS4FmuQf70psXrsMLEDQXNkLq2&index=16