
---
Utworzono: 2025-01-17 21:03
Tagi: [[INFORMATYKA]], [[LINUX]], [[C]]

---

### **System call `wait()` w języku C**
Wywołanie metody `wait()` w kodzie procesu powoduje zatrzymanie jego dalszego wykonania do momentu, kiedy jego proces potomny zostanie zakończony lub zostanie otrzymany odpowiedni sygnał. Po zakończeniu się procesu potomnego, rodzic kontynuuje wykonywanie tuż po wywołaniu system calla `wait()`.

Syntax w języku C wygląda następująco:

```c
#include <sys/wait.h>  
#include <unistd.h>  
  
// Waits for a child process to terminate and returns its PID  
pid_t wait(int *stat_loc);
```

Proces potomny może zakończyć się w wyniki jednego z poniższych zdarzeń:
- w kodzie procesu potomnego została wywołana funkcja `exit()`,
- zwraca on wartość `integer` ze swojej funkcji `main()` (równoznaczne z regularnym zakończeniem wykonywania się kodu),
- proces otrzyma sygnał od systemu operacyjnego lub innego procesu, którego domyślna akcja to zakończenie procesu, do którego został wysłany.

Zachowania system calla `wait()` obejmują:
- jeżeli proces-rodzic ma wielu potomków, `wait()` spowoduje oczekiwanie do momentu, w którym **jakikolwiek** z nich zostanie zakończony,
- jeżeli proces potomny zakończy się, `wait()` zwróci do procesu rodzica PID zakończonego właśnie potomka,
- jeżeli zakończy się więcej niż jeden proces potomny, `wait()` w sposób losowy wybierze jeden z tych procesów i zwróci jego PID do rodzica,
- jeżeli nie istnieje żaden potomek, `wait()` natychmiast zwraca wartość -1.

---

### **Przykład nr 1**
Proste użycie system calla `wait()`

```c
// C program to demonstrate working of wait()
#include <stdio.h>
#include <stdlib.h>
#include <sys/wait.h>
#include <unistd.h>

int main() {
    pid_t cpid;
    if (fork() == 0) {
        exit(0);  // Terminate child
    } else {
        cpid = wait(NULL);  // Reap parent
    }
    printf("Parent pid = %d\n", getpid());
    printf("Child pid = %d\n", cpid);
    return 0;
}
```

---

### **Przykład nr 2**

```c
#include <stdio.h>
#include <sys/wait.h>
#include <unistd.h>

int main() {
    if (fork() == 0)
        printf("HC: hello from child\n");
    else {
        printf("HP: hello from parent\n");
        wait(NULL);
        printf("CT: child has terminated\n");
    }
    printf("Bye\n");
    return 0;
}
```

Informacja statusu o potomku raportowana przez `wait()` to więcej niż tylko kod wyjścia procesu potomnego. Można do niej jeszcze zaliczyć:
- informacja o tym, czy proces został zakończony w sposób normalny, czy nietypowy,
- powód zakończenia procesu potomnego,
- kod wyjścia potomka.
W celu znalezienia informacji na temat statusu wyjścia procesu-dziecka, używa się makr `WIF...()`. Do tych makr wliczamy:
1. `WIFEXITED(status)` - sprawdzenie, czy dziecko zakończyło się normalnie,
	- `WEXITSTATUS(status` - otrzymanie kodu wyjścia lub zwrotu procesu potomnego
2. `WIFSIGNALED(status)` - sprawdzenie, czy dziecko skończyło się poprzez brak otrzymania sygnału,
	- `WTERMSIG(status)` - otrzymanie numeru sygnału, który zakończył działanie potomka,
3. `WIFSTOPPED(status)` - sprawdzenie, czy dziecko jest zatrzymane,
	- `WSTOPSIG(status)` - otrzymanie numeru sygnału stopu.

Przykład wykorzystania powyższych makr:

```c
#include <stdio.h>
#include <stdlib.h>
#include <sys/wait.h>
#include <unistd.h>

void waitexample() {
    int stat;
    if (fork() == 0) {
        exit(1);  // Child exits with status 1
    } else {
        wait(&stat);
    }
    
    if (WIFEXITED(stat)) {
        printf("Exit status: %d\n", WEXITSTATUS(stat));
    } else if (WIFSIGNALED(stat)) {
        psignal(WTERMSIG(stat), "Exit signal");
    }
}

int main() {
    waitexample();
    return 0;
}
```

---

### **Wskazanie do oczekiwania konkretnego procesu**
Jak wyżej, call `wait()` wybiera losowy proces, jednak jeżeli chcemy zatrzymać konkretny proces, należy użyć funkcji `waitpid()`.

Syntax system calla `waitpid()` w języku C:

```c
pid_t waitpid(pid_t child_pid, int *status, int options);
```

Parametry opcji system calla `waitpid()`:
- 0 oznacza brak opcji, rodzic musi czekać na zakończenie się dziecka,
- **WNOHANG** oznacza, że rodzic nie czeka na zakończenie się dziecka, jedynie sprawdza i zwraca wartość funkcji `waitpid()` (rodzic nie jest blokowany do czasu zakończenia się procesu potomnego),
- -1 oznacza dowolne dziecko. W tym przypadku `waitpid()` działa identycznie jak `wait()`.

Wartości zwracana przez funkcję `waitpid()`:
- PID potomka, jeżeli nastąpiło wyjście z jego procesu,
- 0, jeżeli użyto opcji **WNOHANG** i nie zakończyło działania.

---

### **Przykład nr 3 - wykorzystanie `waitpid()`**

```c
#include <stdio.h>
#include <stdlib.h>
#include <sys/wait.h>
#include <unistd.h>

void waitexample() {
    int i, stat;
    pid_t pid[5];
    for (i = 0; i < 5; i++) {
        if ((pid[i] = fork()) == 0) {
            sleep(1);
            exit(100 + i);
        }
    }
    
    for (i = 0; i < 5; i++) {
        pid_t cpid = waitpid(pid[i], &stat, 0);
        if (WIFEXITED(stat))
            printf("Child %d terminated with status: %d\n", cpid, WEXITSTATUS(stat));
    }
}

int main() {
    waitexample();
    return 0;
}
```






---
## Źródła i odniesienia:
- https://www.geeksforgeeks.org/wait-system-call-c/