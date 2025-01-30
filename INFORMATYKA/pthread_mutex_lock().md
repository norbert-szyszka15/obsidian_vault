---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---

---
Utworzono: 2025-01-19 15:16
Tagi: [[INFORMATYKA]], [[LINUX]], [[C]]

---

### **System call `pthread_mutex_lock()`**
Mutex powinien zostać zablokowany przez odwołanie do funkcji `pthread_mutex_lock()`, która zwraca zero lub **EOWNERDEAD**. Jeżeli mutex jest już zablokowany przez inny wątek, wątek wzywający funkcję powinien zostać zablokowany do momentu, aż mutex ponownie będzie dostępny. Ta operacja powinna zwrócić obiekt mutexa `mutex` w stanie zablokowanym i z wątkiem wywołania funkcji jako swoim właścicielem. Jeżeli wątek zacznie próbować relockować mutex który jest już przez niego zablokowany, funkcja powinna zachować się tak, jak opisano w tabeli w kolumnie **Relock**. Jeżeli wątek próbuje odblokować mutex, którego ten wątek nie zablokował, lub mutex, który jest odblokowany, funkcja powinna zachować się tak, jak opisano w tabeli w kolumnie **Unlock When Not Owner**.

| **Mutex Type** | **Robustness** | **Relock**              | **Unlock When Not Owner** |
| -------------- | -------------- | ----------------------- | ------------------------- |
| NORMAL         | non-robust     | deadlock                | undefined behavior        |
| NORMAL         | robust         | deadlock                | error returned            |
| ERRORCHECK     | either         | error returned          | error returned            |
| RECURSIVE      | either         | recursive (patrz niżej) | error returned            |
| DEFAULT        | non-robust     | undefined behavior      | undefined behavior        |
| DEFAULT        | robust         | undefined behavior      | error returned            |

Kiedy tabela wskazuje zachowanie rekursywne, mutex musi zachować koncept `lock count`. Kiedy wątek po raz pierwszy otrzymuje dostęp do jakiegoś mutexa, `lock count` musi być ustawione na wartość 1. Za każdym razem, kiedy wątek relockuje mutex, wartość `lock count` powinna zostać zwiększona o 1. Analogicznie, kiedy wątek odblokowuje mutex, powinien zmniejszać wartość `lock count` o 1. Kiedy `lock count` dojdzie do zera, mutex powinien stać się dostępnym do przejęcia dla innych wątków.

Funkcja [[pthread_mutex_trylock()]] działa na podobnej zasadzie co `pthread_mutex_lock()`, z tą różnicą, że kiedy obiekt mutexa `mutex` jest obecnie zablokowany przez dowolny proces (wliczając w to proces, z którego funkcja została wywołana), następuje natychmiastowe zwrócenie wartości przez funkcję. Jeżeli wątek mutex jest obecnie własnością wątku, który wywołuje funkcję, i typ mutexu jest ustawiony na `PTHREAD_MUTEX_RECURSIVE`, `pthread_mutex_trylock()` powinien natychmiast zwrócić sukces, a wartość `lock count` powinna ulec inkrementowi o 1. Po więcej różnic między tymi dwiema funkcjami lub dokładniejszy opis różnic wymienionych tutaj wraz z przykładami, patrz: [[pthread_mutex_trylock()]].

Funkcja [[pthread_mutex_unlock()]] uwalnia obiekt mutexa opisywany przez argument `mutex`.

Jeżeli do wątku czekającego na mutex zostanie dostarczony sygnał, po zakończeniu obsługi tego sygnału (czyli tuż po zwróceniu przez procedurę obsługującą sygnał), wątek powinien kontynuować oczekiwanie ma mutex.

Jeżeli mutex jest ustawiony jako robust mutex i zakończony zostanie proces, który jest właścicielem mutexa, wywołanie `pthread_mutex_lock()` powinno zwrócić kod błędu **EOWNERDEAD**.

Jeżeli mutex jest ustawiony jako robust mutex i wątek będący właścicielem tego mutexu zostanie zakończony w trakcie utrzymywania blokady mutexa, wywołanie `pthread_mutex_lock()` powinno zwrócić kod błędu **EOWNERDEAD** nawet wtedy, kiedy proces tego wątku nie jest zakończony.

Syntax system calla `pthread_mutex_lock()` w języku C:

```c
#include <pthread.h>

int pthread_mutex_lock(pthread_mutex_t *mutex);
int pthread_mutex_trylock(pthread_mutex_t *mutex);
int pthread_mutex_unlock(pthread_mutex_t *mutex);
```

Wartości zwracane:
- w przypadku sukcesu, wszystkie powyższe funkcje zwracają wartość 0,
- w przypadku niepowodzenia, zwrócą one kod numer błędu.


---
## Źródła i odniesienia:
- https://man7.org/linux/man-pages/man3/pthread_mutex_lock.3p.html