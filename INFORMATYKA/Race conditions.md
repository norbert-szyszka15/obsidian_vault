---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---

---
Utworzono: 2025-01-21 21:34
Tagi: [[INFORMATYKA]], [[LINUX]], [[C]]

---
### **Race condition dla wątków w systemie Linux**
Race condition to ogólna nazwa problemu występującego wraz z wątkami w języku C pod Linuxem. Występują one gdy w trakcie wykonywania się jakiegoś procesu istnieje w nim więcej niż jeden wątek i wszystkie z nich modyfikują jeden konkretny obszar w pamięci. Jako że pamięć dla wątków jest dzielona pomiędzy nie wszystkie, czasami może dojść do sytuacji, w której ta sama wartość w pamięci zostanie przeczytana przez więcej niż jeden wątek. Wtedy jeden z tych wątków zostaje wstrzymany, a w międzyczasie działają wszystkie pozostałe. Kiedy zatrzymany wątek zostanie wznowiony, wykona on swój zestaw operacji na danych, jakie sczytał z pamięci tuż przed jego zatrzymaniem, zmodyfikuje w rejestrze procesora i zapisze na szynę pamięci, cofając postęp i zmiany poczynione w tym czasie przez inne wątki.

Prosty przykład race condition można przedstawić za pomocą rutyny, która ma w prosty sposób inkrementować wartość zmiennej w dwóch wątkach:

```c
#include <stdlib.h>
#include <stdio.h>
#include <pthread.h>

int mails = 0;

void* routine() {
	for (int i = 0; i < 100000000; i++) {
		mails++;
	}
}

int main() {
	pthread_t p1, p2;
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

	printf("Number of mails: %d\n", mails);
	return 0;
}
```

W powyższym przypadku spodziewany wynik to podwojona wartość liczby wątków, jednak faktyczna wartość jest zupełnie inna. Race condition dla małych wartości zdarza się stosunkowo rzadko, ale nadal może wystąpić i trzeba mieć go na uwadze w trakcie pisania aplikacji.

==UWAGA==: race conditions występują w głównej mierze w procesorach wielordzieniowych. Jednordzeniowe procesory, choć niezwykle rzadko spotykane współcześnie, nadal potrafią być używane w nieco mniej zaawansowanych komputerach lub w urządzeniach IoT.

Do radzenia sobie ze wszelkiego rodzaju race conditions API wątków POSIX posiada wbudowany mechanizm narzucania blokowania wykonywania operacji przez inne wątki w czasie trwania jednego, konkretnego wątku, i są nim [[Mutexy]].


---
## Źródła i odniesienia:
- https://www.youtube.com/watch?v=FY9livorrJI&list=PLfqABt5AS4FmuQf70psXrsMLEDQXNkLq2&index=3