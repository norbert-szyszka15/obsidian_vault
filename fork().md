
---
Utworzono: 2025-01-17 21:03
Tagi: [[INFORMATYKA]], [[LINUX]], [[C]]

---

### **Zastosowanie, parametry i wartości zwracane**
Funkcja `fork()` w systemach Linux służy do utworzenia procesu zwanego procesem potomnym, który działa współbieżnie do procesu-rodzica. Po utworzeniu potomka, oba procesy wykonują kolejne instrukcje, zapisane po funkcji `fork()`. Proces potomny wykorzystuje te same rejestry i otwarte pliki, co proces-rodzic. Funkcja nie pobiera żadnych parametrów i zwraca wartość integer.

Wartości zwracane przez funkcję `fork()`:
- **Wartość negatywna** (najczęściej -1) - utworzenie procesu potomnego zakończyło się niepowodzeniem,
- **0** - wartość zwracana do świeżo utworzonego procesu,
- **Wartość pozytywna** (PID nowo powstałego procesu) - wartość zwracana do procesu rodzicielskiego.




---
## Źródła i odniesienia:
