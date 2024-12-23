---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---

---
Utworzono: 2024-11-26 07:59
Tagi: [[INFORMATYKA]], [[SERWER]]

---

### Przedstawienie problemu
W komputerze znajdują się dwa dyski twarde, jeden NVME, drugi SSD. Na obu zainstalowany jest system operacyjny (lub inna forma plikowa uniemożliwiająca wyzerowanie dysku poprzez GUI Proxmoxa lub innego systemu operacyjnego opartego na Linuxie). Przykładowy komunikat błędu wyrzucany przez Proxmox:
![[Pasted image 20241126080603.png | center]]

### Odpowiedzi
Ten komunikat w Proxmoxie wskazuje, że partycja `/dev/sda3` jest obecnie używania przez system, dlatego nie może zostać wyzerowana ani sformatowana. W celu rozwiązania tego problemu, należy wykonać poniższe kroki:

##### 1. Sprawdzenie, co używa partycji
Uruchom terminal Proxmoxa (można to zrobić z interfejsu sieciowego lub poprzez narzędzie SSH) i wykonaj następujące polecenie:
```bash
lsblk -o NAME,MOUNTPOINTS,PARTTYPE,TYPE

```

Sprawdź, czy partycja `/dev/sda3` jest zamontowana lub używana jako część LVM.

***LVM (Logical Volume Manager)*** - z ang. zarządca woluminów logicznych, mechanizm systemu operacyjnego do zarządzania przestrzenią pamięci masowej. W szczególności, zarządca pozwala na połączenie partycji znajdujących się na różnych urządzeniach pamięci masowej w jeden [[Dysk wirtualny]]. Jego rozmiar nie jest zdefiniowany na stałe - jeśli zachodzi taka potrzeba, istnieje możliwość jego rozszerzenia o nową przestrzeń pamięciową. Obsługa woluminów logicznych zaimplementowana jest w większości systemów operacyjnych, może się jednak różnić funkcjonalnością i stosowaną terminologią.

##### 2. Odmontowanie partycji (jeśli jest zamontowana)
Jeśli partycja jest zamontowana, możesz ją odmontować za pomocą komendy:
```bash
umount /dev/sda3
```

##### 3. Wyłączenie wolumenu LVM (jeśli dotyczy)
Jeśli partycja jest częścią LVM, należy wyłączyć wolumen logiczny. Najpierw wylistujmy wszystkie wolumeny logiczne za pomocą komendy:
```bash
lvdisplay
```

Jeśli znajdziesz wolumen korzystający z `/dev/sda3`, wyłącz go: 
```bash
lvchange -an /dev/vgname/lvname
```

Zastąp `vgname` i `lvname` odpowiednimi nazwami grupy i wolumenu logicznego.

##### 4. Dezaktywacja grupy wolumenów (jeśli dotyczy)
Jeśli `/dev/sda3` jest częścią grupy wolumenów, dezaktywuj ją:
```bash
vgchange -an vgname
```

##### 5. Usunięcie konfiguracji LVM
Jeśli dysk był używany jako LVM i chcesz go wyczyścić, usuń grupę wolumenów:
```bash
vgremove vgname
```

Następnie usuń dane fizycznego wolumenu za pomocą:
```bash
pvremove /dev/sda3

```

##### 6. Zerowanie dysku SSD
Po upewnieniu się, że partycja nie jest używana, możesz wyczyścić dysk SSD. Aby to zrobić, możesz zainicjalizować w GUI Proxmoxa lub skorzystać z terminala:
```bash
wipefs -a /dev/sda
```

Ewentualnie możesz nadpisać cały dysk zerami (opcjonalnie, zajmuje więcej czasu):
```bash
dd if=/dev/zero of=/dev/sda bs=1M status=progress
```

##### 7. [[Proxmox - dodanie dysku jako pamięci]]
Po wyczyszczeniu dysku możesz go dodać jako pamięć w Proxmox GUI: **Datacenter > Storage > Add > Directory**



---
## Źródła i odniesienia:
- rozmowa z ChatGPT pt. *Zerowanie dysku SSD Proxmox*, 25 listopada 2024,
- https://pl.wikipedia.org/wiki/LVM