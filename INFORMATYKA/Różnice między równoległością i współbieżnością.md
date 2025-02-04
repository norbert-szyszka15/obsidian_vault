---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---

---
Utworzono: 2025-01-30 17:51
Tagi: [[INFORMATYKA]], [[LINUX]], [[C]]

---
### **Równoległość wątków**
Równoległość w skrócie polega na uruchomieniu **wielu wątków wykonujących ten sam kod, ale na różnych danych**. Zakładając że CPU ma na tyle mocy, by obsłużyć zakładaną przez programistę liczbę wątków, można stwierdzić, że program jest szybszy o tyle razy, ile wątków wykonuje się w jego ramach. ![[Pasted image 20250130181955.png | center]]
Jednym z lepszych przykładów wątków równoległych jest kod zawarty w notatce [[Prosty praktyczny przykład zastosowania wątków]]. Obliczanie liczb pierwszych dzieje się tam poprzez podział wejściowej tablicy na dwie mniejsze, na których to dopiero wątki wykonują swoje działania.

---
### **Współbieżność wątków**
Współbieżność wątków zachodzi wówczas, kiedy wszystkie wątki w jej ramach wykonują pewne działania na pamięci współdzielonej, a nie na swoim własnym zestawie danych. Zazwyczaj współbieżność jest niepożądanym elementem kodu, ponieważ wątki wykonując swoje operacje często muszą na siebie np. czekać, co powoduje, że taki program wielowątkowy wcale nie jest szybszy od identycznego programu wykonującego się bez pomocy wątków. Oczywiście, współbieżności nie zawsze można uniknąć, lub czasami nawet jest ona elementem pożądanym w jakimś rozwiązaniu (np. tworzenie symulacji, gdzie każdy wątek jest innym jej elementem, znacząco zwiększa jej czytelność). ![[Pasted image 20250130182120.png | center]]
Przykładem współbieżności wątków może być kod zawarty w notatce [[Mutexy]]. Wszystkie wątki wykonują tam mniej więcej ten sam kod na zmiennej `mails`.

---
### **Odniesienie do rzeczywistości**
Oczywiście w realnych programach bardzo rzadko zdarza się, by jakieś wątki działały w pełni współbieżnie lub równolegle. Wtedy tak naprawdę pod uwagę bierze się niejako balans między tymi dwoma formami operacji, i pożądanym jest, by ten balans jak najbardziej zmierzał w stronę współbieżności.

---
## Źródła i odniesienia:
- https://www.youtube.com/watch?v=56OSQSoRR04&list=PLfqABt5AS4FmuQf70psXrsMLEDQXNkLq2&index=27