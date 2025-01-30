---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---

---
Utworzono: 2025-01-24 10:30
Tagi: [[INFORMATYKA]], [[LINUX]], [[C]]

---
### **Zwracanie wartości z wątków do funkcji `main()`**
Jako, że rutyna wątku musi być określona jako wskaźnik na `void`, wartość przez nią zwracana musi być tego samego typu. Na szczęście wskaźniki na typy proste dość łatwo jest castować na wskaźnik na `void`. Ponadto, do wydobycia wartości z wątku posłuży dodatkowo znana nam już funkcja [[pthread_join()]]. Poniżej przykład prostego, elementarnego przypadku zwracania wartości przez wątek:

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
	printf("Thread result: %p\n", result);
	return (void*) result;
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

Jak widać, wskaźnik na zmienną typu całkowitego z funkcji `roll_dice()` o nazwie `result` i taki sam wskaźnik z funkcji `main()` o nazwie `res` podają jako adres życia zmiennej tę samą szynę pamięci. 

Powyższe podejście ma jednak kilka problemów, m.in.:
- z punktu widzenia architektury kodu, to rozwiązanie jest dalekie od ideału poprzez fakt dynamicznej alokacji pamięci i jej zwolnienia w dwóch różnych funkcjach, czego powinno się tym bardziej unikać, nad im większym projektem pracujemy,
- skomplikowanie kodu.

Żeby usprawnić działanie zwracania, można posłużyć się dodatkowymi argumentami przekazywanymi do wątku. Istnieje także inna metoda zwracania wartości z wątku, i wykorzystuje się w niej wywołanie funkcji systemowej [[pthread_exit()]] (tam też opisano szczegóły działania tej metody).

---
## Źródła i odniesienia:
- https://www.youtube.com/watch?v=ln3el6PR__Q&list=PLfqABt5AS4FmuQf70psXrsMLEDQXNkLq2&index=7