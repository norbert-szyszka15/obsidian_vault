---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---
---
Utworzono: 2024-12-22 20:34
Tagi: [[INFORMATYKA]], [[SERWER]]

---
### **Komendy dotyczące maszyn wirtualnych**
Lista komend związanych z wyświetlaniem maszyn wirtualnych i zarządzaniem ich stanów uruchomieniowych:
1. `qm list` - komenda listująca wszystkie maszyny wirtualne w konkretnym węźle serwerowym (wraz z szablonami),
2. `qm start <vm_id>` - uruchamia maszynę wirtualną o podanym ID, przykład uruchomienia maszyny wirtualnej o ID 101
	```bash
	qm start 101
	```
3. `qm shutdown <vm_id>` - zawiesza działanie maszyny wirtualnej o wskazanym ID, a następnie ją wyłącza,
4. `qm reboot <vm_id>` - jak wyżej, uruchamia ponownie maszynę wirtualną o podanym przez użytkownika ID,
5. `qm reset <vm_id>` - ==**POTENCJALNE DESTRUKTYWNA**==, w przeciwieństwie do `reboot`, ta komenda wywołuje nagle zatrzymanie maszyny wirtualnej, spokojnego zabijania procesów przed jej wyłączeniem. Ekwiwalent wyłączenia i restartu poprzez przytrzymanie przycisku zasilania,
6. `qm stop <vm_id>` - ==**POTENCJALNE DESTRUKTYWNA**==, w odróżnieniu od `reset` tylko nagle wyłącza maszynę wirtualną.
<br>

Lista komend związanych z konfiguracją opcji maszyn wirtualnych z poziomu CLI (ekwiwalent i rozszerzenie konfiguracji graficznej w zakładce **Datacenter > pve1 > "vm_id" > Options**):
1. `qm config <vm_id>` - wyświetlenie wszystkich opcji konfiguracyjnych, jakie można zobaczyć i zmodyfikować. Przydatna w celu podejrzenia obecnej konfiguracji i znalezienia nazw opcji do użycia w CLI w komendzie `qm set`,
2. Zmiana parametru "Start at boot" z wartości `Yes` na wartość `No`
	```bash
	qm set --onboot 0 <vm_id>
	```
	Proces można analogicznie odwrócić komendą:
	```bash
	qm set --onboot 1 <vm_id>
	```
3. Zmiana ilości pamięci RAM przypisanej do maszyny wirtualnej na wartość 2048 MB:
	```bash
	qm set --memory 2048 <vm_id>
	```

### **Komendy dotyczące kontenerów**
Lista komend związanych z wyświetlaniem kontenerów i zarządzaniem ich stanów uruchomieniowych:
1. `pct list` - wylistowuje wszystkie kontenery utworzone w ramach węzła (wraz z szablonami),
2. `pct start <ct_id>` - uruchamia kontener o wskazanym numerze ID,
3. `pct shutdown <ct_id>` - łagodnie wyłącza procesy wewnątrz kontenera i sam kontener,
4. `pct reboot <ct_id>` - uruchamia ponownie kontener o wskazanym ID w sposób łagodny.
<br>

Lista komend związanych z konfiguracją opcji maszyn wirtualnych z poziomu CLI (ekwiwalent i rozszerzenie konfiguracji graficznej w zakładce **Datacenter > pve1 > "ct_id" > Options**) oraz opcjami dostępu:
1. `pct config <ct_id>` - wyświetla możliwe do zmodyfikowania opcje kontenera i ich statusy,
2. `pct enter <ct_id>` - płynnie przechodzi do powłoki SHELL kontenera o wskazanym numerze ID,
3. Zmiana ilości pamięci RAM przypisanej do kontenera na wartość 1024 MB:
	```bash
	pct set <ct_id> -memory 1024
	```

==**UWAGA**==: składnia komend `qm set` oraz `pct set` różni się miejscem zapisania ID obiektu, którego konfigurację modyfikujemy, oraz ilością myślników przy nazwach opcji (dla `qm set` jeden, dla `pct set` dwa).

---
## Źródła i odniesienia:
- https://www.youtube.com/watch?v=g77bEThFjwQ&list=PLT98CRl2KxKHnlbYhtABg6cF50bYa8Ulo&index=14