---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---

---
Utworzono: 2025-01-18 22:12
Tagi: [[INFORMATYKA]], [[LINUX]], [[C]]

---

### **System call `pthread_cancel()`**
Ta funkcja systemowa wysyła do wątku `thread` żądanie jego anulowania. To, czy wątek zostanie anulowany, zależy od dwóch rzeczy: jego podatności na anulowanie `state` (z ang. *cancelability state*) oraz typu wątku `type`.

**Podatność wątku na anulowanie `pthread_setcancelstate()`** może być włączona lub wyłączona. Jeżeli anulowania wątku są wyłączone, żądanie anulowania wątku będzie czekać aż do momentu, kiedy anulowania zostaną włączone. Kiedy możliwość anulowania wątku zostanie ponownie włączona, żądanie zostanie odpowiednio zakolejkowane. Czas, kiedy nastąpi anulowanie wątku, zależy teraz od typu anulowania wątku.

**Typ anulowania wątku, opisany przez `pthread_setcanceltype()`**, może być **asynchroniczny** lub **odroczony**:
- **anulowanie asynchroniczne** oznacza, że wątek może być zakończony w dowolnym momencie, najczęściej natychmiast (chociaż nie jest to zapewnione przez system operacyjny),
- **anulowanie odroczone** oznacza, że anulowanie wątku zostanie odroczone do momentu, w którym wątek wywoła funkcję oznaczoną jako `cancelation point`.

Kiedy żądanie anulowania wątku zostaje wysłane, zachodzą następujące zdarzenia:
1. Procedury czyszczenia wątku przeprowadzają swoje działania (w kolejności odwrotnej niż kolejność ich wywołania),
2. Specyficzne dla wątku destruktory danych są wywoływanie w bliżej nieokreślonym porządku,
3. Wątek zostaje anulowany.

Powyższe kroki są wykonywane asynchronicznie w stosunku do wywołania `pthread_cancel()` - kod zwrotu tej funkcji służy bardziej poinformowaniu użytkownika, że żądanie anulowania wątku zostało wysłane poprawnie.

Kiedy anulowany wątek zostanie zakończony, dołączenie tego wątku przy użyciu system calla [[pthread_join()]] wyrzuci status `PTHREAD_CANCELED`.

Syntax system calla `pthread_cancel()` w języku C:
```c
#include <pthread.h>

int pthread_cancel(pthread_t thread);
```

Wartości zwracane:
- w przypadku sukcesu, funkcja zwraca wartość 0;
- w przypadku błędu, funkcja zwraca niezerowy kod tego błędu.




---
## Źródła i odniesienia:
- https://man7.org/linux/man-pages/man3/pthread_cancel.3.html