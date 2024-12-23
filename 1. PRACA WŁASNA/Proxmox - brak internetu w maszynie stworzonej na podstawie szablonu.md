---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---

---
Utworzono: 2024-11-30 17:26
Tagi: [[INFORMATYKA]], [[SERWER]]

---

==**UWAGA**==: poniższe instrukcje można zastosować do systemów operacyjnych wykorzystujących **Netplan**. Systemy operacyjne Ubuntu 18.04 i nowsze są wyposażone w tę technologię.

Stan sieci w każdym momencie, w tym adresy IP przypisane przez serwer DHCP i adresy MAC urządzeń lub maszyn wirtualnych skonfigurowane wewnątrz nich, można sprawdzić przy pomocą komendy
```bash
ip a
```

### **Opis problemu**
Zgodnie ze wskazówkami z [[Proxmox - tworzenie szablonów maszyn wirtualnych]] utworzona została maszyna wirtualna na podstawie szablonu, która ma na pokładzie Ubuntu Server. Problemem jest fakt, że maszyna wirtualna nie ma dostępu do internetu.

##### Krok 1: utwórz plik `netplan`
`Netplan` to rodzaj pliku, który zawiera docelową konfigurację sieciową dla maszyny, na której się znajduje. utwórz go za pomocą:
```bash
sudo touch /etc/netplan/01-netcfg.yaml
```

Następnie edytuj go używając poniższej komendy (tą komendą można też utworzyć i otworzyć plik do edycji za jednym zamachem, co jest właściwością edytora **Nano**):
```bash
sudo nano /etc/netplan/01-netcfg.yaml
```

##### Krok 2: dodaj podstawową konfigurację
Przykładowa konfiguracja dla systemu korzystającego z DHCP:
```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    ens18:
      dhcp4: true
      match:
        macaddress: "BC:24:11:FD:07:4A"
      set-name: ens18
```

Aby sprawdzić, jaki adres MAC jest przypisany do interfejsu sieciowego maszyny wirtualnej, należy:
1. Wybrać maszynę wirtualną,
2. Przejść do zakładki **Hardware** i znaleźć interfejs sieciowy (np. **Network Device** lub **VirtIO NIC**),
3. Kliknąć tę pozycję, aby wyświetlić szczegóły, pośród których będzie znajdował się adres MAC przypisany do maszyny.

##### Krok 3: zaktualizuj konfigurację w systemie operacyjnym
Zastosowanie konfiguracji sprowadza się do wykonania komendy
```bash
sudo netplan apply
```
i zrestartowania maszyny wirtualnej przy pomocy
```bash
sudo reboot
```

==**UWAGA**==: zatwierdzenie konfiguracji `netplan` może powodować kilka ostrzeżeń o niepoprawnych prawach dostępu do plików, jednak powinny one automatycznie zostać utworzone z właściwymi prawami. By to zweryfikować, wylistuj wszystkie pliki w katalogu
```bash
ls -l /etc/netplan
```
Plik z poprawnie utworzonymi prawami dostępu będzie wyglądał mniej więcej tak:
```css
-rw-r--r-- 1 root root  150 Nov 30 10:00 01-netcfg.yaml
```
Jeżeli prawda dostępu będą wyświetlać się w inny sposób, wykonaj następujące kroki:
1. Zmień właściciela pliku na `root`:
    ```bash
    sudo chown root:root /etc/netplan/01-netcfg.yaml
	```
2. Ustaw odpowiednie uprawnienia:
    ```bash
    sudo chmod 644 /etc/netplan/01-netcfg.yaml
	```


Po wykonaniu tych kroków karta sieciowa maszyny wirtualnej powinna mieć przypisany swój właściwy adres MAC i zezwalać na dostęp do internetu.

---
## Źródła i odniesienia:
- rozmowa z ChatGPT pt. *Proxmox SSH CloudInit konfiguracja*, 30 listopada 2024