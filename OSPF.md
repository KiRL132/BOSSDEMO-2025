# Настраиваем OSPF

## HQ-RTR

```
router ospf 1
 network 172.16.1.0 0.0.0.3 area 0.0.0.0
 network 192.168.0.0 0.0.0.255 area 0.0.0.0 [Адрес в этой строчке указываем свою сеть. Пример: 10.0.0.0]
!
interface tunnel.1
 ip ospf authentication message-digest
 ip ospf message-digest-key 1 md5 Demo2025
 ip ospf network point-to-point
```

## BR-RTR

```
key-chain ospfkey
  key 1
    key-string ascii-text Demo2025
  exit
exit

router ospf 1
  area 0.0.0.0
    enable
  exit
  enable
exit

interface gigabitethernet 1/0/2
  ip ospf instance 1
  ip ospf
exit
tunnel gre 1
  ip ospf instance 1
  ip ospf network point-to-point
  ip ospf authentication key-chain ospfkey
  ip ospf authentication algorithm md5
  ip ospf
exit

commit
confirm
```

## Проверка

### HQ-RTR

```
sh ip ospf neighbor 
sh ip ospf interface brief
sh ip route
```

![image](https://github.com/user-attachments/assets/3bf39e81-8655-4150-a99e-cdb261053977)


### BR-RTR

```
sh ip ospf neighbor
sh ip route
```

![image](https://github.com/user-attachments/assets/fbc156ad-a3fd-4a63-95d5-65723795bd95)


```
sh ip ospf interface
```

![image](https://github.com/user-attachments/assets/c4be0f6c-a961-4f0c-acfc-9de5eb17e5f7)

