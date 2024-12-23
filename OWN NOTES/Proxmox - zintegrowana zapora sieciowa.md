---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---
---
Utworzono: 2024-12-22 17:43
Tagi: [[INFORMATYKA]], [[SERWER]]

---
### **Umiejscowienie ustawień zapory sieciowej**
Jedną z najbardziej nieintuicyjnych rzeczy na początku przygody z Proxmoxem mogą być na pierwszy rzut oka losowo porozrzucane ustawienia zapory sieciowej. Warto patrzeć na nie w kierunku z góry na dół: ustawienia zapory w zakładce `Datacenter` dotyczą całego centrum.

==**UWAGA**==: poniżej poziomu węzła włącznie, ustawienia zapory sieciowej dotyczą tylko i wyłącznie jednej instancji obiektu. To znaczy, że ustawienie np. reguły przepuszczania ruchu sieciowego dla protokołu SSH na węźle `pve1` nie udostępni tej opcji niżej, do kontenerów i maszyn wirtualnych utworzonych wewnątrz tego węzła. Innymi słowy, ***węzły dziedziczą po*** `Datacenter`, ***ale VM i CT nie dziedziczą po węzłach!***

### **Dodawanie zasad wyjątków w zaporze sieciowej**
W starszych wersjach Proxmoxa, uruchomienie zapory sieciowej bez ustawienia wyjątków dla protokołu SSH i interfejsu sieciowego, może skutkować trwałym zablokowaniem zdalnego dostępu do serwera. By to odwrócić, należy zmodyfikować plik konfiguracyjny o adresie `/etc/pve/firewall/cluster.fw` przy pomocy dowolnego edytora tekstowego, oraz zmodyfikować jego wartość z:
```bash
[OPTIONS]

enable: 1
```
na następujący format:
```bash
[OPTIONS]

enable: 0
```
<br>
By jednak całkowicie zapobiec wystąpieniu takiej sytuacji, należy dodać wspomniane wcześniej ustawienia wyjątków zapory sieciowej. Za przykład posłuży `Datacenter`.

By dodać regułę, należy przejść do **Datacenter > Firewall** oraz kliknąć przycisk **Add**. By otworzyć ruch sieciowy w celu otwarcia portu interfejsu sieciowego, należy w następujący sposób ustawić regułę zapory:
![[Pasted image 20241222180821.png | center]]

W okienku `Source` można dodać adres IP urządzenia, które będzie mogło nawiązywać ruch sieciowy z serwerem, węzłem lub maszyną wirtualną, np. można przyznać uprawnienia do pingowania tylko jednemu urządzeniu w całej sieci, w celu zwiększenia bezpieczeństwa serwera. "Złamanie" adresu IP liczbą 32 jak poniżej powoduje, że cały zakres dostępnych adresów IP jest zawężany do jednego, konkretnego, podanego przez użytkownika adresu. Przykład takiej konfiguracji: 
![[Pasted image 20241222190755.png | center]]

Reguła akceptowania ruchu sieciowego na porcie 22 dla jednego urządzenia będzie wyglądać następująco:
![[Pasted image 20241222195648.png | center]]

==**UWAGA**==: nowsze wersje Proxmoxa nie wymagają dodawania do ustawień zapory całego szeregu reguł. Reguły dla portu 8006 i 22, zezwalające na swobodny ruch sieciowy w obrębie interfejsu sieciowego i protokołu SSH, są wbudowane w zaporę sieciową. 

---
## Źródła i odniesienia:
- https://www.youtube.com/watch?v=DNsLLrCgK0U&list=PLT98CRl2KxKHnlbYhtABg6cF50bYa8Ulo&index=13&t=1s