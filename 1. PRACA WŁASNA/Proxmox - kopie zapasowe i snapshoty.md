---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---

---
Utworzono: 2024-12-02 19:55
Tagi: [[INFORMATYKA]], [[SERWER]]

---
### **Kopia zapasowa kontra snapshot**
Kopia zapasowa (ang. backup) jest kopią dysku maszyny wirtualnej i jest od niej odseparowana, tzn. można przechowywać ją na innych, zewnętrznych nośnikach (często w przechowalni kopii zapasowych wykorzystuje się oddzielną instancję TrueNAS). Snapshot maszyny wirtualnej jest z kolei przechowywany na dysku tej maszyny. 

##### Snapshoty
Snapshot jest użyteczny np. w przypadku testowania nowej aplikacji w maszynie wirtualnej. Chociaż zawsze można odinstalować daną aplikację, utworzenie snapshota jest prostym sposobem na zagwarantowanie niezaśmiecania dysku wirtualnego dodatkowymi pakietami. Snapshot powinien mieć nazwę, która wyraźnie świadczy o jego stanie. Podczas jego tworzenia można zaznaczyć opcję `Include RAM`, która oprócz wykonania snapshota stanu dysku maszyny wirtualnej, zapamięta też stan jej pamięci operacyjnej, jednak w większości przypadków nie jest to konieczne.
![[Pasted image 20241202202908.png | center]]
Snapshot można przywrócić w tym samym oknie, w którym są tworzone, tj.  np. **Datacenter > pve1 > 101 (webserver-1) > Snapshots**.
##### Kopie zapasowe
Tworząc kopię zapasową, najpierw należy wybrać miejsce, w którym dane będą przechowywane. Chociaż można tutaj wybrać opcję `local`, bardziej polecaną opcją jest zapisywanie backupów poza serwerem z Proxmoxem, np. dzięki wykorzystaniu [[Proxmox - współużytkowana pamięć masowa (shared storage)]]. Kolejnym istotnym elementem jest wybór trybu kopii zapasowej, co wpływa na czas jej tworzenia. Tryby backupów to:
1. `Stop Mode` - maszyna wirtualna zostanie wyłączona, po czym zostanie utworzony backup. W tym przypadku będzie on najdokładniejszy, żaden plik nadpisywany w trakcie tworzenia kopii zapasowej nie zostanie źle w niej zapisany (ponieważ maszyna będzie wyłączona) i minimalizujemy ryzyko uszkodzenia danych. Jeśli maszyna nie była uruchomiona w momencie wykonana kopii, oczywiście nie zostanie ona wyłączona,
2. `Suspend Mode` - opcja niepolecana przed twórców i dokumentację Proxmoxa. Maszyna wirtualna będzie wyłączona przez najdłuższy okres spośród wszystkich trzech trybów backupu. Maszyna wirtualna przejdzie w tryb uśpienia, wtedy wykona się kopia zapasowa jej dysku, po czym zostanie ona wybudzona. VM nie będzie dostępny w trakcie tworzenia backupu. Ryzyko uszkodzenia danych także jest bardzo niewielkie,
3. `Snapshot Mode` - chociaż nazywa się podobnie, nie jest faktycznym snapshotem. Opcja ta oferuje najkrótszy przestój w funkcjonowaniu maszyny wirtualnej. Kopia zapasowa zostanie wykonana na żywo, w trakcie działania maszyny wirtualnej.

![[Pasted image 20241202205435.png | center]]

Proces przywracania kopii zapasowej odbywa się w tej samej zakładce, w której jest ona tworzona, tj. np. **Datacenter > pve1 > 101 (webserver-1) > Backup**. Maszyna wirtualna musi być wyłączona przed przywróceniem backupu. W szczególności można zwrócić uwagę, że maszynę wirtualną można odtworzyć na wolumenie pamięci innym od tego, na którym znajdowała się ona pierwotnie.

### **Automatyzacja backupów maszyn wirtualnych**
Proces backupu można zautomatyzować tworząc rozkład procesów wykonywania kopii zapasowych w zakładce **Datacenter > Backup**. Można tutaj wybrać węzeł, którego będzie dotyczył dany proces, czas kiedy będzie się wykonywał, miejsce zapisu i inne podobne opcje. Warto jest ustawić wysyłanie emaili ze statusem wykonania backupu. 
![[Pasted image 20241202210431.png | center]]

Całość tego procesu można zautomatyzować jeszcze bardziej przy wykorzystaniu [[Proxmox - serwer kopii zapasowych (backup server)]].

---
## Źródła i odniesienia:
- https://www.youtube.com/watch?v=NnKQnqoEm-I&list=PLT98CRl2KxKHnlbYhtABg6cF50bYa8Ulo&index=12