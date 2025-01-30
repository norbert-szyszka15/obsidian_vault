---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---

---
Utworzono: 2024-11-26 08:34
Tagi: [[INFORMATYKA]], [[SERWER]]

---

### Wybór opcji dodania dysku
- **Scenariusz 1: osobny magazyn** - jeśli SSD będzie przechowywać dane niezależenie od NVMe w zwykłym systemie plików ext4.
- **Scenariusz 2: LVM lub ZFS** - jeśli chcemy, połączyć przestrzenie dyskowe z NVMe i SSD w jeden duży, wspólny zasób (skupimy się na LVM).

### Scenariusz 1: osobny magazyn
##### Krok 1: przygotowanie dysku
Upewnij się, że dysk jest wyczyszczony i gotowy do użycia (np. za pomocą polecenia `wipefs` lub z GUI, jak opisano wcześniej).

##### Krok 2: utworzenie systemu plików na dysku
Musisz utworzyć system plików na dysku lub partycji, która będzie używana. Załóżmy, że zamierzasz użyć całego dysku SSD (`/dev/sda`).

1. Utworzenie systemu plików (w systemach Linux najczęściej ext4):
```bash
mkfs.ext4 /dev/sda
```

2. Utworzenie katalogu, w którym dysk zostanie zamontowany:
```bash
mkdir /mnt/ssd
```

3. Zamontowanie dysku w tym katalogu:
```bash
mount /dev/sda /mnt/ssd
```

4. Dodanie wpisu do pliku `/etc/fstab`, aby montowanie było automatycznie wykonywanie przy każdym uruchomieniu systemu operacyjnego:
```bash
echo "/dev/sda /mnt/ssd ext4 defaults 0 2" >> /etc/fstab
```

##### Krok 3: dodanie dysku jako pamięci w GUI Proxmoxa
1. W Proxmox wejdź w **Datacenter** (w lewym menu). 
2. Kliknij **Storage**, a następnie wybierz opcję **Add** (górny prawy róg). 
3. Wybierz typ magazynu: - Jeśli chcesz przechowywać obrazy VM/CT, wybierz **Directory** 
4. W formularzu konfiguracji:
	- **ID**: Podaj nazwę magazynu, np. `ssd-storage`.
	- **Directory**: Wskaż katalog, w którym zamontowałeś dysk, np. `/mnt/ssd`.
	- **Content**: Wybierz typy danych, które chcesz przechowywać (np. `Disk image`, `Container`, `ISO image` itp.).
	- Kliknij **Add**, aby zapisać.

##### Krok 4: testowanie
Po dodaniu magazynu możesz go zobaczyć w sekcji **Datacenter > Storage**. Możesz teraz używać go do przechowywania maszyn wirtualnych, kontenerów lub innych plików.

### (Opcjonalnie) Odwracanie powyżej wykonanych kroków
##### Krok 1: usuń magazyn z GUI Proxmoxa
1. Wejdź w **Datacenter** w lewym menu.
2. Przejdź do sekcji **Storage**.
3. Znajdź magazyn dodany na podstawie `/mnt/ssd` (np. `ssd-storage`).
4. Kliknij prawym przyciskiem myszy na magazyn i wybierz opcję **Remove**.
Te kroki usuną magazyn z konfiguracji Proxmoxa, lecz dane na dysku pozostaną nienaruszone.

##### Krok 2: odmontuj dysk
1. Sprawdź, czy dysk jest zamontowany za pomocą komendy:
```bash
mount | grep /mnt/ssd
```

2. Jeżeli dysk jest zamontowany, w kolejnym kroku odmontuj go:
```bash
umount /mnt/ssd
```

##### Krok 3: usuń katalog montowania
```bash
rmdir /mnt/ssd
```

##### Krok 4: usuń wpis z `/etc/fstab`
1. Edytuj plik `/etc/fstab`:
```bash
nano /etc/fstab
```

2. Znajdź linię dodaną wcześniej (np. `/dev/sda /mnt/ssd ext4 defaults 0 2`) i usuń ją.
3. Zapisz plik i zamknij edytor (w **nano**: `CTRL+O`, `ENTER`, a potem `CTRL+X`).

##### Krok 5: (opcjonalnie) wyzerowanie systemu plików na dysku
Jeśli chcesz wyczyścić system plików z dysku SSD, użyj następującego polecenia:
```bash
wipefs -a /dev/sda
```

