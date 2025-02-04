---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---

---
Utworzono: 2025-01-18 21:49
Tagi: [[INFORMATYKA]], [[LINUX]], [[C]]

---

### **Wstęp**
Wątki, podobnie jak procesy, umożliwiają współbieżną realizację czynności w wykonywanym programie. Domyślnie w ramach każdego procesu działa pojedynczy wątek. Liczba wątków może zostać zwiększona, jeżeli system operacyjny dostarcza mechanizmów ich obsługi. Mechanizmy obsługi można implementować w przestrzeni użytkownika, w jądrze systemu lub hybrydowo (zarówno w przestrzeni użytkownika, jak i w jądrze systemu).

Nowe wątki tworzone są zawsze w obrębie istniejącego procesu. Każdy wątek współdzieli zasoby z innymi wątkami działającymi w ramach tego samego procesu. Jednym z tych zasobów jest przestrzeń adresowa, dzięki czemu nie trzeba dodatkowych zabiegów, aby zrealizować pamięć dzieloną, jest ona po prostu domyślnie dostępna. Z drugiej strony wymusza to ostrożne korzystanie ze zmiennych, które nie są lokalne. Współdzielenie zasobów eliminuje konieczność ich ochrony przed wątkami, a więc zmniejsza ilość informacji, jaką trzeba zapamiętać przy przełączaniu kontekstu, redukując tym samym ilość czasu, jaką trzeba poświęcić na tę czynność. Z tego względu wątki są czasem określanie mianem "lekkich procesów". Wątek jest mniejszą, czyli bardziej **drobnoziarnistą**, jednostką pracy niż proces.

Kolejnymi istotnymi elementami programów, które są wielowątkowe, i wielowątkowości samej w sobie są [[Różnice między równoległością i współbieżnością]]. 

[[Prosty praktyczny przykład zastosowania wątków]].

---

### **Wątki w Linuxie**
Dla programistów aplikacji system Linux dostarcza API wątków, które jest zgodne w większości szczegółów ze standardem **POSIX**. W przeciwieństwie do innych systemów operacyjnych (nawet tych, które są zgodne z Unixem) Linux nie posiada osobnych mechanizmów jądra do obsługi wątków. W jego przypadku wątki są realizowane jako procesy, które współdzielą swoje zasoby. Z tego względu powstały odstępstwa od standardu.

Wysyłanie sygnału w Linuxie z innego procesu do procesu, w ramach którego działają wątki, jest traktowane jako wysyłanie sygnałów zawsze do wątku głównego. Wywołanie jednej z funkcji [[exec]] powoduje automatyczne zakończenie wszystkich wątków, które zostały stworzone w ramach procesu, który wywołał tę funkcję.

---
### **Różnice między procesami a wątkami**
Kod wątków:

```c
#include <stdlib.h>
#include <stdio.h>
#include <unistd.h>
#include <pthread.h>

int x = 2;

void* routine() {
	x++;
	sleep(2);
	printf("Process id %d\n", getpid());
	printf("The value of x: %d\n", x);
}

void* routine2() {
	sleep(2);
	printf("Process id %d\n", getpid());
	printf("The value of x: %d\n", x);
}

int main() {
	pthread_t thread1, thread2;
	
	if (pthread_create(&thread1, NULL, routine, NULL) != 0) {
		return 1;
	}
	if (pthread_create(&thread2, NULL, routine2, NULL) != 0) {
		return 2;
	}

	if (pthread_join(thread1, NULL) != 0) {
		return 3;
	}
	if (pthread_join(thread2, NULL) != 0) {
		return 4;
	}
	
	return 0;
}
```

Kod procesów:

```c
#include <stdlib.h>
#include <stdio.h>
#include <unistd.h>
#include <sys/wait.h>
#include <sys/types.h>

int main () {
	int x = 2;
	int pid = fork();
	
	if (pid == -1) {
		return 1;
	}

	if (pid == 0) {
		x++;
	}

	printf("Process id %d\n", getpid());
	printf("Value of x: %d\n", x);
	
	if (pid != 0) {
		wait(NULL);
	}
	
	return 0;
}
```

