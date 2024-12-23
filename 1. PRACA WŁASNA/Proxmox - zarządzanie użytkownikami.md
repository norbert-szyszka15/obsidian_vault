---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---

---
Utworzono: 2024-12-02 18:53
Tagi: [[INFORMATYKA]], [[SERWER]]

---
### **Użytkownicy na serwerze Proxmox**
Proxmox, tak jak każda inna dystrybucja Linuxa, zezwala na tworzenie użytkowników. Bazowo, do serwera złożonego z jednego urządzenia, przypisany jest użytkownik `root`. Nowych użytkowników można tworzyć w zakładce **Datacenter > Permissions > Users**, znajdującym się u góry ekranu przyciskiem `Add`. Użytkownik `root` korzysta z bazowego uwierzytelniania zaimplementowanego w każdym systemie Linux, czyli PAM.

Dodanie użytkownika w interfejsie sieciowym nie dodaje go automatycznie do instancji serwera. Po dodaniu użytkownika w GUI, należy go także dodać w Shellu serwera, używając komendy:
```bash
adduser <user_name>
```
Po wpisaniu tej komendy system poprosi użytkownika o wprowadzenie i potwierdzenie hasła (obowiązkowe), oraz wypełnienie kilku danych opcjonalnych, jak pełne imię i nazwisko (opcjonalne). Aby zweryfikować, czy użytkownik faktycznie został utworzony, można użyć komendy:
```bash
cat /etc/passwd
```
Poprawnie utworzony użytkownik pojawi się na samym dole pliku `passwd` (w którym przechowywane są informacje na temat wszystkich użytkowników systemu operacyjnego) z ID 1000.

Po poprawnym dodaniu użytkownika w GUI i Shellu Proxmoxa, w interfejsie sieciowym serwera można zalogować się jako ten użytkownik.

### **Różnice między użytkownikami w realmach PAM i PVE**

| Opcja                                        | Użytkownik w realmie PAM                                                                        | Użytkownik w realmie PVE                                           |
| -------------------------------------------- | ----------------------------------------------------------------------------------------------- | ------------------------------------------------------------------ |
| **Logowanie się do serwera przy pomocy SSH** | ***Może*** logować się do konsoli serwera przez SSH                                             | ***Nie może*** logować się do konsoli serwera przez SSH            |
| **Logowanie do interfejsu sieciowego**       | ***TAK*** (jeśli dodany w GUI)                                                                  | ***TAK*** (musi być dodany w GUI)                                  |
| **Zarządzanie maszynami wirtualnymi**        | ***Może*** zarządzać maszynami wirtualnymi                                                      | ***Może*** zarządzać maszynami wirtualnymi                         |
| **Sposób dodawania użytkownika**             | ***Może (nie musi)*** być dodany w ***GUI***, ***musi*** być dodany w ***pliku*** `/etc/passwd` | ***Może i musi*** być dodany jedynie w ***interfejsie sieciowym*** |
| **Wpływ na system operacyjny Proxmox**       | ***Tak***                                                                                       | ***NIE***                                                          |
Podsumowując, użytkownik w realmie PAM jest użyteczny jako druga osoba administrująca serwer lub mająca do niego bardziej zaawansowany dostęp, podczas gdy użytkownik w realmie PVE działa bardziej na zasadzie dodatkowego pracownika, który może pomagać w tworzeniu maszyn wirtualnych i kontenerów oraz zarządzania nimi. 

### **Zarządzanie uprawnieniami użytkowników**
Najlepszą opcją dodawania uprawnień dla użytkowników jest wykorzystywanie grup. Nową grupę można dodać w interfejsie sieciowym w zakładce **Datacenter > Permissions > Groups**, a modyfikowanie uprawnień grupy użytkowników wykonuje się w zakładce **Datacenter > Permissions**. W okienku `Path` można wybrać szereg różnych uprawnień dla grupy, zaczynając od dostępu tylko do realmów, grup czy fizycznych urządzeń podpiętych do serwera, przechodząc przez udzielanie praw dostępu do maszyn wirtualnych, kontenerów lub całych węzłów, a kończąc na przyznaniu uprawnienia `/`, czyli `root`. ***Grupa użytkowników powinna mieć przyznanie minimum uprawnień koniecznych do wykonania swojego zadania***. Na końcu procesu przyznawania uprawnień należy nadać danej grupie rolę. Lista dostępnych ról i spis ich przywilejów są dostępne w zakładce **Datacenter > Permissions > Roles**.
![[Pasted image 20241202194929.png | center]]

==**UWAGA**==: należy pamiętać, by po dodaniu nowych grup przypisać do nich użytkowników w zakładce **Datacenter > Permissions > Users**!
![[Pasted image 20241202194039.png | center]]


---
## Źródła i odniesienia:
- https://www.youtube.com/watch?v=frnILOGmATs&list=PLT98CRl2KxKHnlbYhtABg6cF50bYa8Ulo&index=11