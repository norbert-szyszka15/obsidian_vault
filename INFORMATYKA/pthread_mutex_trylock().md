---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---

---
Utworzono: 2025-01-19 15:16
Tagi: [[INFORMATYKA]], [[LINUX]], [[C]]

---

### **System call `pthread_mutex_trylock()`**
Opis działania: patrz [[pthread_mutex_lock()]].

Syntax system calla `pthread_mutex_trylock()` w języku C:

```c
#include <pthread.h>

int pthread_mutex_trylock(pthread_mutex_t *mutex);
int pthread_mutex_unlock(pthread_mutex_t *mutex);
```

---
### **Główne różnice między [[pthread_mutex_lock()]] a `pthread_mutex_trylock()` wraz z przykładami**

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <pthread.h>
#include <errno.h>

pthread_mutex_t mutex;

void* routine(void* arg) {
	if (pthread_mutex_trylock(&mutex) == 0) {
		printf("Got lock\n");
		sleep(1);
		pthread_mutex_unlock(&mutex);
	} else {
		printf("Didn't get lock\n");
	}

}

int main() {
	pthread_t th[4];
	pthread_mutex_init(&mutex, NULL);

	for (int i = 0; i < 4; i++) {
		if (pthread_create(&th[i], NULL, &routine, NULL) != 0) {
			printf("Error at creating thread\n");
		}
	}

	for (int i = 0; i < 4; i++) {
		if (pthread_join(th[i], NULL) != 0) {
			printf("Error at joining thread\n");
		}
	}

	pthread_mutex_unlock(&mutex);
	return 0;
}
```

Różnica pomiędzy zastosowaniem funkcji [[pthread_mutex_lock()]] i `pthread_mutex_trylock()` w powyższym kodzie polega na tym, że ta pierwsza zawsze będzie czekać na odblokowanie mutexa, ta druga natomiast w przypadku natrafienia na zablokowany mutex nie będzie czekać na jego odblokowanie, lecz zwróci natychmiast swoją wartość, która w takim przypadku będzie różna od 0.

---
### **Praktyczny przykład zastosowania `pthread_mutex_trylock()`**
Opis problemu: w pewnej kuchni są cztery palniki wykorzystywane przez kucharzy. Palniki z biegiem czasu zużywają paliwo, którym jest gaz. Kiedy gaz się skończy, szefowie mogą iść do domów.

```c
#include <pthread.h>
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <errno.h>
#include <time.h>

// chefs = thrads
// stove = shared data (+mutex)

pthread_mutex_t stoveMutex[4];
int stoveFuel[4] = { 100, 100, 100, 100 };

void* routine(void* args) {
	for (int i = 0; i < 4; i++) {
		if (pthread_mutex_trylock(&stoveMutex[i]) == 0) {
			int fuelNeeded = (rand() % 20);
			if (stoveFuel[i] - fuelNeeded < 0) {
				printf("No more fuel... Going home\n");
			} else {
				stoveFuel[i] -= (rand() % 20);
				usleep(500000);
				printf("Fuel left: %d\n", stoveFuel[i]);
			}
			pthread_mutex_unlock(&stoveMutex[i]);
			break;
		} else {
			if (i == 3) {
				printf("No stove available yet... Waiting\n");
				usleep(300000);
				i = 0;
			}
		}
	}
}

int main() {
	srand(time(NULL));
	pthread_t th[10];
	for (int i = 0; i < 4; i++) {
		pthread_mutex_init(&stoveMutex[i], NULL);	
	}
	for (int i = 0; i < 10; i++) {
		if (pthread_create(&th[i], NULL, &routine, NULL) != 0) {
			perror("Failed to create thread");
		}
	}

	for (int i = 0; i < 10; i++) {
		if (pthread_join(th[i], NULL) != 0) {
			perror("Failed to join thread");
		}
	}
	for (int i = 0; i < 4; i++) {
		pthread_mutex_destroy(&stoveMutex[i]);	
	}
	return 0;
}
```

Ten przykład pokazuje, kiedy warto używać funkcji `pthread_mutex_trylock()`. Funkcja ta jest pomocna kiedy operujemy na wielu pamięciach dzielonych (niejako na dzielonych konceptach) i wątek musi wybrać jeden z nich, ale nie ma znaczenia który. Bez funkcji `pthread_mutex_trylock()`, powyższy problem nie dał by się rozwiązać, ponieważ program cały czas czekałby na jeden mutex.

---
## Źródła i odniesienia:
- https://man7.org/linux/man-pages/man3/pthread_mutex_trylock.3p.html,
- https://man7.org/linux/man-pages/man3/pthread_mutex_lock.3p.html
- https://www.youtube.com/watch?v=OIKr2ll2Nd8&list=PLfqABt5AS4FmuQf70psXrsMLEDQXNkLq2&index=9