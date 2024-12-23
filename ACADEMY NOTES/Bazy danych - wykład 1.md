---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---
---
Utworzono: 2024-10-12 14:39
Tagi: [[INFORMATYKA]], [[BAZY DANYCH]]

---
### 1. Co umożliwiają bazy danych i systemy zarządzania nimi?

- Łatwość składowania dużych ilości danych,
- Przejrzystość danych dzięki odpowiednim strukturom (np. tabele i relacje między tymi tabelami),
- Szybki dostęp do danych,
- Współdzielenie danych przez wielu użytkowników,
- Zabezpieczenia przed utratą danych,
- Dostarczają środowiska programistycznego,
- Współpraca z wieloma platformami programistycznymi,
- Dostarczają licznych specjalistycznych funkcji,
- Mają zaimplementowaną politykę bezpieczeństwa.

### 2. Rodzaje baz danych:

- Relacyjne (ang. RDBMS - Relational Database Management System): dane są grupowane w relacje (zbiory rekordów), reprezentowane w postaci tabel, relacje zgrupowane są w schematy. ==Kolumny nazywamy atrybutami, wiersze krotkami.==
- Obiektowe (and. OODBMS - Object-Oriented Database Management System): udostępniane w postaci obiektów (analogicznie jak w środowiskach programistycznych bazujących na obiektach),
- Relacyjno-obiektowe: hybryda powyższych rozwiązań, m.in. tabele mogą przechowywać krotki lub obiekty,
- Nierelacyjne: NoSQL, BigData, dane przechowywany w postaci: JSON, BSON, XML

### 3. Relacyjny system zarządzania bazami danych

##### Główne cechy RDBMS obejmują:

1. Relacyjną strukturę danych: dane są przechowywane w postaci tabel ,z których każda składa się z wierszy i kolumn. Relacje między tabelami określane są za pomocą kluczy obcych,
2. Deklaratywny język zapytań: RDBMS używają języka zapytań, takiego jak SQL, który umożliwia programistom i użytkownikom zadawanie złożonych zapytań do bazy danych w sposób deklaratywny,
3. Bezpieczeństwo danych: RDBMS zapewniają mechanizmy bezpieczeństwa, takie jak autoryzacja i uwierzytelnianie, aby kontrolować dostęp do danych i zapewnić poufność, integralność i dostępność danych,
4. Integrowane narzędzia administracyjne: RDBMS oferują narzędzia do zarządzania bazą danych, takie jak tworzenie tabel, indeksowanie, optymalizacja zapytań, zarządzanie użytkownikami, monitorowanie wydajności itp.,
5. Transakcyjność: RDBMS obsługują transakcje, które zapewniają spójność danych i atomowość operacji.

##### Popularne relacyjne systemy zarządzania bazami danych:

- MySQL: jeden z najpopularniejszych otwarto-źródłowe RDBMS na świecie,
- Microsoft SQL Server: rozbudowany RDBMS opracowany przez Microsoft,
- IBM Db2: opracowany przez IBM, znany ze skalowalności i zastosowań w rozwiązaniach biznesowych,
- Oracle Database: popularny w zastosowaniach komercyjnych,
- PostgreSQL: otwarto-źródłowy i rozbudowany RDBMS z silnym naciskiem na zgodność ze standardami SQL.
- MariaDB: jest rozwiniętą wersją MySQL, zapewniającą dodatkowe funkcje i ulepszenia. MariaDB jest również dostępny jako otwarto-źródłowe oprogramowanie.

##### Najważniejsze cechy MariaDB:

- **Jeden z najpopularniejszych systemów** zarządzania relacyjnymi bazami danych na świecie,
- **Otwarty kod źródłowy**,
- **Kompatybilność z MySQL**,
- **Wsparcie społeczności**,
- **Wsparcie dla JSON**,
- **Wsparcie dla GIS**,
- **Skalowalność i wydajność**,
- **Zaawansowane mechanizmy replikacji**,
- Każda tabela jest obsługiwana przez inny **silnik bazodanowy**, którzy przydziela użytkownik, najpopularniejsze z nich to:
	- MyISAM: posiada algorytmy w celu szybkiego wyszukiwania danych w tabeli, jeden z najstarszych tutaj wymienionych,
	- InnoDB: posiada mechanizm obsługujący transakcje bazodanowe,
	- FEDERATED: umożliwia połączenie z konkretną tabelą w konkretnej bazie danych na innym serwerze,
	- Memory: przechowuje dane w pamięci RAM, po reboocie dane są już całkowicie niedostępne,
	- CSV: umożliwia czytanie plików w postaci CSV.

### 4. Projektowanie własnej bazy danych

***Normalizacja baz danych*** to proces mający na celu zlikwidowanie redundancji, czyli elementów powtarzających się w bazie danych. Dzięki normalizacji zmniejsza się ryzyko powstawania:
- **nadmiarowości danych**: redundancja (w tym przypadku niepożądana), ma miejsce wtedy, kiedy dana wartość jest <u>niepotrzebnie</u> powielana w krotkach danego atrybutu (kolumny). 
- **anomalii modyfikacji (aktualizacji)**: chęć zmiany powielanej informacji wymusza zmianę tej informacji we wszystkich krotkach. Pominięcie choćby jednej krotki powoduje utratę spójności danych,
- **anomalii usunięć**: ma miejsce wtedy, gdy usuniemy wiersz, który przechowywał istotną informację, która w konsekwencji została niepotrzebnie utracona,
- **anomalii wstawiania**: powstaje kiedy np. podczas dodawania kolejnego wiersza użytkownik popełni błąd w wartości powielanej danej, w wyniku czego baza traci spójność. 

***Denormalizacja baz danych*** odnosi się do celowego zastosowania redundancji danych w celu poprawy wydajności odczytu i uproszczenia złożonych operacji zapytań. Jest używana w sytuacjach, gdy priorytetem jest optymalizacja wydajności odczytu danych kosztem wydajności zapisu. Przykładami takich sytuacji są: hurtownie danych, raportowanie, aplikacje, które wykonują więcej operacji odczytu niż zapisu.

##### Prosty przepis na względnie poprawny projekt bazy danych w aspekcie normalizacji:

- Wypunktować to, co powinna przechowywać baza danych,
- Pogrupować w tabelce powyższe dane tak, aby jednoznacznie charakteryzowały dany obiekt,
- Poszczególne kolumny (jeżeli wymaga tego kontekst projektu/problemu) powinny zawierać dane atomowe, np. kolumna `adres` powinna zostać rozbita na kolumny przechowujące nazwę ulicy, numer domu, numer mieszkania, miasto, kod pocztowy itd.,
- Każda tabela powinna zawierać kolumnę jednoznacznie identyfikującą wiersze w pozostałych kolumnach (tzw. klucz główny),
- Wyeliminować zależności przechodnie, czyli wykluczyć takie kolumny, których wartości nie są bezpośrednio zależne od klucza głównego, ale od innej kolumny.

---
## Źródła i odniesienia:
