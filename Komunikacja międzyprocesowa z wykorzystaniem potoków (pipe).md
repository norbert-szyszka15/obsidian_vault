---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---

---
Utworzono: 2025-02-04 16:23
Tagi: [[INFORMATYKA]], [[LINUX]], [[C]]

---
### **Potoki nienazwane w Linuxie**
Potok nienazwany w Linuxie jest czymś na kształt pliku zapisanego w pamięci komputera i służy on do komunikacji między procesami (procesy mogą zapisywać i czytać dane z tego potoku w trakcie wykonywania się programu). Można go interpretować jako duży bufor, który znajduje się w pamięci operacyjnej lub cache, w którym można pisać lub z którego można czytać różne rzeczy. Potok nienazwany można utworzyć za pomocą system calla [[pipe()]] z biblioteki `unistd.h` (zaleca się sprawdzenie wartości zwracanej przez tę funkcję systemową w kodzie programu). 

Utworzenie potoku nienazwanego łączy się także ze stworzeniem dla niego małej tablicy `fd`, który do skrót pochodzi od angielskiej nazwy **file descriptor**. Deksryptor plikowy o indeksie 0 służy do czytania danych z potoku, natomiast deskryptor o indeksie 1 jest używany do zapisywania danych do potoku nienazwanego. W kontekście [[pipe()]], potoków i deskryptorów plików często przytacza się analogię rury, przez którą przepływa potok, i która ma dwa końce - wejściowy i wyjściowy.

Jeżeli utworzymy nowy proces przy pomocy system calla [[fork()]] po wcześniejszym wywołaniu funkcji [[pipe()]], deksryptory plików są kopiowane i dziedziczone przez procesy potomne. Na przykład, jeżeli deskryptor pliku zostanie zamknięty w procesie rodzica, w procesie dziecka może on pozostawać otwarty.

---
### **Oprogramowywanie potoków nienazwanych w języku C**
Po utworzeniu deskryptorów pliku dla potoku nienazwanego, praca na nich wygląda niemal identycznie jak praca na zwykłych plikach, jednak występuje jedna ważna różnica - należy zwracać szczególną uwagę na fakt wieloprocesowości programu i dbać o to, by wszystkie procesy dziedziczące potok odpowiednio zajmowały się zamknięciem obu jego końców.

Zapis danych do potoku nienazwanego obywa się za pomocą funkcji [[write()]], natomiast zamykanie deskryptora potoku wykonujemy funkcją [[close()]]. Czytanie danych z potoku również wygląda tak samo, jak w przypadku plików zwyczajnych, i jest wykonywane wywołaniem funkcji [[read()]].

==UWAGA==: w przypadku wykorzystania funkcji czytania i pisania dobrą praktyką jest zadbanie o sprawdzenie wartości zwracanej przez obie te funkcje.

==UWAGA==: potoki mają wbudowane mechanizmy czekania podobne do tych wykorzystywanych np. przez [[Semafory]], ale bez dodatkowych funkcji, które trzeba wywoływać:
- jeżeli chcemy coś **zapisać** do potoku który **nie jest pusty**, system operacyjny poczeka na odczytanie potoku przez inny proces,
- jeżeli chcemy **odczytać** jakieś dane z potoku, w którym żadnych **danych nie ma**, system operacyjny poczeka na zapisanie danych do potoku przez inny proces.

W czasie oczekiwania w procesach, które uległy "zawieszeniu" nie będą wykonywane żadne inne instrukcje.

```c
#include <stdio.h>
#include <string.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/wait.h>

int main() {
	int fd[2]; // fd[0] - read end, fd[1] - write end
	if (pipe(fd) == -1) {
		printf("An error ocurred when opening a pipe\n");
		return 1;
	}
	pid_t id = fork();
	if (id == -1) {
		printf("Error on fork()");
		return 4;
	} else if (id == 0) { // kod procesu dziecka (odziedziczyło potok)
		close(fd[0]); // zamknięcie nieużywanego w procesie potoku
		int x;
		printf("Input a number: ");
		scanf("%d", &x);
		if (write(fd[1], &x, sizeof(int)) == -1) {
			printf("Error on write\n");
			return 2;
		}
		close(fd[1]);
	} else { // kod procesu rodzica
		int y;
		close(fd[1]);
		if (read(fd[0], &y, sizeof(int)) == -1) {
			printf("Error on read\n");
			return 3;
		}
		close(fd[0]);
		printf("Got from child process %d\b", y);
	}
	
	return 0;
}

```

---
### **Przykład praktyczny**
Po nieco więcej informacji i praktyczny przykład zastosowania system calli [[fork()]] i [[pipe()]], patrz [[Prosty praktyczny przykład zastosowania procesów i potoków nienazwanych]].

---
## Źródła i odniesienia:
- www.youtube.com/watch?v=Mqb2dVRe0uo&list=PLfqABt5AS4FkW5mOn2Tn9ZZLLDwA3kZUY&index=6