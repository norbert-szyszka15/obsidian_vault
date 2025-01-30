---
lastSync: Fri Oct 04 2024 18:57:35 GMT+0200 (Central European Summer Time)
---

---
Utworzono: 2025-01-24 14:03
Tagi: [[INFORMATYKA]], [[LINUX]], [[C]]

---
### **Deadlocki, gdzie można się na nie natknąć i ich skutki**  
Deadlock zachodzi, kiedy wątek próbuje zablokować jeden mutex więcej niż jeden raz, lub kiedy wiele wątków blokuje większą liczbę tych samych mutexów pojedynczo, ale w innej kolejności. W przypadku niewielkich programów napotkanie deadlocka jest raczej mało prawdopodobne, ale wraz ze skalowaniem kodu w górę szansa zablokowania wykonywania się całego programu znacząco wzrasta. Zasymulowanie pojawienia się deadlocka można wykonać np. następująco (odwrócenie kolejności blokowania mutexów przez wątki):

```c
void* routine(void* args) { 
	if (rand() % 2 == 0) { 
		pthread_mutex_lock(&mutexFuel); 
		sleep(1); 
		pthread_mutex_lock(&mutexWater); 
	} else {
		pthread_mutex_lock(&mutexWater); 
		sleep(1); 
		pthread_mutex_lock(&mutexFuel); 
	} 
	fuel += 50; 
	water = fuel; 
	printf("Incremented fuel to: %d set water to %d\n", fuel, water);
	pthread_mutex_unlock(&mutexFuel); 
	pthread_mutex_unlock(&mutexWater);
}
```

Na podstawie powyższej rutyny można przygotować prosty schemat obrazujący mechanizm zachodzenia deadlocka dla dwóch wątków:

|             | fuel mutex                | water mutex               |
| ----------- | ------------------------- | ------------------------- |
| **wątek 1** | x (lock) - działanie nr 1 | wait - działanie nr 3     |
| **wątek 2** | wait - działanie nr 4     | x (lock) - działanie nr 2 |
Jak widać zachodzi sytuacja, w której oba wątki czekają na odblokowanie mutexa, do którego chcą uzyskać dostęp. Wątek 1 czeka na odblokowanie jednego mutexa przez wątek 2, a ten z kolei czekan na odblokowanie drugiego mutexa przez wątek 1 - to jest dokładnie istota i definicja problemu deadlocków. Deadlocków można unikać modyfikując pewne właściwości samych mutexów, otrzymując w ten sposób [[Mutexy rekurencyjne]].

---
## Źródła i odniesienia:
- https://www.youtube.com/watch?v=LjWug2tvSBU&list=PLfqABt5AS4FmuQf70psXrsMLEDQXNkLq2&index=19
- https://code-vault.net/lesson/yk00ihnexz:1609011623086