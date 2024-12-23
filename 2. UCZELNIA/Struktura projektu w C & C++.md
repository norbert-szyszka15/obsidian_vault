---
lastSync: Fri Oct 04 2024 22:43:29 GMT+0200 (Central European Summer Time)
---
---
Utworzono: 2024-10-04 18:41
Tagi: [[INFORMATYKA]], [[C]], [[C++]]

---
### 1. Struktura plików i katalogów projektu

```bash
~/.../{proj root dir}
				|-- bin	              	(main executable file)
				|-- include	            (project header .h files)
				|-- lib		            (attached external/user libraries)
				|-- src		            (project source .cpp files)
				|-- main.cpp	        (project main source .cpp file)
				|-- Makefile	        (makefile file used to build project)
```

Skrypt w bashu do stworzenia nowej struktury projektu (podkreślony tekst może zostać zmieniony w celu zmiany nazwy katalogu nowego projektu, zmieniając tym samym całą jego nazwę):
```bash
mkdir -p project{/bin,/include,/lib,/src} && touch project/Makefile project/main.cpp
```

### 2. Plik `Makefile`

W celu zbudowania i skompilowania projektu jako całości, w katalogu projektu musi znajdować się plik `Makefile` zawierający następującą zawartość:
```Makefile
CXX       := g++
CXX_FLAGS := -std=c++17 -ggdb

BIN     := bin
SRC     := src
INCLUDE := include

LIBRARIES   :=
EXECUTABLE  := main


all: $(BIN)/$(EXECUTABLE)

run: clean all
	clear
	./$(BIN)/$(EXECUTABLE)

$(BIN)/$(EXECUTABLE): $(SRC)/*.cpp main.cpp
	$(CXX) $(CXX_FLAGS) -I$(INCLUDE) $^ -o $@ $(LIBRARIES)

clean:
	-rm $(BIN)/*
```

==UWAGA:== W celu zbudowania projektu w języku C zamiast C++, należy rozszerzenia plików w linijce
```Makefile
$(BIN)/$(EXECUTABLE): $(SRC)/*.cpp main.cpp
```
w następujący sposób:
```Makefile
$(BIN)/$(EXECUTABLE): $(SRC)/*.c main.c
```

### 3. Budowanie projektu po wprowadzeniu zmian

By zbudować projekt po wprowadzeniu do niego zmian, należy użyć komendy `make` w terminalu, znajdując się w korzeniu struktury katalogów projektu.

==UWAGA:== Jeżeli komenda `make` zwraca kod błędu o treści `Makefile:(line number): *** missing separator. Stop.`, należy zwrócić uwagę by w pliku `Makefile` użyć komendy `>Convert Identations to Tabs` w celu zamiany znaków spacji na znaki tabulacji.

###  4. `tasks.json` - setup zadań budowania projektu w VS Code

W celu stworzenia zadań budowania projektu w VS Code, należy użyć skrótu klawiszowego `ctrl+shift+b` kiedy żadne okna edytora nie są otwarte, a potem kolejno:
- wybrać opcję `Configure Build task`,
- wybrać opcję `Create tasks.json file from template`,
- wybrać opcję `Others`.

W tym momencie powinien zostać utworzony nowy plik `tasks.json`, którego zawartość należy zastąpić następującym kodem:
```json
{
    "version": "2.0.0",
    "tasks": [
        {
            "label": "build",
            "type": "shell",
            "command": "make",
            "group": {
                "kind": "build",
                "isDefault": true,
                "problemMatcher": "$gcc"
            }
        }
    ]
}
```

### 5. `launch.json` - ustawianie GDB jako domyślnego debuggera

W celu dodania konfiguracji debuggera, należy wcisnąć klawisz `F5`, a następnie wybrać opcję `C/C++` w wyskakującym okienku. W ten sposób w katalogu projektu wygenerowany zostanie plik `launch.json`, do którego należy wkleić poniższy kod:
```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "(gdb) Launch",
            "type": "cppdbg",
            "request": "launch",
            "program": "${workspaceFolder}/bin/main",
            "args": [],
            "stopAtEntry": false,
            "cwd": "${workspaceFolder}",
            "environment": [],
            "externalConsole": false,
            "MIMode": "gdb",
            "setupCommands": [
                {
                    "description": "Enable pretty-printing for gdb",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true,
                    "preLaunchTask": "build"
                }
            ]
        }
    ]
}
```

---
## Źródła i odniesienia:

- https://dev.to/talhabalaj/setup-visual-studio-code-for-multi-file-c-projects-1jpi