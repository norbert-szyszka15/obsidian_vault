---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---

---
Utworzono: 2025-01-23 15:32
Tagi: [[INFORMATYKA]], [[LINUX]], [[C]]

---
### **Przykłady zmiennych warunkowych**
Problemem pojawiającym się w poniższym przykładzie przed zastosowaniem zmiennych warunkowych jest fakt, że rutyna wątku `car` blokuje mutex, a następnie ten sam mutex próbuje zablokować rutyna wątku `fuel_filling`, ale nie jest w stanie (nie można zablokować już zablokowanego mutexu), w związku z czym wątek czeka na jego odblokowane, które nigdy nie następuje. Kłopot ten można obejść stosując zmienne warunkowe.

Zmienna warunkowa jest identyfikatorem dla pewnego sygnału, który można albo "zasygnałować" (wysłać), albo na który można czekać. Sygnał nie mówi, że warunek w pętli `while` stał się prawdziwy lub fałszywy, a jedynie mówi, że wynik warunku mógł ulec zmianie. Czekanie na warunek odblokowuje mutex, więc jakiś inny wątek może również czekać na zmienną razem z tym pierwszym. Do obsługi tych działań służą funkcje systemowe powiązane ze zmiennymi warunkowymi:
- [[pthread_cond_wait()]] - ta funkcja musi być stosowana wewnątrz pętli `while`,
- [[pthread_cond_broadcast()]],
- [[pthread_cond_signal()]].

Tak jak w przypadku mutexów lub atrybutów wątków, zmienne warunkowe muszą zostać zainicjalizowane i zniszczone.

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
		pthread_cond_signal(&cond_fuel);
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
	pthread_t th[2];
	pthread_mutex_init(&mutex_fuel, NULL);
	pthread_cond_init(&cond_fuel, NULL);
	for (int i = 0; i < 2; i++) {
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

	for (int i = 0; i < 2; i++) {
		if (pthread_join(th[i], NULL) != 0) {
			perror("Failed to join");
		}
	}
	pthread_mutex_destroy(&mutex_fuel);
	pthread_cond_destroy(&cond_fuel);
	return 0;
}
```

---
### **Statyczna inicjalizacja zmiennych warunkowych**
Tak jak w przypadku mutexów, zmienne warunkowe można zainicjalizować statycznie pod warunkiem, że nie będą one przyjmować żadnych parametrów różnych od wyjściowych. Po więcej informacji na temat statycznych inicjalizatorów mutexów i zmiennych warunkowych, patrz: [[Mutexy]].

---
## Źródła i odniesienia:
- https://www.youtube.com/watch?v=0sVGnxg6Z3k&list=PLfqABt5AS4FmuQf70psXrsMLEDQXNkLq2&index=10
- https://www.youtube.com/watch?v=Xo6e6RsWoBk&list=PLfqABt5AS4FmuQf70psXrsMLEDQXNkLq2&index=18