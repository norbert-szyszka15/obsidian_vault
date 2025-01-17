---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---

---
Utworzono: 2025-01-17 18:55
Tagi: [[INFORMATYKA]], [[LINUX]], [[C]]

---

### **Procesy**
**Procesem** można nazwać każdy program, który został uruchomiony i jest obecnie wykonywany. W praktyce, procesor komputera może wykonywać tylko jeden proces jednocześnie, jednak w dobie potężnych procesorów możliwe jest bardzo krótkotrwałe przydzielanie zasobów CPU do procesu, a on i tak zostanie wykonany w rozsądnym czasie. To daje złudzenie wykonywania wielu procesów równolegle do tego stopnia, że technicznie rzecz ujmując UNIX jest systemem **wieloprocesowym**.

Do wyświetlania listy procesów w Linuxie służy komenda `ps`. Tabela niektórych opcji polecenia:

| **Polecenie**    | **Opis**                      |
| ---------------- | ----------------------------- |
| `ps -f`          | Dodatkowe parametry procesu   |
| `ps -l`          | Szczegółowe parametry procesu |
| `ps -e`          | Wszystkie parametry procesu   |
| `ps -u username` | Procesy użytkownika username  |
| `ps -o format`   | Parametry procesu wg format   |

<br>

Tabela przedstawiająca wybrane parametry procesu:

| **Parametr** | **Opis**                                 |
| ------------ | ---------------------------------------- |
| UID          | Efektywny identyfikator użytkownika      |
| USER         | Efektywna nazwa użytkownika              |
| RUID         | Rzeczywisty identyfikator użytkownika    |
| RUSER        | Nazwa rzeczywistego użytkownika          |
| GID          | Efektywny identyfikator grupy            |
| GROUP        | Efektywna nazwa grupy                    |
| RGID         | Rzeczywisty identyfikator grupy          |
| RGROUP       | Rzeczywista nazwa grupy                  |
| PID          | Numeryczny identyfikator procesu         |
| PPID         | Numeryczny identyfikator procesu rodzica |

### Budowa procesu w Linuxie
Przestrzeń jedno procesu może być podzielona na dwa konteksty: **kontekst jądra** systemu operacyjnego i **kontekst użytkownika**, który dodatkowo może być podzielony na sześć obszarów:
1. **Obszar tekstu** - zawiera rozkazy maszynowe, wykonywane przez sprzęt. Obszar ten jest tylko do odczytu, może być współdzielony przez kilka procesów,
2. **Obszar stałych** - obszar tylko do odczytu. We współczesnych systemach jest łączony z obszarem tekstu w jeden obszar,
3. **Obszar zmiennych zainicjowanych** - zawiera zmienne, którym zostały przypisane pewne wartości początkowe, ale proces może je dowolnie modyfikować,
4. **Obszar zmiennych niezainicjowanych** - zawiera zmienne, które mają wartość początkową zero, a zatem nie trzeba przechowywać w plikach programu wartości inicjujących te zmienne,
5. **Sterta (and. heap)** - służy do dynamicznego przydzielania dodatkowego obszary w pamięci,
6. **Stos (ang. stack)** - przechowuje ramki stosu, czyli informacje związane z wywoływaniem podprogramów.
Dwa ostatnie we współczesnych systemach UNIX tworzą w zasadzie jeden obszar. Ponadto, sterta rozszerza się w stronę coraz wyższych adresów, a stos - w stronę niższych. **Proces użytkownika nie ma bezpośredniego dostępu do kontekstu jądra** systemu operacyjnego.

### **Tworzenie nowych procesów**
Obecnie wykonujący się proces może stworzyć swój proces potomny używając funkcji [[fork()]], udostępnianej w bibliotece standardowej języka C. W Linuxie funkcja ta jest "opakowaniem" na wywołanie funkcji `clone()`. **`clone()` nie jest wywołaniem standardowym**, co znaczy, że nie jest odstępne w innych systemach kompatybilnych z UNIX.

Linux używa wariantu  tworzenia procesów o nazwie **copy-on-write**, co znaczy, że proces-dziecko po utworzeniu współdzieli ze swoim rodzicem zarówno obszar tekstu, jak i obszar danych. Dopiero przy modyfikacji danych nastąpi rozdzielnie obszaru danych (proces potomny otrzyma kopię obszaru rodzica).

Aby wykonać nowy program, należy w procesie potomnej użyć jednego z wariantów funkcji [[exec]]. Proces-rodzic może poznać status wykonania swojego potomka wykonując jedną z funkcji [[wait()]]. Jeżeli rodzic nie wykona tej funkcji, proces-dziecko stanie się procesem zombie. Jeżeli rodzic zakończy się przed wykonaniem się potomka, zostanie on "adoptowany" przez proces `init`, czyli proces o PID równym 1, lub przez inne procesy należące do grupy procesu-rodzica.

Opis wyjątku powstałego przy powstawaniu procesu-dziecka (o ile takowy wystąpi), można dostać wykorzystując funkcję [[perror()]]. 

