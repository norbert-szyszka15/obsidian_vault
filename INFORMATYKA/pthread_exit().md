---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---

---
Utworzono: 2025-01-18 22:11
Tagi: [[INFORMATYKA]], [[LINUX]], [[C]]

---

### **System call `pthread_exit()`**
Ta funkcja systemowa kończy proces, z którego została wywołana, i zwraca wartość `retval`, która (pod warunkiem, że proces daje się wykorzystać z funkcją [[pthread_join()]]) jest dostępna do innego procesu działającego w obrębie tego samego procesu. Wszystkie napisane przez użytkownika handlery czyszczenia zasobów są wywoływanie przy wywołaniu tej funkcji. Kiedy wątek kończy swoje działanie, zasoby dzielone w ramach procesu nie są zwalniane (np. semafory, zmienne warunkowe, mutexy i zmienne opisujące pliki). Kiedy działanie kończy ostatni wątek w procesie, proces kończy się tak, jak poprzez wywołanie funkcji `exit()` z kodem 0. W tym przypadku zasoby dzielone w ramach procesu są zwalniane.

Syntax system calla `pthread_exit()` w języku C:

```c
#include <pthread.h>

[[noreturn]] void pthread_exit(void *retval);
```

Funkcja nie zwraca żadnych wartości do miejsca jej wywołania.

---
### **Wykorzystanie `pthread_exit()` jako alternatywnej metody zwracania danych z funkcji**
Funkcji systemowej `pthred_exit()` można użyć zamiast słowa kluczowego `return` w celu zwrócenia jakiejś wartości z wątku wewnątrz jego rutyny.

Kolejną interesującą właściwością jest możliwość wywołania tej funkcji systemowej wewnątrz wątku głównego (co jest niemal równoznaczne z wywołaniem jej wewnątrz funkcji głównej `main`). W poniższym kodzie, wywołanie regularnego słowa kluczowego `return` lub zwykłej funkcji `exit()` spowoduje zakończenie się działania nie tylko wątku głównego, ale także całego procesu (co jest zachowaniem w pełni spodziewanym po tych wywołaniach). Użycie `pthread_exit()` zagwarantuje zakończenie się wątku głównego bez jednoczesnego zamykania procesu całego programu, a cały kod pod wywołaniem wspomnianego system calla nie zostanie wykonany. Istotnym konceptem jest tutaj fakt, że główny proces programu jest właścicielem wszystkich wątków tego programu. Proces główny po wywołaniu system calla `pthread_exit()` wewnątrz funkcji `main` zaczeka na wykonanie się wszystkich wątków programu przed zamknięciem procesu programu.

```c
#include <stdlib.h>
#include <stdio.h>
#include <unistd.h>
#include <pthread.h>
#include <time.h>

void* roll_dice() {
	int value = (rand() % 6) + 1;
	int* result = malloc(sizeof(int));
	*result = value;
	printf("Thread result: %d\n", value);
	pthread_exit((void*) result);
}

int main() {
	int* res;
	srand(time(NULL));
	pthread_t th;

	if (pthread_create(&th, NULL, roll_dice, NULL) != 0) {
		return 1;
	}

	if (pthread_join(th, (void**) &res) != 0) {
		return 2;
	}

	printf("Main res: %p\n", res);
	printf("Result: %d\n", *res);

	free(res);
	return 0;
}
```

---
## Źródła i odniesienia:
- https://man7.org/linux/man-pages/man3/pthread_create.3.html
