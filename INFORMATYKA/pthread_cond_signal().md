---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---

---
Utworzono: 2025-01-23 17:37
Tagi: [[INFORMATYKA]], [[LINUX]], [[C]]

---

### **System call `pthread_cond_signal()`**
Opis działania: patrz [[pthread_cond_broadcast()]].

Syntax system calla `pthread_cond_singal()` w języku C:

```c
#include <pthread.h>

int pthread_cond_signal(pthread_cond_t *cond);
```

Wartości zwracane: patrz [[pthread_cond_broadcast()]].

---
### **Różnice między `pthread_cond_signal()` a [[pthread_cond_broadcast()]]**
Przykład działa na podstawie kodu z notatki [[Zmienne warunkowe w C]]:

```c
#include <pthread.h>
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <errno.h>

pthread_mutex_t mutex_fuel;
pthread_cond_t cond_fuel;
int fuel = 0;

void* fuel_filling(void* arg) {
	for (int i = 0; i < 5; i++) {
		pthread_mutex_lock(&mutex_fuel);
		fuel += 15;
		printf("Filled fuel... %d\n", fuel);
		pthread_mutex_unlock(&mutex_fuel);
		pthread_cond_broadcast(&cond_fuel);
		sleep(1);
	}
}

void* car(void* arg) {
	pthread_mutex_lock(&mutex_fuel);
	while (fuel < 40) {
		printf("No fuel, waiting...\n");
		pthread_cond_wait(&cond_fuel, &mutex_fuel);
		// Equivalent to:
		// pthread_mutex_unlock(&mutex_fuel);
		// wait for signal on cond_fuel
		// pthread_mutex_lock(&mutex_fuel);
	}
	fuel -= 40;
	printf("Got fuel, now left: %d\n", fuel);
	pthread_mutex_unlock(&mutex_fuel);
}

int main() {
	pthread_t th[5];
	pthread_mutex_init(&mutex_fuel, NULL);
	pthread_cond_init(&cond_fuel, NULL);
	for (int i = 0; i < 5; i++) {
		if (i == 1) {
			if (pthread_create(&th[i], NULL, &fuel_filling, NULL) != 0) {
				perror("Failed to create thread");
			}
		} else {
			if (pthread_create(&th[i], NULL, &car, NULL) != 0) {
				 perror("Failed to create thread");
			}
		}
	}

	for (int i = 0; i < 5; i++) {
		if (pthread_join(th[i], NULL) != 0) {
			perror("Failed to join");
		}
	}
	pthread_mutex_destroy(&mutex_fuel);
	pthread_cond_destroy(&cond_fuel);
	return 0;
}
```

`pthread_cond_signal()` sygnalizuje zawsze tylko jeden wątek. [[pthread_cond_broadcast()]] przekazuje sygnał do wszystkich oczekujących wątków. 

---
## Źródła i odniesienia:
- https://man7.org/linux/man-pages/man3/pthread_cond_signal.3p.html