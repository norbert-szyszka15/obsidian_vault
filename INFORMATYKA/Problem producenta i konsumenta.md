---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---

---
Utworzono: 2025-01-24 17:22
Tagi: [[INFORMATYKA]], [[LINUX]], [[C]]

---
### **Opis problemu producenta i konsumenta**
Załóżmy, że program ma symulować działanie producentów i konsumentów jakiejś jednostki. Obaj mają dostęp do pewnego bufora, do którego producenci dokładają pewną ilość produkowanych jednostek, a konsumenci te jednostki z niego wyciągają w miarę ich zużywania. Trzy główne wyzwania, które przed programistą sprawia ten problem wielowątkowości, to kolejno:
1. Zarządzanie dostępem do pamięci dzielonej,
2. Sprawdzanie, czy bufor jest już pełen i określenie, co w tym przypadku mają robić producenci,
3. Sprawdzanie, czy bufor jest pusty i określenie, co w takim przypadku powinni robić konsumenci.

---
### **Przykład rozwiązania problemu**
Poniższy kod pokazuje rozwiązanie problemu producenta i konsumenta i odpowiednio skaluje się wraz z ilością wątków producentów i wątków producentów.

Ilość producentów i konsumentów można łatwo modyfikować poprzez zastosowanie w kodzie warunków `if {} else` nałożonych na liczbę wątków zdefiniowaną za pomocą makra na początku kodu.

Zarówno producenci jak i konsumenci mają dostęp do dzielonej zmiennej `count` i próbują na niej wykonać inkrementację albo dekrementację. W przypadku długo działających procesów to może doprowadzić do problemów [[Race conditions]]. By temu zapobiec, zastosujemy mutex, który funkcjami [[pthread_mutex_lock()]] i [[pthread_mutex_unlock()]] będzie okalał część kodu modyfikującą wartość zmiennej. To podejście eliminuje problem nr 1 przedstawiony w paragrafie wyżej.

[[Semafory]] `semEmpty` i `semFull` są semaforami przechowującymi kolejno: ilość pozostałych pustych miejsc w buforze i ilość pozostałych pełnych miejsc w buforze. Zainicjalizowanie tych buforów odbywa się przy pomocy wskazania wartości początkowych odpowiednich parametrów w buforze, tj.:
- semafor `semEmpty` inicjalizujemy wartością 10 (ponieważ na początku działania programu w buforze znajduje się dokładnie tyle pustych miejsc),
- semafor `semFull` inicjalizujemy wartością 0 (ponieważ na początku działania programu dokładnie tyle elementów pełnych znajduje się w buforze).

Przed zastosowaniem semaforów dochodziło bardzo często do sytuacji, w której konsument próbował wyjmować jednostki z pustego bufora, lub w której producent próbował jednostki do pustego bufora dodać. Operacje te oczywiście kończyły się w każdym przypadku niepowodzeniem, ale mimo wszystko marnowały cenne zasoby i czas obliczeniowy, bo nawet w przypadku porażki zachodził kod całej rutyny obu wątków. Zastosowanie semaforów pozwala pozbyć się tego problemu i rozwiązuje podpunkty nr 2 i nr 2 z paragrafu pierwszego.

##### Opis działania wątków producenta
Krok produkcji w rutynie producenta generuje losową liczbę pomiędzy 0 a 99.

Krok dodawania wyprodukowanej jednostki do bufora sprawdza ilość wolnych miejsc w buforze za pomocą semafora `semEmpty`, jeżeli w buforze są wolne miejsca wątek blokuje mutex by dostęp do niego nie był możliwy z poziomu innych wątków, dodaje do bufora wartość wygenerowaną w kroku produkcji i zwiększa licznik elementów w buforze, a następnie odblokowuje wartość mutexa i zwiększa licznik zajętych elementów bufora za pomocą semafora `semFull`. Jeżeli w buforze natomiast nie ma wolnych miejsc, rutyna producenta czeka na funkcji [[sem_wait()]] aż któryś z konsumentów zinkrementuje wartość semafora `semEmpty`.

##### Opis działania wątków konsumenta
Krok wyjmowania jednostki z bufora w celu jej skonsumowania robi właściwie dokładnie to samo co krok dodawania jednostki w przypadku procesu producenta, tylko od tyłu. Najpierw przy pomocy semafora `semFull` odbywa się sprawdzenie, czy w buforze są jakieś zajęte pola, jeżeli tak to blokowany jest mutex, co uniemożliwia dostęp do zmiennej licznika innym wątkom, z bufora wyjmowany jest element w celu jego późniejszej konsumpcji i licznik elementów bufora zostaje zmniejszony. W kolejnym etapie mutex zostaje odblokowany i zwiększa się licznik pustych elementów bufora przy pomocy semafora `semEmpty`. Jeżeli w buforze natomiast nie ma żadnych zajętych miejsc, rutyna konsumenta czeka na funkcji [[sem_wait()]] aż któryś z konsumentów zinkrementuje wartość semafora `semFull`. 

Krok konsumpcji w rutynie konsumenta wypisuje na do standardowego wyjścia liczbę wyjętą z bufora.

##### Kod programu
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <pthread.h>
#include <unistd.h>
#include <time.h>
#include <semaphore.h>

#define THREAD_NUM 8

sem_t semEmpty;
sem_t semFull;

pthread_mutex_t mutexBuffer;

int buffer[10];
int count = 0;

void* producer(void* args) {
    while (1) {
        // Produce
        int x = rand() % 100;
        sleep(1);

        // Add to the buffer
        sem_wait(&semEmpty);
        pthread_mutex_lock(&mutexBuffer);
        buffer[count] = x;
        count++;
        pthread_mutex_unlock(&mutexBuffer);
        sem_post(&semFull);
    }
}

void* consumer(void* args) {
    while (1) {
        int y;

        // Remove from the buffer
        sem_wait(&semFull);
        pthread_mutex_lock(&mutexBuffer);
        y = buffer[count - 1];
        count--;
        pthread_mutex_unlock(&mutexBuffer);
        sem_post(&semEmpty);

        // Consume
        printf("Got %d\n", y);
        sleep(1);
    }
}

int main(int argc, char* argv[]) {
    srand(time(NULL));
    pthread_t th[THREAD_NUM];
    pthread_mutex_init(&mutexBuffer, NULL);e
    sem_init(&semEmpty, 0, 10);
    sem_init(&semFull, 0, 0);
    int i;
    for (i = 0; i < THREAD_NUM; i++) {
        if (i > 0) {
            if (pthread_create(&th[i], NULL, &producer, NULL) != 0) {
                perror("Failed to create thread");
            }
        } else {
            if (pthread_create(&th[i], NULL, &consumer, NULL) != 0) {
                perror("Failed to create thread");
            }
        }
    }
    for (i = 0; i < THREAD_NUM; i++) {
        if (pthread_join(th[i], NULL) != 0) {
            perror("Failed to join thread");
        }
    }
    sem_destroy(&semEmpty);
    sem_destroy(&semFull);
    pthread_mutex_destroy(&mutexBuffer);
    return 0;
}
```

---
## Źródła i odniesienia:
- https://www.youtube.com/watch?v=l6zkaJFjUbM&list=PLfqABt5AS4FmuQf70psXrsMLEDQXNkLq2&index=23
- https://code-vault.net/lesson/tlu0jq32v9:1609364042686
- Lab_6_Klasyczne_problemy_synchronizacji.pdf
- https://www.youtube.com/watch?v=Qx3P2wazwI0