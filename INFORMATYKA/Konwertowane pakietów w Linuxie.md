---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---

---
Utworzono: 2025-01-18 15:55
Tagi: [[INFORMATYKA]], [[LINUX]]

---

### **Alternatywy i instalacja oprogramowania `alien`**
Przede wszystkim, na samym początku zawsze najlepiej będzie zainstalować aplikację z autoryzowanego źródła jej dystrybucji (jeżeli nie jest dostępna w pakiecie natywnym dla danej dystrybucji, dobrze jest zwrócić się w stronę Flatpaka lub nawet pakietów Snap). Kiedy jednak obie te drogi nie są dostępne, istnieje możliwość manualnego przekonwertowania pakietów aplikacji na inny typ. Służy do tego oprogramowanie CLI `alien`. By je zainstalować w systemie Fedora, należy użyć komendy

```bash
sudo dnf install alien
```

### **Konwertowanie między pakietami z `.deb` do `.rpm` w Linuxie przy użyciu programu `alien`**
Wszystkie poniższe przykłady zostały zaczerpnięte z podlinkowanego w źródłach forum i opierają się o aplikację **Termius**. Schemat działania ma jednak oczywiście zastosowanie do dużo szerszego grona aplikacji, które nie są natywnie spakowane w pliki `.rpm`. Znajdując się w katalogu z pobranym wcześniej pakietem `.deb`, należy uruchomić następującą komendę:

```bash
sudo alien -r -g -v ./Termius.deb
```

Następnie należy przejść do otrzymanego w ten sposób katalogu, a będąc wewnątrz niego uruchamiamy kolejną komendę CLI:

```bash
sudo sed -i 's/Summary:/Summary: termius-app/' termius-app-7.49.0-2.spec
```

Aby w końcu zbudować plik z rozszerzeniem `.rpm`, cały czas znajdując się w utworzonym powyżej folderze, należy użyć komendy budowania:

```bash
sudo rpmbuild --target=x86_64 --buildroot $PWD  -bb termius-app-7.49.0-2.spec
```

---
## Źródła i odniesienia:
- https://discussion.fedoraproject.org/t/deb-to-rpm-conversion-with-alien-not-working/72504