##### **Różnice:**
1. Procesy mogą zawierać wiele wątków i są strukturą stojącą ponad nimi.
2. Wszystkie wątki wewnątrz konkretnego mają wspólną przestrzeń adresową, a procesy nie - one dostają kopię danych rodzica, którą mogą modyfikować.
3. Wszystkie wątki dzielą między sobą handlery plików, podczas gdy dla procesów są one duplikowane.

Ponadto, w przypadku wątków należy zwrócić szczególną uwagę na tzw. [[Race conditions]].

---
### **Oprogramowywanie wątków**
##### Obsługa wątków
Aby obsługiwać wątki **POSIX**, nazywane w skrócie `pthreads`, należy w programie włączyć plik nagłówkowy `pthread.h` i skompilować program z dodaną opcją `ipthread` lub `-lpthread`.

Lista funkcji związanych z zarządzaniem wątkami:
- [[pthread_create()]] - tworzy nowy wątek. Przyjmuje cztery argumenty: wskaźnik na zmienną typu [[pthread_t]], wskaźnik na strukturę typu `pthread_attr_t`, wskaźnik na funkcję o prototypie `void *func(void *)` oraz wskaźnik typu `void *` do przekazania argumentów wywołania funkcji realizowanej w ramach wątku,
- [[pthread_exit()]] - kończy działanie wątku. Przyjmuje wskaźnik `void *` na zmienną przechowującą status zakończenia wątku, 
- [[pthread_join()]] - odpowiednik funkcji [[wait()]] dla procesów,
- [[pthread_self()]] - zwraca ID wątku, który ją wywołał,
- [[pthread_equal()]] - porównuje ID dwóch wątków,
- [[pthread_attr_init()]] - inicjuje strukturę atrybutów wątku,
- [[pthread_attr_destroy()]] - dokonuje finalizacji struktury atrybutów wątku,
- [[pthread_cancel()]] - służy do anulowania wątku.

Ponadto, z wątkami w języku C pod Linuxem wiąże się mnóstwo konceptów i obiektów stworzonych do zarządzania wątkami, w których skład wchodzą:
1. [[Mutexy]],
2. [[Semafory]],
3. [[Podstawy zwracania wartości przez wątki]],
4. [[Przekazywanie wartości do rutyny wątków]],
5. [[Bariery]],
6. [[Wątki odłączone]].

---
### **Najczęściej spotykane problemy, z jakimi wiąże się wielowątkowość**
Jest wiele problemów, do których prowadzić może zjawisko wielowątkowości. Najstarsze i najpopularniejsze z nich nazywamy **klasycznymi problemami synchronizacji.** Wśród nich szczególną uwagę należy zwrócić na:
- [[Problem producenta i konsumenta]] - problem polegający na występowaniu bufora, do którego producenci i konsumenci mają dostęp,
- [[Problem czytelników i pisarzy]] - czytanie wartości odbywa się równolegle dla wszystkich procesów czytelników, ale pisarze muszą pisać pojedynczo,
- [[Problem pięciu filozofów]] - wszystkie procesy muszą co jakiś czas wykonywać określoną grupę czynności i nie można doprowadzić do sytuacji deadlocka.

---
## Źródła i odniesienia:
- Lab_4_SO_v_2.pdf
- https://www.youtube.com/watch?v=IKG1P4rgm54&list=PLfqABt5AS4FmuQf70psXrsMLEDQXNkLq2&index=2
- https://www.youtube.com/watch?v=ln3el6PR__Q&list=PLfqABt5AS4FmuQf70psXrsMLEDQXNkLq2&index=6
- https://www.youtube.com/watch?v=HDohXvS6UIk&list=PLfqABt5AS4FmuQf70psXrsMLEDQXNkLq2&index=7