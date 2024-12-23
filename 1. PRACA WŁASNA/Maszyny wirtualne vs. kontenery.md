---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---

---
Utworzono: 2024-11-26 18:55
Tagi: [[INFORMATYKA]], [[SERWER]]

---
### **Maszyny wirtualne kontra kontenery na serwerze Proxmox**
Główną różnicą między maszyną wirtualną a kontenerem jest fakt, że kontener symuluje jedynie części systemu operacyjnego wymagane do uruchomienia danej aplikacji.


| FUNKCJA                     | MASZYNA WIRTUALNA                                                                                                                         | KONTENER                                                                                                                                                                                                                    |
| --------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **SYMULACJA SYSTEMU**       | Symuluje ***cały system*** operacyjny                                                                                                     | Symuluje ***fragmenty systemu*** operacyjnego wymagane do uruchomienia danej aplikacji                                                                                                                                      |
| **ZUŻYCIE RAMU**            | Pobiera z maszyny serwera ***całą*** zaalokowaną dla maszyny wirtualnej pamięć operacyjną                                                 | Pobiera z maszyny serwera ***maksymalnie*** tyle pamięci RAM, ile zostało przypisane do kontenera                                                                                                                           |
| **ZUŻYCIE ZASOBÓW MASZYNY** | Pobiera pełnię zasobów wymaganych do pełnoprawnego i funkcjonalnego działania pełnego systemu operacyjnego przez wzgląd na jego symulację | Pobiera tylko część zasobów konieczną do poprawnego funkcjonowania elementów systemu operacyjnego wpływających na działanie aplikacji, jak i samej aplikacji                                                                |
| **MIGRACJA**                | Maszynę wirtualną można zmigrować na żywo (***Live Migration***) między poszczególnymi węzłami w klastrze serwerów                        | Kontener za przy każdej migracji zostanie wyłączony, pliki i dane zostaną przekopiowane z jednego węzła na drugi, po czym nowy kontener zostanie uruchomiony (migracja na żywo nie jest możliwa)                            |
| **WSPARCIE DLA APLIKACJI**  | Maszyna wirtualna jest w stanie uruchomić każdą aplikację przygotowaną dla systemu operacyjnego, który jest wewnątrz niej uruchomiony     | Kontenery mogą mieć problemy z kompatybilnością z niektórymi aplikacjami (w szczególnych przypadkach twórcy niektórych aplikacji lub oprogramowania są w stanie odmówić wsparcia dla aplikacji uruchomionych w kontenerach) |





---
## Źródła i odniesienia:
- https://www.youtube.com/watch?v=JsexhY_gQZY&list=PLT98CRl2KxKHnlbYhtABg6cF50bYa8Ulo&index=5