---

Poniżej prosty program, w którym za pomocą wywołania funkcji fork() tworzony jest proces potomny. Jeśli ta funkcja zwróci wartość -1, to znaczy, że nie udało się jej utworzyć potomka. Opis wyjątku, który spowodował to niepowodzenie, możemy uzyskać na ekranie dzięki funkcji perror(). Jako jej argument wystarczy przekazać ciąg znaków będący nazwą funkcji, ale w przypadku gdy ta funkcja jest używana kilkukrotnie w obrębie, a nawet kilku
plików, jeśli program jest podzielony na osobne jednostki kompilacji, to można jeszcze dodać do tej nazwy numer wiersza i nazwę pliku, w którym ta funkcja jest wywoływana. Po zakończeniu swojej pracy proces potomny kończy się wywołując funkcję exec(). Proces rodzicielski czeka na jego zakończenie wywołując funkcję wait(). Ponieważ funkcja exec() nie zwraca kodu zakończenia, to nie jest on badany, ale ta czynność wykonywana jest w przypadku funkcji wait().

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/wait.h>

void do_child_work(void) {
	puts("Jestem potomkiem.");
	exit(0);
}

void do_parent_work(void) {
	puts("Jestem rodzicem.");
	if (wait(0) < 0) {
		perror("wait");
	}
}

int main(void) {
	int pid = fork();
	if (pid < 0) {
		perror("fork");
	}
	if (pid == 0) {
		do_child_work();
	} else {
		do_parent_work();
	}
	
	return 0;
}
```

Tworzenie większej ilości procesów potomnych można podzielić na dwa główne typy ujęte w kolejnych przykładach, tj.:
- [[Sekwencyjne wykonywanie się procesów względem siebie]],
- [[Współbieżne wykonywanie się procesów względem siebie]].

### **Sygnały**
Sygnały można uznać za prostą formę komunikacji między procesami, ale głównie służą do poinformowania procesu, że nastąpiło jakieś zdarzenie (stąd pochodzi ich druga nazwa, **przerwy programowe**). Sygnały są asynchroniczne względem wykonania procesu, czyli nie można przewidzieć, kiedy się pojawią. Mogą być wysłane z jądra lub z innego procesu. Funkcja [[kill()]] umożliwia wysłanie sygnału do procesu o podanym PID. Każdy proces posiada strukturę, w której umieszczone są adresy procedur obsługi sygnałów. Jeżeli nie zostanie napisana żadna funkcja obsługująca dany sygnał, wykonywana jest procedura domyślna, która najczęściej powoduje natychmiastowe zakończenie procesu. Część sygnałów można zignorować lub zablokować je na określony czas. Istnieją sygnały, których nie można ani obsłużyć przy pomocy własnej funkcji, ani zignorować, ani zablokować, a przykładem takiego sygnału jest np. `sigkill`.

### **Krótkie opisy najważniejszych funkcji systemowych**:
- [[fork()]] - tworzy proces potomny. Funkcja zwraca dwie wartości: dla procesu macierzystego - PID potomka. Dla procesu potomnego - 0. Jeśli jej wywołanie nie powiedzie się, zwraca wartość -1,
- [[clone()]] - funkcja specyficzna na Linuxa, służy do tworzenia nowego procesu,
- [[getpid() i getppid()]] - funkcje zwracające odpowiednio: PID procesu bieżącego i PID procesu rodzica procesu bieżącego,
- [[sleep()]] - służy do zawieszenia działania procesu na określoną liczbę sekund,
- [[wait()]] - nie jest to jedna funkcja, ale cała rodzinka funkcji (np. `wait()`, `waitpid()`, `wait3()`, `wait4()`). Powodują one, że proces macierzysty czeka na zakończenie procesu potomnego,
- [[exit()]] - funkcja kończąca działanie procesu. Istnieje kilka innych podobnych funkcji,
- [[exec]] - rodzina funkcji (inne to np. `execl()`, `execlp()`, `execle()`, `execv()`, `execv()`), które zastępują obraz w pamięci aktualnie wykonywanego procesu obrazem nowego procesu, odczytanym z pliku,
- [[kill()]] - wysyła sygnał o określonym numerze do procesu o określonym PID,
- [[signal()]] - pozwala określić zachowanie procesu po otrzymaniu odpowiedniego sygnału. Z tą funkcją powiązane są funkcje `sigblock()` i `sigsetmask()`. Współcześnie, zamiast korzystania z `signal()`, poleca się używanie funkcji `sigaction()` i `sigprocmask()`,
- [[pause()]] - powoduje, że proces czeka na otrzymanie sygnału,
- [[alarm()]] - pozwala ustawić czas, po którym proces dostanie sygnał `sigalrm`,
- [[perror()]] - wypisuje na ekran komunikat związany z ostatnim napotkanym wyjątkiem pochodzącym od funkcji systemowej.




---
## Źródła i odniesienia:
- Lab_3_SO.pdf