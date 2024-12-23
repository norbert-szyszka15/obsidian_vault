---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---

---
Utworzono: 2024-11-26 10:55
Tagi: [[INFORMATYKA]], [[SERWER]]

---

Polecenie `resize2fs` służy **rozszerzaniu lub zmniejszaniu rozmiaru systemów plików** w przypadku, gdy system plików jest oparty na ext2, ext3 lub ext4. W kontekście Proxmox i ogólnych operacji na dyskach, `resize2fs` możesz użyć w sytuacjach, gdy chcesz **zmienić rozmiar istniejącego systemu plików**, ale tylko wtedy, gdy spełnione są odpowiednie warunki.

### Kiedy można użyć `resize2fs`?

1. **Rozszerzanie systemu plików po powiększeniu partycji lub wolumenu logicznego:**
    - Jeśli powiększyłeś rozmiar partycji (np. w GParted) lub wolumenu logicznego LVM, musisz odpowiednio rozszerzyć system plików, np:
```bash
lvextend -L +10G /dev/pve/root
resize2fs /dev/pve/root
```
        
Tutaj system plików `ext4` na wolumenie `/dev/pve/root` zostanie dostosowany do nowego rozmiaru wolumenu logicznego.

2. **Zmniejszanie systemu plików (z ostrożnością!):**
    - Jeśli chcesz zmniejszyć rozmiar partycji, musisz najpierw zmniejszyć system plików.
```bash
resize2fs /dev/sda1 20G
```
        
Ten przykład zmniejszy system plików do 20 GB. Następnie możesz zmniejszyć rozmiar partycji za pomocą narzędzi takich jak `fdisk` lub `parted`.

3. **Tworzenie większej przestrzeni w istniejących strukturach dyskowych:**
    - Na przykład, jeśli w Proxmox masz magazyn przechowujący dane jako zwykły system plików (np. `/var/lib/vz`), a dysk ten został rozszerzony, `resize2fs` pozwoli wykorzystać nową przestrzeń.
    
4. **Naprawianie problemów z systemem plików:**
    - Jeśli system plików jest niespójny, a rozmiary partycji i systemu plików się różnią, `resize2fs` może być użyte do synchronizacji tych rozmiarów.

### Kiedy NIE używać `resize2fs`?

1. **Na Thin Pool LVM:**
    Thin Pool (`/dev/pve/data`) w LVM nie jest systemem plików, więc `resize2fs` nie działa w tym przypadku. Thin Pool zarządza przestrzenią na poziomie bloków, a nie systemów plików.
    
2. **Na ZFS:**
   W przypadku ZFS nie korzysta się z `resize2fs`, ponieważ ZFS zarządza przestrzenią automatycznie.
    
3. **Na partycjach bez systemu plików:**
   Jeśli partycja lub wolumen logiczny nie jest sformatowany systemem plików (np. ext4), `resize2fs` nie będzie działać.
    
### Jak sprawdzić, czy mogę użyć `resize2fs`?
1. Zweryfikuj, czy partycja lub wolumen logiczny ma system plików obsługiwany przez `resize2fs`:
    ```bash
blkid
    ```
    
Wynik powinien pokazać typ systemu plików, np.:
```bash
/dev/sda1: UUID="..." TYPE="ext4"
```
    
2. Upewnij się, że wolumen lub partycja nie jest używana jako Thin Pool lub ZFS.
    
### Przykład użycia w praktyce:

Jeśli powiększyłeś rozmiar wolumenu `/dev/pve/root`, który zawiera system plików ext4:
1. Powiększ wolumen:
```bash
lvextend -L +10G /dev/pve/root
```
    
2. Rozszerz system plików:
```bash
resize2fs /dev/pve/root
```
    
System plików dostosuje się do nowego rozmiaru wolumenu.
<br>

Podsumowując, **`resize2fs` jest narzędziem do zarządzania systemami plików ext2/ext3/ext4**. W kontekście Proxmox zazwyczaj używasz go na standardowych systemach plików, np. przy powiększaniu lub zmniejszaniu partycji zawierających dane lub obrazy VM/CT. W przypadku Thin Pool lub ZFS, należy używać odpowiednich narzędzi dla tych technologii.

---
## Źródła i odniesienia:
- rozmowa z ChatGPT pt. *Zerowanie dysku SSD Proxmox*, 25 listopada 2024