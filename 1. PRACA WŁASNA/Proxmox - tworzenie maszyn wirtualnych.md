---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---

---
Utworzono: 2024-11-26 19:47
Tagi: [[INFORMATYKA]], [[SERWER]]

---

### **Dodawanie obrazów ISO do serwera** 
Właściwie istnieją dwie opcje dodawania obrazów ISO do serwera: 
1. Pobranie pliku ISO na komputer z przeglądarką, przez którą łączymy się do interfejsu sieciowego Proxmoxa, i upload takiego obrazu do pamięci serwera,
2. Wklejenie linku do pobrania obrazu bezpośrednio do Proxmoxa, który automatycznie ściągnie z sieci wymagany plik i umieści go w odpowiednim katalogu.

Obrazy ISO należy uploadować do **Datacenter > local (pve1) > ISO Images**.

### **Tworzenie maszyny wirtualnej**
##### Zakładka `General`
W tej zakładce dostępne są opcje wyboru węzła, nazwy serwera, puli przypisania zasobów i ID maszyny wirtualnej. Dobrą praktyką jest wyrobienie sobie schematu przydzielania numerów ID, np. numery 100-199 zarezerwowane dla maszyn wirtualnych, 200-299 dla kontenerów, 300-399 dla schematów maszyn wirtualnych i 400-499 dla schematów kontenerów. Proxmox daje użytkownikowi pełną dowolność w wyborze ID tak długo, jak nie powtarzają się one między maszynami. 
##### Zakłada `OS`
W tej zakładce wybierany jest system operacyjny używany przez maszynę. Szczególną uwagę należy zwrócić na to, by opcje wyświetlane pod `Guest OS` były zgodne z obrazem systemu, jaki wybieramy do jego instalacji.
##### Zakładka `System`
W tej zakładce mamy do wyboru kilka rzeczy związanych z konfiguracją systemu, ale można je zostawić na ustawieniach domyślnych dla większości maszyn. Szczegóły pojawią się w innych notatkach, jeżeli będą wymagane.
##### Zakładka `Disks`
Po pierwsze, bardzo dobrą praktyką jest zaznaczenie opcji `Discard` w przypadku używania Proxmoxa na dysku/dyskach SSD, co pozwala na efektywniejsze zarządzanie pamięcią ROM maszyny wirtualnej i jej czyszczenie. Oprócz tego można tutaj wybrać pamięć, którą przydzielimy do maszyny wirtualnej i wielkość dysku wirtualnego.
##### Zakładka `CPU`
Tutaj można wybrać ilość rdzeni procesora przydzielonych do maszyny wirtualnej. Im więcej rdzeni, tym więcej potencjalny serwer będzie w stanie wykonać. Większość maszyn powinna używać jednego rdzenia procesora, a jeżeli w panelu zarządzania któraś z nich będzie wykazywała oznaki posiadania zbyt małej ilości zasobów, dopiero wtedy należy dodawać kolejne rdzenie. Pozwoli to odpowiednio zadbać o fizyczny procesor w maszynie serwera.
##### Zakładka `Memory`
Minimalna ilość rekomendowana dla maszyny wirtualnej wynosi 1 GB. Jak w poprzednim przypadku, należy aktywnie monitorować zużycie pamięci przez VM. Często proces instalacji systemu operacyjnego wymaga większej ilości pamięci RAM niż postawiony system, więc jeżeli podczas prób instalacji będą pojawiać się crashe, w większości przypadków będą wynikiem za małej ilości pamięci operacyjnej przypisanej do maszyny.
##### Zakładka `Network`
Dobrą praktyką jest odseparowanie sieci maszyn wirtualnych od sieci zarządzania, co zostanie poruszone w późniejszych notatkach. 
##### Zakładka `Confirm`
To miejsce stanowi podsumowanie wszystkich wybranych opcji. Maszynę wirtualną można uruchomić od razu po stworzeniu, lub jedynie ją utworzyć.

### **Opcje maszyny wirtualnej, na które należy zwrócić szczególną uwagę**
1. `Start at boot` - wartość domyślnie ustawiona na `No`, jednak dobrze jest gdy większość maszyn uruchamia się samoistnie wraz ze startem maszyny serwerowej,
2. `Start/Shutdown order` - można określić kolejność, w jakiej uruchamiane są maszyny wirtualne, poprzez dodawanie numeru priorytetu do każdej z nich. Przykładowo, jeżeli jakaś maszyna wirtualna hostuje bazę danych, na której polegają usługi w innych maszynach, VM z bazą danych ***musi być zawsze uruchamiany przed pozostałymi***.
3. `QEMU Guest Agent` - opcję tę należy uruchomić po instalacji QEMU Guest Agent i uruchomieniu tego serwisu w systemie operacyjnym maszyny wirtualnej.

 Za każdym razem, gdy jakaś opcja zmienia kolor na czerwony/pomarańczowy, to znaczy że wprowadzone zmiany wejdą w życie dopiero po reboocie maszyny wirtualnej.
 
### **Dobre praktyki po stworzeniu maszyny wirtualnej**
1. Aktualizacja maszyny i dystrybucji, np. w przypadku Ubuntu Server:
   ```bash
   sudo apt update && sudo apt dist-upgrade
   ```

2. Instalacja QEMU Guest Agent, różniąca się dla każdej dystrybucji Linuxa, w przypadku Ubuntu Server:
   ```bash
   sudo apt install qemu-guest-agent
	```

3. Sprawdzenie statusu serwisu QEMU Guest Agent:
   ```bash
   systemctl status qemu-guest-agent.service
	```

	Bazowo usługa jest wyłączona, można ją uruchomić za pomocą
	```bash
   sudo systemctl start qemu-guest-agent.service
	```


---
## Źródła i odniesienia:
- https://www.youtube.com/watch?v=xBUnV2rQ7do&list=PLT98CRl2KxKHnlbYhtABg6cF50bYa8Ulo&index=7