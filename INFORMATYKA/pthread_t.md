---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---

---
Utworzono: 2025-01-24 11:54
Tagi: [[INFORMATYKA]], [[LINUX]], [[C]]

---

### **Czym właściwie jest zmienna `pthread_t`?**
`pthread_t`, po najechaniu na niego kursorem w większości IDE, okazuje się być zmienną o typie `unsigned long` (lub inną o podobnej strukturze, więcej na ten temat w uwadze poniżej). W związku z tym wartości tych zmiennych można bez problemu wyprowadzać np. do standardowego wyjścia programu, tak jak poniżej:

```c
#include <pthread.h>
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <string.h>
#include <time.h>
#include <sys/syscall.h>

#define THREAD_NUM 2

void* routine(void* args) {
    pthread_t th = pthread_self();
    printf("%ul\n", th);
}

int main(int argc, char *argv[]) {
    pthread_t th[THREAD_NUM];
    
    int i;
    for (i = 0; i < THREAD_NUM; i++) {
        if (pthread_create(&th[i], NULL, &routine, NULL) != 0) {
            perror("Failed to create thread");
        }
        // printf("%ul\n", th[i]); - wypisanie na ekran wartości
        // zmiennych pthread_t zamkniętych w tablicy th
    }

    for (i = 0; i < THREAD_NUM; i++) {
        if (pthread_join(th[i], NULL) != 0) {
            perror("Failed to join thread");
        }
    }
    return 0;
}
```

Wartość zmiennej `pthread_t` właściwie jest **identyfikatorem wątku na poziomie API `pthread_t`**. [[pthread_create()]] ustawia te liczby na utworzonej przez programistę tablicy typu `pthread_t`, a [[pthread_join()]] w oparciu o nie identyfikuje wątki.

Jeżeli wartość identyfikatora konkretnego wątku jest potrzebna wewnątrz funkcji jego rutyny, można ją wydobyć wykorzystując do tego funkcję [[pthread_self()]].

==UWAGA==: zazwyczaj używanie identyfikatora wątku jako zwykłej liczby jest **bardzo mocno niewskazane!** Powodem takiego stanu rzeczy jest fakt, że `pthread_t` należy do typów danych znanych jako [[Nieprzezroczyste typy danych (opaque data types)]] dokumentacja API wskazuje, że programista tak właśnie powinien traktować te zmienne. Oznacza to, że implementacja została ukryta przed użytkownikiem, co jest standardową czynnością wykonywaną podczas pisania bibliotek do języków programowania. Co za tym idzie, w zależności od konkretnego wykorzystania zmiennej, `pthread_t` niekoniecznie musi być typu `unsigned long`, ale może należeć także do typów `unsigned long long`, `int`, a wreszcie może nawet być ciągiem tekstowym albo strukturą wielu innych typów pomniejszych. W skrócie, **nigdy nie należy zakładać, że obiekt `pthread_t` będzie jakimś konkretnym, określonym typem prostym lub złożonym**.

---
### **Wyłuskiwanie ID wątku na poziomie systemu operacyjnego (tylko Linux)**
Kolejnym istotnym konceptem jest zastosowanie funkcji systemowych i wywołania `syscall(SYS_gettid)` do wydobycia identyfikatora konkretnego wątku na poziomie systemu operacyjnego. Jest to inny numer niż identyfikator używany do identyfikacji wątków przez API `pthread_t`. Za przykład posłuży nieco rozbudowany kod z poprzedniego podpunktu:

```c
#include <pthread.h>
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <string.h>
#include <time.h>
#include <sys/syscall.h>

#define THREAD_NUM 2

void* routine(void* args) {
    pthread_t th = pthread_self();
    printf("%ul\n", th); // wydobycie ID wątku na poziomie API
    printf("%d\n", (pid_t) syscall(SYS_gettid)); // wydobycie ID wątku na poziomie systemu operacyjnego Linux
}

int main(int argc, char *argv[]) {
    pthread_t th[THREAD_NUM];
    
    int i;
    for (i = 0; i < THREAD_NUM; i++) {
        if (pthread_create(&th[i], NULL, &routine, NULL) != 0) {
            perror("Failed to create thread");
        }
    }

    for (i = 0; i < THREAD_NUM; i++) {
        if (pthread_join(th[i], NULL) != 0) {
            perror("Failed to join thread");
        }
    }
    return 0;
}
```

---
## Źródła i odniesienia:
- https://www.youtube.com/watch?v=4VQmuj1RrCk&list=PLfqABt5AS4FmuQf70psXrsMLEDQXNkLq2&index=16
- https://code-vault.net/lesson/18ec1942c2da46840693efe9b5210e1b