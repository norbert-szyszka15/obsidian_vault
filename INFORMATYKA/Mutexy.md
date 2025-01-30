---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---

---
Utworzono: 2025-01-23 11:34
Tagi: [[INFORMATYKA]], [[LINUX]], [[C]]

---
### **Czym jest mutex?**
API wątków POSIX posiada już blokady, których można używać w celu zapobiegania [[Race conditions]]. Blokady na wątki, by nie wpadały w destruktywne pętle pomijające niektóre operacje, nazywamy mutexami. Niech za przykład posłuży rutyna wątku użyta do zobrazowania wspomnianych race conditions. Logika rutyny z wprowadzonymi blokadami powinna wyglądać mniej więcej tak:

```c
void* routine() {
	for (int i = 0; i < 10000000; i++) {
		if (lock == 1) {
			// wait until the lock is 0
		}
		lock = 1;
		mails++;
		lock = 0;
	}
}
```

Cały powyższy mechanizm blokady można zastąpić jedną funkcją już zaimplementowaną w API, którą jest [[pthread_mutex_lock()]], a mechanizm odblokowywania zawiera się w funkcji [[pthread_mutex_unlock()]]. Przed wykorzystaniem mutexa należy go oczywiście także zainicjalizować, np. w głównej funkcji programu, przy pomocy zaimplementowanej w bibliotece wątków funkcji inicjalizującej [[pthread_mutex_init()]]. Jednak, tak jak w przypadku niemal każdej inicjalizacji, to, co zostało stworzone, na końcu działania programu musi zostać odpowiednio zniszczone i pamięć musi zostać adekwatnie zwolniona. Do tego celu służy funkcja [[pthread_mutex_destroy()]]. Poprawne zaimplementowanie wątków inkrementujących jakąś wartość będzie więc wyglądało następująco:

```c
#include <stdlib.h>
#include <stdio.h>
#include <pthread.h>

int mails = 0;
pthread_mutex_t mutex;

void* routine() {
	for (int i = 0; i < 100000000; i++) {
		pthread_mutex_lock(&mutex);
		mails++;
		pthread_mutex_unlock(&mutex);
	}
}

int main() {
	pthread_t p1, p2;
	pthread_mutex_init(&mutex, NULL);
	if (pthread_create(&p1, NULL, &routine, NULL) != 0) {
		return 1;
	}
	if (pthread_create(&p2, NULL, &routine, NULL) != 0) {
		return 2;
	}
	if (pthread_join(p1, NULL) != 0) {
		return 3;
	}
	if (pthread_join(p2, NULL) != 0) {
		return 4;
	}

	pthread_mutex_destroy(&mutex);
	printf("Number of mails: %d\n", mails);
	return 0;
}
```

Powyższy mechanizm oczywiście działa na większej niż liczbie wątków i jest odpowiednio skalowalny.

Mutexy dość znacząco spowalniają działanie programu, więc dobrze jest ich unikać na tyle, na ile to możliwe. Jednak w przypadku wątków ich użycie bardzo często jest niezbędne w celu zapewnienia poprawności działania programu. W skrócie można powiedzieć, że **mutexów należy używać z wątkami wszędzie tam, gdzie do gry wchodzi jakaś zmienna globalna.** Istotnym elementem w przypadku mutexów jest rozpatrzenie mechanizmu podwójnego blokowania tego samego mutexu lub blokowania dwóch lub więcej mutexów przez różne wątki, przy czym będą one je blokować w różnej kolejności. Mechanizm ten jest zwany jako [[Deadlock]] i stanowi duże zagrożenie dla większych i nieco bardziej skomplikowanych programów wieloprocesowych. Deadlocków można unikać modyfikując pewne właściwości samych mutexów, otrzymując w ten sposób [[Mutexy rekurencyjne]].

---
### **Mutexy w skrócie...**
W skrócie można powiedzieć, że mutexy są zmiennymi synchronizującymi podobnymi do semaforów, ale mogą przyjmować tylko dwie wartości. Do obsługi mutexów służą funkcje systemowe:
- [[pthread_mutex_init()]] - inicjuje mutex,
- [[pthread_mutex_destroy()]] - niszczy mutex,
- [[pthread_mutex_lock()]] - zajmuje mutex lub zawiesza wątek, jeśli jest zajęty,
- [[pthread_mutex_unlock()]] - zwalnia mutex,
- [[pthread_mutex_trylock()]] - działa jak `pthread_mutex_lock()`, ale nie blokuje wątku.

---
### **Statyczne inicjalizatory mutexów w C**
Kiedy mutexy w programie nie będą inicjowane żadnymi dodatkowymi, specjalnymi parametrami (tzn. kiedy wartość drugiego argumentu funkcji systemowej [[pthread_mutex_init()]] będzie ustawiona na `NULL`), można zamiast wywoływania inicjalizacji mutexu wewnątrz funkcji `main()` można posłużyć się statycznym inicjalizatorem mutexów `PTHREAD_MUTEX_INITIALIZER`. Właściwie jest to makro posiadające w sobie podstawowe wartości parametrów mutexów. Funkcja [[pthread_mutex_destroy()]] w dalszym ciągu nie musi być koniecznie wywoływana, ale jak już zaznaczaliśmy, jej wywołanie jest zawsze dobrą praktyką, która nie zaszkodzi w żaden sposób jakości kodu.

```c
#include <pthread.h>
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <string.h>
#include <time.h>

#define THREAD_NUM 2

pthread_mutex_t mutexFuel = PTHREAD_MUTEX_INITIALIZER;
pthread_cond_t condFuel = PTHREAD_COND_INITIALIZER;

void* routine(void* args) {
}

int main(int argc, char *argv[]) {
    pthread_t th[THREAD_NUM];
    
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
    return 0;
}
```

==UWAGA==: jak widać powyżej, za pomocą statycznych inicjalizatorów można inicjalizować także [[Zmienne warunkowe w C]].

---
## Źródła i odniesienia:
- https://www.youtube.com/watch?v=oq29KUy29iQ&list=PLfqABt5AS4FmuQf70psXrsMLEDQXNkLq2&index=5
- https://www.youtube.com/watch?v=Xo6e6RsWoBk&list=PLfqABt5AS4FmuQf70psXrsMLEDQXNkLq2&index=18
- https://code-vault.net/lesson/18ec1942c2da46840693efe9b521565c