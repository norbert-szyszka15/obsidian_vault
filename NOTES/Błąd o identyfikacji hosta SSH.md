---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---

---
Utworzono: 2024-12-01 13:31
Tagi: [[INFORMATYKA]], [[SERWER]]

---

### **Błąd o identyfikacji zdalnego hosta w SSH**
Czasami po sklonowaniu maszyny wirtualnej lub kontenera w Proxmoxie czy przebudowaniu systemu operacyjnego na komputerze, z którym łączymy się zdalnie przez SSH, podczas prób połączenia może pojawić się komunikat o treści:
![[Pasted image 20241201133340.png | center]]

W opisanych powyżej okolicznościach to całkowicie normalna rzecz, która bez powodu jest utrzymana w bardzo alarmującym tonie. Po jego otrzymaniu istnieją dwie ścieżki:

1. Jeżeli wystąpił on na ***adresie sieci lokalnej***, wtedy możemy go bezpiecznie zignorować. By wyczyścić pulę kluczy SSH przypisaną do adresu IP hosta możemy uruchomić komendę:
   ```bash
   ssh-keygen -R <ip_address>
   ```
	Dla przykładu:
   ```bash
   ssh-keygen -R 192.168.0.113
   ```

2. Jeżeli wystąpił on na adresie ***zdalnym sieci publicznej***, należy sprawdzić, czy nie doszło na niej do żadnego włamania.

---
## Źródła i odniesienia:
- https://forums.raspberrypi.com/viewtopic.php?t=362325