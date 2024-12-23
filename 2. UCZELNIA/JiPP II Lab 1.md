---
lastSync: Fri Oct 04 2024 23:17:00 GMT+0200 (czas środkowoeuropejski letni)
---
---
Utworzono: 2024-10-04 18:41
Tagi: [[INFORMATYKA]], [[C++]]

---

### 1. Kontenery

Język C++ udostępnia użytkownikowi "inteligentne tablice", które są nazywane kontenerami, a do najpopularniejszych z nich należą `vector` oraz `map`.

`Vector` to tablica, która dynamicznie zmienia swój rozmiar dostosowując swoją pojemność tak, aby zmieścić wszystkie elementy, które chcemy do niej włożyć. By dodać element do kontenera, należy użyć metod `insert()` lub `push_back()`. Dostęp i modyfikowanie elementów wektora odbywa się na takiej samej zasadzie, jak w regularnej tablicy.

Przykładowe zastosowanie kontenera `vector`:
```cpp
#include <vector>

int main() {
	vector<int> myTab;

	myTab.push_back(10);
	myTab.push_back(11);
	myTab.push_back(12);
	myTab.push_back(13);

	cout << myTab.size() << " myTab[0]" << myTab[0] << endl;
	return 0;
}
```

### 2. Przekazywanie obiektów do funkcji

W języku C++ wyróżniamy kolejno trzy sposoby przekazywania obiektów do funkcji, tj.:
- przekazanie obiektu ==przez kopię==, najczęściej wykorzystywany sposób przekazywania obiektów, służy głównie do przekazywania do funkcji typów prostych, modyfikacja kopii obiektu nie wpływa na obiekt oryginalny,
```cpp
void fun(string txt) {
    //do sth
}

int main() {
    string txt;
    fun(txt);

    return 0;
}
```
- przekazanie obiektu ==przez referencję==, sposób przekazywania znacząco optymalizujący kod w przypadku potrzeby przekazania do funkcji typu złożonego i obiektów zajmujących dużo miejsca w pamięci operacyjnej komputera, przez co unikamy ich powielania i zużywamy de facto dwa razy mniej pamięci. Modyfikacja w tym przypadku zmienia obiekt oryginalny. Jeżeli naszym celem jest zmodyfikowanie obiektu wewnątrz funkcji, w dalszym ciągu będziemy przekazywać go przez kopię,
```cpp
void fun(const string &txt) {
    //do sth
}

int main() {
    string txt;
    fun(txt);

    return 0;
}
```
- przekazanie obiektu ==przez wskaźnik==.

### 3. Polimorfizm

Ogólna definicja polimorfizmu to przypisanie jednego interfejsu do zmiennych i obiektów różnych typów. Polimorfizm możemy podzielić na:
#### a) Polimorfizm statyczny
==Polimorfizm statyczny== (działania wykonywane w czasie kompilacji, and. compile-time) możemy osiągnąć przy użyciu ==przeciążeń funkcji oraz szablonów funkcji i klas.==
#### b) Polimorfizm dynamiczny
==Polimorfizm dynamiczny== (działania wykonywane na bieżąco w czasie, w którym program jest uruchomiony, ang. "run-time") możemy osiągnąć przy pomocy ==funkcji wirtualnych, które dostarczają interfejsu zawartego w klasie bazowej.==

Ponadto, ==polimorfizm dynamiczny pozwala przechowywać obiekt typu klasy pochodnej w obiekcie typu klasy bazowej.== Załóżmy, że klasa `Figure` jest wirtualną klasą bazową, a klasy `Circle` i `Rectangle` to klasy pochodne, których część metod pokrywa się z klasą bazową. Możemy wtedy zastosować następujący kod:
```cpp
int main() {
	Figure *fig = new Circle();
	
	Rectangle rect = new Rectangle();
	Figure& fig1 = rect;

	return 0;
}
```
==Polimorfizm dynamiczny działa tylko i wyłącznie ze wskaźnikami i referencją.==

---
## Źródła i odniesienia:

- https://filipkrzysiekpk.github.io/page/materials/jipp-ii-2024-n/z2/