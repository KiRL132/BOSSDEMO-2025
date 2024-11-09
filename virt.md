# Виртуальные сичи (ВНИМАТЕЛЬНО ЧИТАЙ МОЙ ГИТХАБ BL)


```
Переходим в "Networking" >> "Virtual switches" >> "add standart virtual switch"
```

![image](https://github.com/user-attachments/assets/de737ddc-3e75-4276-add0-e0428918f73b)

```
Даем имя "HQ-SW", раскрываем пункт "Security" и ставим везде "Accept"
```

![image](https://github.com/user-attachments/assets/f1336dfe-6f72-42c4-ac1a-e288ab91baec)

```
Даем имя "ISP-HQ", раскрываем пункт "Security" и ставим везде "Accept" (иначе пересдача)
```

![image](https://github.com/user-attachments/assets/f1014f7d-0c1c-44d2-9bf6-67ee40b09652)

```
ЧИТАЙ ВНИМАТЕЛЬНО BL
Создаем еще:
1. ISP-BR
2. SRV-HQ
3. BR-net
!!! НО !!! У этих трёх свичей в пункте "Security" оставляем "reject". (иначе пересдача)
```

```
Когда создали все свичи:
Переходим в "Networking" >> "Port group" >> "add port group (щас ваще внимательно)"
```

![image](https://github.com/user-attachments/assets/9dd04ec8-6e3b-4947-bce2-bcc4790e689d)

```
Щас надо сделать 6 портов, а именно:
1. SRV-net
2. CLI-net
3. HQ-net
4. ISP-BR
5. ISP-HQ
6. BR-net
```

![image](https://github.com/user-attachments/assets/82f0a940-941f-407e-848c-a4ed3006d4ea)


```
ВНИМАТЕЛЬНО!!!!!!!!!!!! BL.
Обращаем внимание какое имя в порта, какой !!"VLAN ID"!! и какой свич мы даем порту.
В Security ничего не меняем. Должно стоять везде "Inherit from vSwitch"
```

![image](https://github.com/user-attachments/assets/45af063b-cb34-47bb-bb73-ea31092994ed)


## Поздравляю, шансы на удачные пинги в будущем увеличены на 50%
