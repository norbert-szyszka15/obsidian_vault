---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---

---
Utworzono: 2025-01-18 22:11
Tagi: [[INFORMATYKA]], [[LINUX]], [[C]]

---

### **System call `pthread_reate()`**
Funkcja tworzy nowy wątek w procesie, który ją wywołał. Wątek zaczyna się poprzez wywołanie `start_routine()`. `arg` jest przekazywany jako argument funkcji `start_routine()`. Nowy wątek może się zakończyć na jeden z poniższych sposobów:
- wątek wywołuje [[pthread_exit()]], określając status wyjścia, który jest dostępny dla innego innego wątku wywołującego [[pthread_join()]] w tym samym procesie,
- wątek zwraca z funkcji `start_routine()`,
- wątek zostaje anulowany przy pomocy [[pthread_cancel()]],
- dowolny inny wątek w procesie wywołuje [[exit()]], lub główny wątek zwraca wartość z funkcji `main()`. To powoduje zakończenie się wszystkich wątków w procesie. Aby umożliwić działanie wątkom utworzonym przez proces główny nawet wtedy, gdy zostanie on zakończony, należy użyć wątków znanych jako [[Wątki odłączone]].

Argument `attr` wskazuje na strukturę `pthread_attr_t`, której zawartość jest wykorzystywana w czasie tworzenia nowego wątku do określenia jego atrybutów; struktura ta jest inicjalizowana przy pomocy system calla [[pthread_attr_init()]] i spokrewnionych z nim funkcji. Jeżeli wartość argumentu `attr` wynosi 0, nowy wątek zostanie utworzony z domyślnym zestawem atrybutów.

Syntax system calla `pthread_create()` w języku C:

```c
#include <pthread.h>

int pthread_create(pthread_t *restrict thread, const pthread_attr_t *restricr attr, void *(*start_routinie)(void *), void *restrict arg);
```

Wartości zwracane:
- w przypadku sukcesu, `pthread_create()` zwraca 0,
- w przypadku wystąpienia błędu, funkcja zwraca kod tego błędu.

---
### **Stworzenie wątku**
Do stworzenia wątku w Linuxie należy utworzyć jakieś miejsce dla API wątków do przechowywania danych na ich temat, i miejscem tym jest zmienna o typie `pthread_t`. Do zainicjalizowania wątku należy użyć wywołania funkcji `pthread_create()`:
- pierwszym argumentem funkcji jest referencja do miejsca, w którym wątek będzie przechowywał swoje dane,
- drugim argumentem funkcji jest zmienna atrybutów, 
- trzecim argumentem jest wskaźnik na funkcję, która będzie wykonywana przez wątek,
- czwartym argumentem jest argument, który zostanie przekazany do wspomnianej w punkcie wyżej rutyny.
Po zainicjalizowaniu wątku należy na niego poczekać, aż zakończy swoje działanie, przy użyciu [[pthread_join()]]. 

```c
#include <stdlib.h>
#include <stdio.h>
#include <unistd.h>
#include <pthread.h>

void* routine() {
	printf("test from threads\n");
	sleep(3);
	printf("ending thread\n");
}

int main() {
	pthread_t thread1, thread2;
	
	// oba wątki powstają w tym samym czasie
	if (pthread_create(&thread1, NULL, routine, NULL) != 0) {
		return 1;
	}
	if (pthread_create(&thread2, NULL, routine, NULL) != 0) {
		return 2;
	}

	// oba wątki kończą się w tym samym czasie
	if (pthread_join(thread1, NULL) != 0) {
		return 3;
	}
	id (pthread_join(thread2, NULL) != 0) {
		return 4;
	}
	
	return 0;
}
```

---
### **Tworzenie wątków w pętli**
Oczywiście w przypadku pisania większych programów, takich jak np. symulacje działania jakichś zjawisk, pojedyncze tworzenie wątków potrafi być bardzo problematyczne. Wtedy najlepiej jest budować wątki w pętli, co można zrobić np. następująco:

```c
#include <stdlib.h>
#include <stdio.h>
#include <unistd.h>
#include <pthread.h>

pthread_mutex_t mutex;

void* routine() {
	printf("test from threads\n");
	sleep(3);
	printf("ending thread\n");
}

int main() {
	pthread_t th[4];
	int i;
	pthread_mutex_init(&mutex, NULL);

	for (i = 0; i < 4; i++) {
		if (pthread_create(&th[i], NULL, &routine, NULL) != 0) {
			return 1;
		}
		printf("Thread %d has started\n", i);
	}

	for (i = 0; i < 4; i++) {
		if (pthread_join(th[i], NULL) != 0) {
			return 2;
		}
		printf("Thread %d has finished execution\n", i);
	}

	pthread_mutex_destroy(&mutex);
	return 0;
}
```

==UWAGA==: bardzo istotne jest, aby **funkcje `pthread_create()` oraz [[pthread_join()]] były użyte w dwóch osobnych pętlach**. Jeżeli obie funkcje zostaną zastosowane w tylko jednej pętli `for`, program właściwie będzie programem jednowątkowym, ponieważ każdy wątek będzie kończył się przed rozpoczęciem się kolejnego. 

---
### **Tworzenie wątków odłączonych od wątku głównego**
Po więcej informacji na temat tworzenia wątków odłączonych, patrz: [[Wątki odłączone]].

---
## Źródła i odniesienia:
- https://man7.org/linux/man-pages/man3/pthread_create.3.html
- https://www.youtube.com/watch?v=d9s_d28yJq0&list=PLfqABt5AS4FmuQf70psXrsMLEDQXNkLq2&index=1