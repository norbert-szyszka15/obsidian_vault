---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---

---
Utworzono: 2024-11-30 14:00
Tagi: [[INFORMATYKA]], [[SERWER]]

---

### **Wprowadzenie**
Czasami przy tworzeniu kolejnych maszyn wirtualnych pojawiają się problemy np. w rzeczach, które każdy zainstalowany system operacyjny ma wyjściowo takie same, czego przykładami są klucze hosta SSH. Klucze hosta SSH można podejrzeć w terminalu za pomocą komendy:
```bash
ls -l /etc/ssh
```

Aby rozwiązać ten problem, należy użyć pakietu zwanego jako CloudInit. Aby sprawdzić, czy pakiet jest zainstalowany, w Ubuntu Server można użyć komendy:
```bash
apt search cloud-init
```

### **CloudInit**
CloudInit to narzędzie pozwalające zautomatyzować proces budowania kolejnych maszyn wirtualnych na serwerach. Właściwie rzecz ujmując jest to narzędzie, które automatycznie uruchamia różne komendy po postawieniu nowego systemu operacyjnego. Komendy te dostosowują maszyny wirtualne do określonych przez administratora standardów.

### **Przygotowanie szablonu maszyny wirtualnej**
1. Pliki, które należy wyczyścić na samym początku, to klucze hosta SSH. By to zrobić, należy przejść do ich katalogu za pomocą:
    ```bash
    cd /etc/ssh
    ```

	Powstałe przy instalacji systemu klucze SSH trzeba usunąć:
	```bash
	sudo rm ssh_host_*
	```

2. Kolejną rzeczą, która musi być inna dla każdej maszyny wirtualnej, jest ID maszyny, które można podejrzeć przy pomocy:
    ```bash
    cat /etc/machine-id
	 ```
	 
	Wymazanie zawartości tego pliku najprościej jest wykonać następującą komendą:
	```bash
	sudo truncate -s 0 /etc/machine-id
	```

	W dalszej kolejności szukamy linku symbolicznego do pliku `machine-id`.
	```bash
	ls -l /var/lib/dbus/machine-id
	```

	Jeżeli:
	- output terminala będzie wyglądał następująco:
		```bash
		lrwxrwxrwx 1 root root 15 Nov 26 19:29 /var/lib/dbus/machine-id -> /etc/machine-id
		```

		to znaczy, że szukany plik jest faktycznie linkiem symbolicznym. Można to rozpoznać po strzałce wskazywania na inny plik oraz po literze `l` na początku wyniku terminala. 
		
	- output terminala wskaże, że dany plik nie jest linkiem symbolicznym, trzeba będzie takowy utworzyć. Czynność tę w tym przypadku wykonać można następującą komendą:
		```bash
		sudo ln -s /etc/machine-dh /var/lib/dbus/machine-id
		```

3. Kolejne komendy, których użycie przy czyszczeniu maszyny pod szablon będzie dobrą praktyką, to:
	```bash
	sudo apt clean 
	```
	```bash
	sudo apt autoremove
	```

4. Najważniejszą komendą do uruchomienia przed wyłączeniem maszyny i zmienieniem jej w szablon jest:
	```bash
	sudo cloud-init clean
	```
	To powie usłudze CloudInit że kolejny boot tej konkretnej maszyny ma być traktowany jako pierwsze jej uruchomienie w ogóle - co pozwoli wygenerować na nowo wymagane pliki, np. klucze hosta SSH.

==**UWAGA**== - proces tworzenia szablonu z istniejącej maszyny wirtualnej jest procesem destruktywnym. Należy się upewnić, że dana maszyna nie jest do niczego używana i czy na pewno chcemy utworzyć z niej szablon.

5. By stworzyć szablon po przeprowadzeniu wszystkich powyższych czynności, należy kliknąć prawym przyciskiem myszy na maszynę wirtualną i wybrać opcję `Convert to template`. 
6. Po utworzeniu szablonu kolejną dobrą praktyką jest wykonanie szeregu czynności dodatkowych, w tym:
   - należy usunąć "przywiązanie" szablonu maszyny wirtualnej do pliku ISO. By to zrobić, należy w zakładce `Hardware` edytować atrybut `CD/DVD Drive (ide2)`,
   - należy dodać dysk CloudInit. Nadal w zakładce `Hardware` wybieramy opcję `Add` i `CloudInit Drive`, które zapisujemy w `local-lvm`,
   - w nowo dodanej zakładce `Cloud-Init` można zmienić kilka rzeczy, w szczególności należy zwrócić uwagę na nazwę użytkownika, hasło, publiczny klucz SSH. 
	By zatwierdzić wszystkie te zmiany i zapisać je w szablonie, należy kliknąć przycisk `Regenerate Image` znajdujący się u góry ekranu. 

### **Tworzenie nowej maszyny wirtualnej na podstawie szablonu**
By to wykonać, należy kliknąć prawym przyciskiem myszy na szablon i wybrać opcję `Clone`. Właściwie rzecz ujmując, stawianie nowej VM na podstawie szablonu jest kopiowaniem 1:1 wszystkich atrybutów ustawionych w maszynie pierwotnej wirtualnej przed przekształceniem jej w szablon, a także tych zmienionych w jej ustawieniach po przekształceniu. Przykładowy schemat wypełnienia formularza klonowania:
![[Pasted image 20241130150417.png | center]]

##### Zmiana nazwy hosta maszyny wirtualnej
Po utworzeniu maszyny wirtualnej na podstawie szablonu dobrze jest edytować nazwę hosta w maszynie, by uniknąć pomyłek przy używaniu SSH. Plik, który należy edytować, ma ścieżkę `/etc/hostname`, a samą edycję można zrobić przy pomocy dowolnego edytora tekstu w terminalu, np. **Nano**.

Kolejnym plikiem, który należy edytować, jest ten o adresie `/etc/hosts`. Po zmianie obu plików należy użyć komendy
```bash
sudo reboot
```
do zrestartowania maszyny wirtualnej. 

### [[Proxmox - brak internetu w maszynie stworzonej na podstawie szablonu]]


---
## Źródła i odniesienia:
- https://www.youtube.com/watch?v=t3Yv4OOYcLs&list=PLT98CRl2KxKHnlbYhtABg6cF50bYa8Ulo&index=8
