---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---
---
Utworzono: 2024-10-09 07:03
Tagi: [[INFORMATYKA]], [[C]]

---
### 1. Do czego i dlaczego używamy wskaźników?

By zwizualizować ogólny zamysł, posłużmy się przykładem funkcji swap:
```c
void swap(int num1, int num2) {
	int temp;
	printf("num1 before swap = %d \n", num1);
	printf("num2 before swap = %d \n", num2);
	temp = num1;
	num1 = num2;
	num2 = temp;
	printf("num1 after swap = %d \n", num1);
	printf("num2 after swap = %d \n", num2);
}
```

Po wywołaniu tej funkcji w konsoli otrzymamy na przykład następujący wynik:
```bash
num1 before swap = 5
num2 before swap = 7
num1 after swap = 7
num2 after swap = 5
```

Powyższa funkcja zawiera jednak poważną wadę, mianowicie przy każdym jej wywołaniu wypisuje wyniki swojego działania na ekran, co może nie być przez nas pożądane. Rozważmy w takim razie funkcję bez tej funkcjonalności, gdzie wyniki będziemy sprawdzać przed i po wywołaniu funkcji `swap` wewnątrz ciała funkcji `main`:
```c
void swap(int num1, int num2) {
	int temp;
	temp = num1;
	num1 = num2;
	num2 = temp;
}

int main() {
	int a = 5, b = 7;
	printf("num1 before swap = %d \n", a);
	printf("num2 before swap = %d \n", b);
	swap(a, b);
	printf("num1 after swap = %d \n", a);
	printf("num2 after swap = %d \n", b);

	return 0;
}
```

Po wywołaniu ten funkcji w konsoli pokaże się następujący komunikat:
```bash
num1 before swap = 5
num2 before swap = 7
num1 after swap = 5
num2 after swap = 7
```

Dzieje się tak dlatego, że wywołując funkcję w sposób przedstawiony powyżej, ***obiekt do funkcji przekazujemy przez kopię***, czyli "wrzucamy" do niej kopie wartości przypisanych do zmiennych `a` i `b`, a nie faktycznie te zmienne. W związku z powyższym ==zmienne `a` i `b` pozostają niezmienione.==

### 2. Adres zmiennej w pamięci

Każda zmienna posiada jednak wartość unikatową, jaką jest jej ***adres***, który jest fizycznym miejscem w pamięci RAM lub cache wewnątrz maszyny. Adres danej zmiennej mówi nam wprost, jakie miejsca w pamięci ona zajmuje. 

By wykorzystać adresy jakichś zmiennych, musimy stworzyć nowe ***zmienne typu wskaźnik na adres pierwotny***. Tak jak zmienne typów prostych przechowują w sobie pewnego rodzaju zadane wartości:

![[Pasted image 20241014200509.png | center | ]]

tak ***wskaźniki do tych zmiennych przechowują w sobie adresy tych zmiennych***:

![[Pasted image 20241014201232.png | center]]

### 3. Dodatkowe motywacje stojące za używaniem wskaźników:

- pozwalają na wykorzystanie ***przekazania przez wskaźnik*** w przypadku przekazywania typów złożonych i typów prostych, kiedy chcemy zmodyfikować wartości faktycznych zmiennych, a nie tylko ich kopii,
- "zwracanie" więcej niż jednej rzeczy z funkcji, ponieważ funkcje w C mogą zwracać maksymalnie jeden obiekt,
- wygodne przekazywanie tablic i stringów do funkcji w C,
- przypisanie odpowiedniej ilości pamięci w trakcie działania programu (runtime), zamiast przypisywania statycznej ilości pamięci, która może okazać się za mała (nastąpi tzw. ***buffer overflow***, czyli przepełnienie bufora na dane), lub za duża, przez co zmarnujemy często cenne miejsce w pamięci, co jest niedopuszczalne przy kodowaniu hiper-zoptymalizowanych programów na np. mikrokontrolery.

---
## Źródła i odniesienia:

- https://www.udemy.com/course/c-programming-pointers-from-zero-to-hero/learn/lecture/23770770#overview
