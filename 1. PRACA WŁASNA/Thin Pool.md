---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---
---
Utworzono: 2024-11-26 10:16
Tagi: [[INFORMATYKA]], [[SERWER]]

---

***Thin Pool*** - źródło pamięci masowej, które zapewnia alokację przestrzeni na żądanie. Mniej więcej przypomina pamięć wirtualną, która każdemu procesowi udostępnia pełną przestrzeń adresową. 

### Fat Provisioning
Tradycyjna metoda alokacji pamięci nazywana jest "grubą" alokacją pamięci. 
Przykład: użytkownik stwierdza, że skorzysta z 10 GB przestrzeni dyskowej. Fat Provisioning rezerwuje dla tego użytkownika fizyczną przestrzeń dyskową o pojemności 10 GB nawet pomimo faktu, że użytkownik w danym momencie wykorzystuje tylko 1% pamięci, o którą poprosił. Pozostałe 99% pozostaje niewykorzystane i żaden inny użytkownik ani proces nie może z nich skorzystać.

### Thin Provisioning
Zapewnia mechanizm alokacji pamięci na żądanie. Pozwala użytkownikowi zażądać większej przestrzeni dyskowej, niż została dla niego fizycznie zarezerwowana. Innymi słowy, umożliwia nadmierną alokację przestrzeni dyskowej.

### Thin Pool
Thin Pool to termin koncepcyjny oznaczający zapasowe źródło pamięci używane w ramach thin provisioning. Thin provisioning alokuje wirtualne fragmenty pamięci z thin pool, podczas gdy fat provisioning alokuje fizyczne bloki pamięci z tradycyjnego źródła pamięci, np. dysku.



---
## Źródła i odniesienia:
- https://pve.proxmox.com/wiki/Storage:_LVM_Thin
- https://stackoverflow.com/questions/49531525/what-does-thin-pool-in-docker-mean
