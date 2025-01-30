---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---

---
Utworzono: 2024-11-30 20:53
Tagi: [[INFORMATYKA]], [[SERWER]]

---

### **Pobieranie szablonów kontenerów**
Przed utworzeniem kontenera należy pobrać jego szablon. W zakładce `local (pve1)` znajduje się opcja `CT Templates`. U góry strony znajduje się przycisk `Templates`, po kliknięciu którego można wybrać szereg różnego rodzaju kontenerów.
![[Pasted image 20241130210840.png | center]]

### **Tworzenie kontenerów**
Kontener można utworzyć klikając przycisk `Create CT` (gdzie `CT` to skrót od `Container`), znajdujący się w prawym górnym rogu ekranu obok przycisku do tworzenia VM. Okienko do tworzenia kontenerów posiada kilka zakładek, a są nimi: 
1. Zakładka `General` - tutaj można wybrać węzeł z klastra, na którym ma być utworzony kontener, ID kontenera, nazwę hosta (faktycznego kontenera). Należy także utworzyć hasło dostępu dla tej konkretnej instancji, oraz można dodać utworzony klucz (lub klucze) publiczny SSH,
2. Zakładka `Template` - wybieramy szablon dla kontenera,
3. Zakładka `Root Disk` - w tym miejscu przypisujemy ilość pamięci ROM do kontenera,
4. Zakładka `CPU` - tutaj przydzielamy ilość rdzeni procesora do kontenera. 
5. Zakładka `Memory` - dodawanie pamięci RAM do kontenera. Pamięć Swap powinna mieć w tym przypadku co najmniej tyle samo, co pamięć RAM,
6. Zakładka `Network` - konfigurowanie wielu opcji sieciowych, m.in. korzystania z DHCP lub przypisywania statycznego adresu IP, ustawienie limitu na prędkości przepływu danych itp.,
7. Zakładka `DNS` - zmiana ustawień serwera DNS.

Wyjściowa nazwa użytkownika w przypadku kontenerów to `root`. Proces instalacji kontenera nie wymaga podania nazwy użytkownika. W związku z tym komendy nie wymagają dodatku `sudo`.

Po utworzeniu kontenera dobrą praktyką jest jego aktualizacja przy pomocy 
```bash
apt update && apt dist-upgrade
```

Aby móc łączyć się z kontenerem za pomocą SSH, najpierw w interfejsie sieciowym kontenera w Proxmoxie należy dodać użytkownika:
```bash
adduser user_name
```

Aby użytkownik mógł wykonywać wszystkie polecenia na kontenerze, należy dodać go do grupy użytkowników `sudo`, czyli grupy tzw. superużytkowników. Wykonujemy to za pomocą:
```bash
usermod -aG sudo user_name
```

### **Opcje utworzonych kontenerów**
1. Podobnie jak w przypadku maszyn wirtualnych, jedną z opcji jest `Start at boot`, co ponownie może się okazać bardzo przydatne w przypadku niektórych usług,
2. Znów podobnie jak z VM, można edytować kolejność uruchomienia i wyłączania kontenerów,
3. `Unprivileged container` - opcja zmieniająca zakres uprawnień kontenera. Kiedy wybrana na `Yes`, kontener nie ma dostępu do żadnych elementów komputera-serwera (`root`). Może wywoływać konflikty i problemy z niektórymi aplikacjami.

---
## Źródła i odniesienia:
- https://www.youtube.com/watch?v=aGfPjdUM2cM&list=PLT98CRl2KxKHnlbYhtABg6cF50bYa8Ulo&index=9