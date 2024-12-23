---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---
---
Utworzono: 2024-10-19 14:09  
Tagi: [[INFORMATYKA]], [[BAZY DANYCH]]

---
### 1. Postacie normalne

 ***Klucz główny (ang. primary key)*** - pojęcie w relacyjnych bazach danych odnoszące się do zestawu jednej lub więcej kolumn w tabeli, które razem zapewniają unikalność wiersza w tabeli. Cechy klucza głównego to: 
- unikalność,
- niezmienność,
- Nie może być pusty,
- może być złożony,
- integralność,
- pojedynczość.
<br>

***Klucz kandydujący*** - to kolumna lub zestaw kolumn w tabeli bazy danych, które mogą być używane jako klucz główny. Innymi słowy, klucz kandydujący jest unikalny dla każdego wiersza w tabeli. Główne cechy klucza kandydującego to: 
- **Unikalność**,
- **Brak wartości NULL**.

Przykładami kluczy kandydujących mogą być np. numery PESEL, numery indeksu lub loginy użytkowników do jakiegoś serwisu.
<br>

***Klucz obcy*** - kolumna lub zestaw kolumn w danej tabeli służący do utrzymania relacji między dwiema tabelami. W jednej z tabel klucz obcy wskazuje na klucz główny, znajdujący się w drugiej tabeli. Cechy:
- **Tworzenie relacji między tabelami**,
- **Integralność referencyjna**: klucz obcy zapewnia integralność i spójność danych,
- **Zapobieganie niespójnościom danych**,
- **Operacje kaskadowe**: np. po usunięciu klucza głównego z jednej tabeli, z drugiej usuwane są klucze obce,
- **Wydajność**: bazy danych używają indeksów na kluczach głównych do zwiększenia wydajności bazy danych,
- **Wielokolumnowe klucze obce**: klucz obcy może składać się z więcej niż jednej kolumny.
<br>

***Pierwsza postać normalna (1PN)***:
- Każda tabela ma klucz główny: unikalny identyfikator dla każdego wiersza,
- Wszystkie kolumny w tabeli przechowują jedną wartość logiczną - atomową (brak zbiorów, list czy innych struktur wieloelementowych),
- Kolejność wierszy i kolumn jest nieistotna.
<br>

***Druga postać normalna (2PN)***:
- Tabela jest w 1PN,
- Wszystkie niekluczowe kolumny są funkcjonalnie zależne od całego klucza głównego, a nie tylko od jego części (dotyczy to przede wszystkim tabel z kluczami złożonymi).
<br>

***Trzecia postać normalna (3PN)***:
- Tabela jest w 2PN,
- Wszystkie atrybuty są funkcjonalnie zależenie tylko od klucza głównego, a nie od innych niekluczowych atrybutów.

### 2. Diagramy ERD

Podstawowe pojęcia:
- encja - reprezentacja obiektu ze świata rzeczywistego,
- atrybut - element encji wchodzący w skład jej cech ją identyfikujących,
- związek - powiązanie między dwoma lub więcej encjami.

---
## Źródła i odniesienia:

