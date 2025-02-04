---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---

---
Utworzono: 2025-01-24 10:28
Tagi: [[INFORMATYKA]], [[LINUX]], [[C]]

---
### **Zasada działania i sposób użycia semaforów**
Przed wykonaniem jakiegokolwiek działania z użyciem semaforów, należy dodać do pliku bibliotekę `semaphore.h`. Aby stworzyć nowy semafor, trzeba najpierw utworzyć zmienną globalną tupu `sem_t`, a następnie zainicjalizować semafor wewnątrz funkcji `main()` wątku głównego używając do tego funkcji [[sem_init()]], która jako argumenty przyjmuje:
- referencję do zmiennej semafora,
- wartość, która mówi semaforowi, czy używamy wielu procesów:
	- wartość 1 mówi semaforowi, że program jest wieloprocesowy,
	- wartość 0 mówi semaforowi, że program właściwie jest jednoprocesowy,
- początkową wartość semafora (ponieważ **semafory mają faktyczną wartość typu `integer` przechowywaną wewnątrz struktury definiującej semafor**).

Stworzenie semafora musi się wiązać z jego zniszczeniem, tak jak ma to miejsce w przypadku większości obiektów z API `pthread`. Do tego celu służy funkcja systemowa [[sem_destroy()]], która jako argument pobiera adres zmiennej semafora. Na semaforach można w skrócie wykonywać dwie operacje:
- [[sem_wait()]] - sprawdza wartość semafora. Jeżeli wartość semafora wynosi:
	- 0, funkcja nie nie dekrementuje tej wartości i czeka na tym semaforze,
	- więcej niż 0, funkcja zdekrementuje tę wartość, a kolejne linijki kodu zaczną się normalnie wykonywać,
- [[sem_post()]] - inkrementuje wartość semafora.

Działanie funkcji [[sem_wait()]] można zatem podsumować następującym schematem:
![[Pasted image 20250124155617.png | center]]

Schemat działania [[sem_post()]] będzie w takim wypadku wyglądał tak:
![[Pasted image 20250124155826.png | center]]

---
### **Przykłady użycia semaforów**
##### Przykład 1 - semafor ustawiony na wartość 1
Przykładowy kod, w którym wątki będą wykonywać swoje programy jeden po drugim (podobnie do zastosowania mutexów):

```c
#include <pthread.h>
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <string.h>
#include <semaphore.h>

#define THREAD_NUM 4

sem_t semaphore;

void* routine(void* args) {
    sem_wait(&semaphore);
    sleep(1);
    printf("Hello from thread %d\n", *(int*)args);
    sem_post(&semaphore);
    free(args);
}

int main(int argc, char *argv[]) {
    pthread_t th[THREAD_NUM];
    sem_init(&semaphore, 0, 1);
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

Na podstawie powyższego kodu można przygotować schemat obrazujący sposób działania wątków w programie (legenda schematu znajduje się na końcu strony):
![[Pasted image 20250124161044.png | center]]

##### Przykład 2 - semafor ustawiony na wartość 2
W tym przykładzie, zastosowanie semafora umożliwia jednoczesne wykonywanie się dwóch procesów jednocześnie. To dlatego, że kiedy wątek#0 natrafia na semafor, dekrementuje jego wartość i wykonuje swoje działania aż do zakończenia się sfery krytycznej wywołaniem funkcji [[sem_post()]], wartość semafora spada z 2 na 1, a to oznacza, że kolejny wątek#1 może również zdekrementować wartość semafora (tym razem z 1 na 0), wykonać swój kod, i dość do końca swojej strefy krytycznej. Skoro dopiero natrafienie na semafor o wartości 0 powoduje, że wątek z funkcją [[sem_wait()]] czeka na tym semaforze, oznacza to, że w tym przypadku dopiero wątek#2 zatrzyma swoje wykonywanie aż do inkrementacji wartości semafora.

```c
#include <pthread.h>
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <string.h>
#include <semaphore.h>

#define THREAD_NUM 4

sem_t semaphore;

void* routine(void* args) {
    sem_wait(&semaphore);
    sleep(1);
    printf("Hello from thread %d\n", *(int*)args);
    sem_post(&semaphore);
    free(args);
}

int main(int argc, char *argv[]) {
    pthread_t th[THREAD_NUM];
    sem_init(&semaphore, 0, 2);
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

Schemat działania powyższego kodu (legenda schematu znajduje się na końcu strony):
![[Pasted image 20250124162000.png | center]]

Analogicznie, gdyby w powyższym kodzie semafor został zainicjalizowany wartością 3, najpierw swój kod wykonałyby 3 wątki, a na końcu 1. Inicjalizacja wartością 4 spowodowałaby, że wszystkie 4 wątki wykonałyby się jednocześnie. 

##### Przykład 3 - [[Prosty praktyczny przykład zastosowania semaforów]]

--- 
### **Zwracanie wartości semafora**
By zwrócić wartość semafora, najpierw należy oczywiście zadeklarować zmienną, do której ta wartość będzie zwracana. Samo w sobie zwrócenie wartości można wykonać za pomocą funkcji [[sem_getvalue()]]. Poniżej najprostszy możliwy przykład rutyny wątku zwracającej wartość semafora:
```c'
sem_t sem;

void* routine(void* args) {
	int index = *(int*)args;
	int semVal;
	sem_wait(&sem);
	sem_getvalue(&sem, &semVal);
	printf("(%d) Current semaphore value after wait is %d\n", index, semVal);
	sem_post(&sem);
	sem_getvalue(&sem, &semVal);
	printf("(%d) Current semaphore value after post is %d\n", index, semVal);
	free(args);
}
```

Powyższy kod jest narażony na [[Race conditions]]. Jeżeli wątki będą utworzone w pętli `for()`, nie ma gwarancji że będą się one wykonywać sekwencyjnie, więc prawdopodobieństwo, że wywołanie [[sem_getvalue()]] nastąpi w momencie zajścia wyścigu jest niezerowe.

Zwracanie wartości semafora bywa przydatne, ale głównie w debugowaniu aplikacji i to zakładając, że ich wartość zostanie zwrócona w miarę dobrze lub że zatrzymanie procesu debugowania nastąpi w momencie umożliwiającym porównanie obu wartości. W zastosowaniach nieco poważniejszych nie powinno się jednak zwracać wartości semafora ani nie powinno używać się funkcji [[sem_getvalue()]].

---
### **Semafory w skrócie...**
W systemie Linux dostępne są implementacje semaforów zgodne ze standardem **POSIX**. Semafory to niejako mutexy z nałożonym na nie licznikiem. Ponadto semafory, w przeciwieństwie do mutexów (a także mutexów rekurencyjnych), mogą być zablokowane w tym samym czasie przez różne wątki. Semafory są używane, kiedy jakiś zasób jest limitowany, i co za tym idzie dostęp do tego zasobu także powinien być limitowany dla wątków. Funkcje obsługujące semafory to:
- [[sem_init()]] - inicjuje semafor,
- [[sem_post()]] - zwalnia semafor,
- [[sem_wait()]] - zajmuje semafor,
- [[sem_destroy()]] - usuwa semafor.

---
## Źródła i odniesienia:
- https://www.youtube.com/watch?v=YSn8_XdGH7c&list=PLfqABt5AS4FmuQf70psXrsMLEDQXNkLq2&index=21
- https://code-vault.net/lesson/v9l3sqtpft:1609091934815
- legenda do schematów działania wątków:
![[Pasted image 20250124161207.png | center]]
- https://www.youtube.com/watch?v=0ZlrB6rrUhA&list=PLfqABt5AS4FmuQf70psXrsMLEDQXNkLq2&index=26
- https://code-vault.net/lesson/ixdjrxwfvt:1609433599509