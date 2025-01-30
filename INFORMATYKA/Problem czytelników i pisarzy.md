---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---

---
Utworzono: 2025-01-24 21:55
Tagi: [[INFORMATYKA]], [[LINUX]], [[C]]

---
### **Opis problemu czytelników i pisarzy**
W systemie istnieją dwie grupy wątków - czytelnicy i pisarze. Czytanie może się odbywać współbieżnie z innymi wątkami czytelników, natomiast pisanie, w celu zapewnienia spójności danych, musi odbywać się na zasadzie wyłączności (każdy wątek pisarza musi pisać osobno, żadne dwa wątki nie mogą działać równolegle do siebie). Ponadto:
- czytelnik może wejść do czytelni gdy jest ona pusta lub gdy są tam inni czytelnicy,
- pisarz może wejść do czytelni gdy jest ona pusta.

Problem stanowi uogólnienie problemu dostępu wielu procesów do jednej bazy danych.
![[Pasted image 20250124220119.png | center]]

---
### **Przykład rozwiązania problemu**
Poniższy kod pokazuje ogólne, skalowalne rozwiązanie problemu czytelników i pisarzy.

Sekcja krytyczna w kodzie rutyn wątków czytelników i pisarzy jest okolona funkcjami [[pthread_mutex_lock()]] i [[pthread_mutex_unlock()]].

##### Logika działania rutyny czytelnika
Czytelnik wchodzi do czytelni, inkrementując licznik aktywnych czytelników `readersCount` (użycie mutexa w celu uniknięcia problemów z synchronizacją danych między wieloma wątkami czytelników). Jeżeli dany wątek czytelnika jest pojawia się w czytelni jako pierwszy (`readersCount` ma wartość 1), funkcja [[sem_wait()]] zablokuje pisarzy semaforem `semWriter`. Następnie czytelnik przeprowadza symulację czytania i wychodzi ze swojej sekcji krytycznej, dekrementując wartość licznika aktywnych czytelników `readersCount`. Tutaj ponownie zachodzi sprawdzenie liczby czytelników w czytelni - jeżeli ta liczba jest równa zeru, funkcja [[sem_post()]] odblokowuje dostęp pisarzom.

##### Logika działania rutyny pisarza
Pisarz za pomocą tego samego semafora `semWriter` sprawdza, czy w czytelni znajduje się już jakiś inny pisarz - jeżeli tak, wątek zaczeka na funkcji [[sem_wait()]] do momentu dekrementacji licznika semafora przez poprzedzający wątek pisarza (co jest równoznaczne z opuszczeniem czytelni przez wcześniejszego pisarza), a następnie wejdzie do środka.

##### Kod programu
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <pthread.h>
#include <unistd.h>
#include <time.h>
#include <semaphore.h>

#define NUMBER_OF_READERS 50
#define NUMBER_OF_WRITERS 10

// implementacja z pierwszeństwem dla czytelników
// w realnych środowiskach ich zawsze będzie więcej niż pisarzy

sem_t semWriter;
pthread_mutex_t readersMutex;
int readersCount = 0;

void* reader_routine(void* arg) {
    int readerID = *(int*)arg;

    while (1) {
        pthread_mutex_lock(&readersMutex);
        readersCount++;
        if (readersCount == 1) {
            sem_wait(&semWriter);
        }
        pthread_mutex_unlock(&readersMutex);

        printf("Czytelnik %d czyta dane...\n", readerID);
        sleep(1);

        pthread_mutex_lock(&readersMutex);
        readersCount--;
        if (readersCount == 0) {
            sem_post(&semWriter);
        }
        pthread_mutex_unlock(&readersMutex);
        sleep(1);
    }
}

void* writer_routine(void* arg) {
    int writerID = *(int*)arg;

    while (1) {
        sem_wait(&semWriter);

        printf("Pisarz %d pisze dane...\n", writerID);
        sleep(2);

        sem_post(&semWriter);
        sleep(2);
    }
}

int main() {
    pthread_t readerThread[NUMBER_OF_READERS], writerThread[NUMBER_OF_WRITERS];
    int readerID[NUMBER_OF_READERS], writerID[NUMBER_OF_WRITERS];

    sem_init(&semWriter, 0, 1);
    pthread_mutex_init(&readersMutex, NULL);

    for (int i = 0; i < NUMBER_OF_READERS; i++) {
        readerID[i] = i + 1;
        if (pthread_create(&readerThread[i], NULL, reader_routine, &readerID[i]) != 0) {
            perror("Nie udało się stworzyć wątków czytelników");
        }
    }

    for (int i = 0; i < NUMBER_OF_WRITERS; i++) {
        writerID[i] = i + 1;
        if (pthread_create(&writerThread[i], NULL, writer_routine, &writerID[i]) != 0) {
            perror("Nie udało się stworzyć wątków pisarzy");
        }
    }

    for (int i = 0; i < NUMBER_OF_READERS; i++) {
        pthread_join(readerThread[i], NULL);
    }
    for (int i = 0; i < NUMBER_OF_WRITERS; i++) {
        pthread_join(writerThread[i], NULL);
    }

    sem_destroy(&semWriter);
    pthread_mutex_destroy(&readersMutex);

    return 0;
}

```
 
---
## Źródła i odniesienia:
- https://www.youtube.com/watch?v=p2XDhW5INOo
- Lab_6_Klasyczne_problemy_synchronizacji.pdf