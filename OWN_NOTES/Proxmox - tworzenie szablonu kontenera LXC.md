---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---
---
Utworzono: 2024-12-01 12:13
Tagi: [[INFORMATYKA]], [[SERWER]]

---

### **Przygotowywanie kontenera do konwersji**
Przed przekonwertowaniem kontenera w szablon należy, tak jak w przypadku konwersji maszyny wirtualnej, przygotować różne pliki i usługi wewnątrz kontenera i go "wyczyścić". 

1. Na samym początku warto zaktualizować aplikacje i pakiety wewnątrz kontenera przy pomocy:
   ```bash
   sudo apt update && sudo apt dist-upgrade
   ```

2. Kolejną prostą komendą, która wyczyści bazę danych pakietów `apt`, jest komenda:
   ```bash
   sudo apt clean
   ```
   Komenda ta nie zrobi gigantycznej różnicy, ale w celu przywrócenia niewielkiej ilości pamięci i przeczyszczenia bazy danych, warto jej użyć.
<br>
3. Komenda, która usuwa pakiety-sieroty, czyli pakiety wymagane dla działania innych pakietów lub aplikacji, a której nie są dłużej potrzebne, to:
   ```bash
   sudo apt autoremove
   ```

4. Znów podobnie jak w przypadku VM, kolejnym krokiem będzie wyczyszczenie kluczy hosta SSH. Znajdują się one w katalogu `/etc/ssh`, a usunąć je można przy pomocy poniższej komendy pod warunkiem, że znajdujemy się w tym katalogu:
   ```bash
   sudo rm ssh_host_*
   ```

5. Ponownie identycznej jak z maszynami wirtualnymi, kolejny krok to usunięcie ID maszyny z kontenera. Wykonuje się to używając komendy:
   ```bash
   sudo truncate -s 0 /etc/machine-id
   ```
	A sam klucz podejrzeć można w katalogu `etc`:
   ```bash
   cat /etc/machine-id
   ```

   ```bash
   sudo ssh-keygen -A && sudo systemctl restart ssh.service
   ```

### **Tworzenie szablonu kontenera**
Tworzenie szablonu kontenera wygląda identycznie jak w przypadku maszyn wirtualnych, należy kliknąć prawym przyciskiem na uprzednio przygotowany i wyłączony przy pomocy
```bash
sudo poweroff
```
kontener, a następnie kliknąć opcję `Convert to Tempalte`.

### **Budowanie kontenera na podstawie szablonu**
Po sklonowaniu szablonu, na nowo powstałym kontenerze należy z poziomu interfejsu sieciowego konsoli kontenera wygenerować na nowo klucze hosta SSH:
1. Najpierw usuwamy klucze istniejące wewnątrz kontenera (**o ile istnieją**), tak jak w przypadku przygotowywania szablonu, z katalogu `/etc/ssh`:
   ```bash
   sudo rm ssh_host_*
   ```

2. Potem odbudowujemy klucze SSH przy pomocy komendy:
   ```bash
   sudo dpkg-reconfigure openssh-server
   ```

Czasami, po odbudowie kluczy SSH hosta, przy łączeniu się z kontenerem lub maszyną wirtualną może wystąpić [[Błąd o identyfikacji hosta SSH]].

---
## Źródła i odniesienia:
- https://www.youtube.com/watch?v=J29onrRqE_I&list=PLT98CRl2KxKHnlbYhtABg6cF50bYa8Ulo&index=12
