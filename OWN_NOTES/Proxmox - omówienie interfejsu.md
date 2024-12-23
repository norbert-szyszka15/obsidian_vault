---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---

---
Utworzono: 2024-11-25 18:26
Tagi: [[INFORMATYKA]], [[SERWER]]

---

### Zakładka `Summary`
Zakładka "Summary" pozwala podejrzeć istotne informacje na temat serwera, w tym zużycie pamięci RAM i ROM oraz procesora, wersję jądra Linuxa na którym działa obecnie zainstalowana wersja Proxmoxa oraz status utworzonych węzłów. Można tutaj również znaleźć informacje na temat ilości obecnie uruchomionych maszyn wirtualnych i kontenerów.

---
### Zakładka `Notes`
Zakładka w której można dodawać różnego rodzaju notatki, w tym takie które chcemy udostępnić komuś, kto także pracuje nad serwerem. Notatki można edytować w markdown w celu dodania bogatego formatowania tekstu.

---
### Zakładka `Shell`
W tej zakładce można używać CLI w celu bezpośredniej ingerencji w system operacyjny Proxmox.

---
### Zakładka `System`
##### Podzakładka `Network`
Tutaj można utworzyć dodatkowe bridge, skonfigurować nowe adaptery i wiele więcej. **DO UZUPEŁNIENIA**

##### Podzakładka `Certificates`
Tutaj znajdują się wszystkie certyfikaty przypisane do serwera. W większości przypadków bazowy `self-signed cert` jest w zupełności wystarczający. Przeglądarka pokazuje połączenie z Proxmoxem jako niezabezpieczone, ale jest to w porządku - dobrą praktyką jest nieotwieranie interfejsu zarządzania serwerem do sieci publicznej. Zamiast tego lepiej jest otwierać poszczególne wymagane maszyny wirtualne.

##### Podzakładka `DNS`
Zakładka w której skonfigurowany jest serwer DNS sieci. Jeżeli skonfigurowany jest domowy serwer DNS (tak jak w przypadku **PiHole** + **Unbound**), należy upewnić się, że adres IP serwera DNS w tej zakładce pokrywa się z adresem tego serwera w sieci domowej.

##### Podzakładka `Hosts`
Tutaj można modyfikować statyczne zmienne hostów, ponieważ mamy dostęp do plików hosta. Chociaż ta możliwość istnieje, zalecaną praktyką jest unikanie modyfikacji tych plików, ponieważ lepiej jest do tego używać serwera DNS, co powoduje, że zmodyfikowane zmienne są dostępne w całej sieci domowej, a nie tylko na komputerze hoście.

##### Podzakładka `Time`
Służy tylko i wyłącznie do zmiany strefy czasowej i serwera czasu. Po instalacji i raz na jakiś czas warto rzucić tutaj okiem, żeby upewnić się, że wszystko się zgadza. Niepoprawnie ustawiona strefa czasowa może powodować dalsze problemy z systemem operacyjnym.

##### Podzakładka `Syslog`
Tutaj wyświetlają się logi systemowe, a w szczególności komunikaty o błędach, które można potem bez przeszkód googlować w celu znalezienia rozwiązania problemu.

---
### Zakładka `Updates`
W tej zakładce będą się wyświetlać wszystkie aktualizacje dla serwera. Właściwie wykonywane są tutaj klasyczne dla Debiana polecenia `apt-get update` oraz `apt-get upgrade`. Proxmox może wyrzucić komunikat błędu przez brak certyfikatu, i chociaż wygląda to tak, jakby serwer nie wykonywał aktualizacji, w rzeczywistości przebiega ona w normalny, nominalny sposób. W ostateczności aktualizację można też przeprowadzić używając emulatora terminala. Ewentualnie prompty wyświetlanie po drodze można akceptować z domyślnymi ustawieniami. Po aktualizacji serwera dobrą praktyką jest reboot maszyny.

---
##### Podzakładka `Repositories`
W tej sekcji wyświetlane są wszystkie repozytoria dodane do systemu operacyjnego, oraz ewentualne opcje do dodawania kolejnych.

---
### Zakładka `Firewall`
Tutaj można zarządzać zezwalaniem ruchu sieciowego w obrębie serwera.

---
### Zakładka `Disks`
Wyświetla informacje na temat dysków zainstalowanych w komputerze serwerowym. Tutaj można znaleźć także opcje do zarządzania fizycznymi nośnikami pamięci, głównie do ich usuwania. Partycjami, wolumenami i innymi tego typu rzeczami najlepiej jest jednak zarządzać przy pomocy terminala i CLI.
##### Podzakładka `LVM-Thin`
Tutaj wyświetlane jest miejsce faktycznie dostępne dla maszyn wirtualnych i kontenerów, dodane do Thin Pool. Tutaj faktycznie można sprawdzić jak wiele miejsca można zaalokować do dysków wirtualnych. 

---
### Zakładka `Datacenter`
Ta zakładka zawiera swój własny szereg opcji i menu, które dotyczą klastra złożonego z dwóch lub więcej fizycznych maszyn działających pod systemem operacyjnym Proxmox.


---
## Źródła i odniesienia:
- https://www.youtube.com/watch?v=1PGRpx7xgmo&list=PLT98CRl2KxKHnlbYhtABg6cF50bYa8Ulo&index=4