To usunie wszystkie metadane systemu plików i sprawi, że dysk będzie jak "czysty".

##### Krok 6: sprawdzenie stanu dysku
Po wykonaniu powyższych kroków dysk powinien być gotowy do ponownego użycia, bez żadnych śladów wcześniejszej konfiguracji w Proxmox.

### Scenariusz 2: LVM lub ZFS
##### Krok 1: utwórz fizyczny wolumen na SSD
```bash
pvcreate /dev/sda
```

##### Krok 2: rozszerzanie grupy wolumenów (VG)
Dodaj wcześniej utworzony fizyczny wolumen do istniejącej grupy wolumenów (zakładając, że NVMe korzysta z LVM):
1. Znajdź nazwę grupy wolumenów na NVMe:
```bash
vgdisplay
```

Wynik w terminalu pokaże coś w poniższym stylu:
```php
VG Name               pve
VG Size               <rozmiar VG>
```

Tutaj `pve` to nazwa grupy wolumenów (VG), którą będziesz rozszerzać.

2. Dodaj utworzony wolumen do istniejącej grupy wolumenów:
```bash
vgextend <nazwa-grupy-wolumenów> /dev/sda
```

Aby dodać cały dysk SSD (w tym przypadku `/dev/sda`) do grupy VG, można użyć poniższego polecenia w CLI:
```bash
vgextend pve /dev/sda
```

##### Krok 3: rozszerzanie wolumenu logicznego (LV)
Teraz przestrzeń z SSD zostanie dołączona do przestrzeni NVMe:
1. Sprawdź dostępne wolumeny logiczne i ich ścieżki za pomocą polecenia:
```bash
lvdisplay
```

Wyniki pokażą coś w stylu:
```bash
LV Name               root
VG Name               pve
LV Path               /dev/pve/root
LV Size               <rozmiar LV>
```

Tutaj `LV Path` (np. `/dev/pve/root` lub `/dev/pve/data`) to ścieżka logicznego wolumenu, który będziesz rozszerzać.

2. Rozszerz istniejący wolumen logiczny za pomocą polecenia:
```bash
lvextend -L +<rozmiar> <ścieżka-wolumenu>
```

Załóżmy, że chcesz rozszerzyć `root` (chociaż równie dobrze może to być wolumen `data`) tak, aby wykorzystać całą dostępną przestrzeń z SSD. Do tego celu należy użyć polecenia:
```bash
lvextend -l +100%FREE /dev/pve/root
```

gdzie:
- `-l +100%FREE` oznacza, że użyjesz całej dostępnej przestrzeni z grupy VG.
- `/dev/pve/root` to ścieżka logicznego wolumenu.

##### Krok 4: zweryfikuj, że wolumen został powiększony:
```bash
lvs
```

W terminalu wynikowym pojawi się odpowiedz w tym stylu:
```kotlin
LV   VG   Attr       LSize    Pool Origin Data%  Meta%  
data pve  twi-aotz-- <rozmiar>                ...
```

`Attr` o wartości `twi-aotz--` wskazuje, że `data` to [[Thin Pool]]. Thin Pool to specjalny typ wolumenu LVM używany w Proxmox do dynamicznego zarządzania przestrzenią dyskową dla maszyn wirtualnych i kontenerów.

### (Opcjonalnie) Jeżeli nie chcesz użyć Thin Pool
Jeśli nie chcesz używać Thin Pool (np. chcesz sformatować go jako ext4 i używać jako zwykły system plików), możesz usunąć Thin Pool i utworzyć nowy system plików.

==**UWAGA**==: usuń Thin Pool tylko, jeśli wiesz, że nie ma na nim żadnych danych! Sprawdź, czy Thin Pool nie jest używany przez maszyny wirtualne lub kontenery.

1. Usuń Thin Pool:
```bash
lvremove /dev/pve/data
```

2. Utwórz nowy system plików:
```bash
mkfs.ext4 /dev/pve/data
```

3. Zamontuj go w systemie:
```bash
mkdir /mnt/data
mount /dev/pve/data /mnt/data
```

4. Dodaj odpowiedni wpis do `/etc/fstab`:
```bash
echo "/dev/pve/data /mnt/data ext4 defaults 0 2" >> /etc/fstab
```

---
## Źródła i odniesienia:
- rozmowa z ChatGPT pt. *Zerowanie dysku SSD Proxmox*, 25 listopada 2024,