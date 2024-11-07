# Настраиваем IP адреса

## Serial Port & Mac address

1. Добавляем новое устройство нажав на `Add other device`
2. Выбираем пункт `Serial port`
3. Изменяем тип порта на `Use network`
4. В строку `Port URL` добавляем значение в формате `telnet://0.0.0.0:<port>`. Номер порта для каждой виртуальной машины должен быть уникальным
5. Сохраняем изменения нажав кнопку `SAVE`
6. Включаем виртуальную машину.

Mac-address
1. vESR = **show interface status**
2. Eco = **show interface**

## HQ-CLI
![image](https://github.com/user-attachments/assets/0e732efa-0062-4799-9ed7-cd054f6537b4)


## ISP
![image](https://github.com/user-attachments/assets/b0259a8a-ea1d-49f6-affa-ecb77fa117aa)

```
configure

int gi1/0/1
ip address dhcp
ip firewall disable

int gi1/0/3
ip address 172.16.4.1/28
ip firewall disable
no shutdown

int gi1/0/2
ip address 172.16.5.1/28
ip firewall disable
no shutdown

commit
confirm
```

## HQ-RTR - EcoRouter

![image](https://github.com/user-attachments/assets/41f3a149-47ce-4a0d-a522-d64a4996738d)


Создаем сущность интерфейса и назначаем IP

```
int TO-ISP
ip address 172.16.4.2/28
no shutdown
```

Привязываем созданный интерфейс к физическому протоколу

```
port ge0
service-instance SI-ISP
encapsulation untagged
connect ip interface TO-ISP
```

Создаем интерфейсы, которые будут обрабатывать трафик vlan 100, 200, 999

```
interface HQ-SRV
 ip mtu 1500
 ip address 192.168.0.1/26 [Тут пишем адрес, который вы рассчитали для своей подсети. В таблице этот адрес в HQ-RTR - ge1.100]
!
interface HQ-CLI
 ip mtu 1500
 ip address 192.168.0.65/28 [Тут пишем адрес, который вы рассчитали для своей подсети. в таблице этот адрес в HQ-RTR - ge1.200]
!
interface HQ-MGMT
 ip mtu 1500
 ip address 192.168.0.81/29 [Тут пишем адрес, который вы рассчитали для своей подсети. в таблице этот адрес в HQ-RTR - ge1.999]
!
```

```
port ge1
 mtu 9234
 service-instance ge1/vlan100
  encapsulation dot1q 100
  rewrite pop 1
  connect ip interface HQ-SRV
 service-instance ge1/vlan200
  encapsulation dot1q 200
  rewrite pop 1
  connect ip interface HQ-CLI
 service-instance ge1/vlan999
  encapsulation dot1q 999
  rewrite pop 1
  connect ip interface HQ-MGMT
end
sh ip int br
```

Создаем GRE туннель

```
interface tunnel.1
 ip mtu 1400
 ip address 172.16.1.1/30
 ip tunnel 172.16.4.2 172.16.5.2 mode gre
```

Задаем маршрут по умолчанию в сторону ISP

```
ip route 0.0.0.0/0 172.16.4.1
```

## HQ-SRV

![image](https://github.com/user-attachments/assets/c3d86f74-53fd-4b05-8f10-44821f38603a)


```
TYPE=eth
DISABLED=no
NM_CONTROLLED=no
BOOTPROTO=static
CONFIG_IPv4=yes" > /etc/net/ifaces/ens192/options
```

```
192.168.0.2/26 > /etc/net/ifaces/ens192/ipv4address [Тут пишем адрес, который вы рассчитали для своей подсети. в таблице этот адрес в HQ-SRV - ens192]
```

```
default via 192.168.0.1 > /etc/net/ifaces/ens192/ipv4route [Тут пишем адрес, который вы рассчитали для своей подсети. В таблице этот адрес в HQ-RTR - ge1.100]
```

```
systemctl restart network
```

## BR-RTR

![image](https://github.com/user-attachments/assets/46877a71-1f3d-4d39-8105-1c9f8018af42)


```
configure

interface gigabitethernet 1/0/1
  ip firewall disable
  ip address 172.16.5.2/28
  no shutdown
exit
interface gigabitethernet 1/0/2
  ip firewall disable
  ip address 192.168.1.1/27 [Тут пишем адрес, который вы рассчитали для своей подсети. В таблице этот адрес в BR-RTR - gi1/0/2]
  no shutdown
exit
tunnel gre 1
  ttl 16
  mtu 1400
  ip firewall disable
  local address 172.16.5.2
  remote address 172.16.4.2
  ip address 172.16.1.2/30
  enable
exit
ip route 0.0.0.0/0 172.16.5.1

commit
confirm
```

## BR-SRV

![image](https://github.com/user-attachments/assets/9ee2091c-1fd0-44ed-b4f2-7a8e7d9623f1)

```
"TYPE=eth
DISABLED=no
NM_CONTROLLED=no
BOOTPROTO=static
CONFIG_IPv4=yes" > /etc/net/ifaces/ens192/options
```

```
192.168.1.2/26 > /etc/net/ifaces/ens192/ipv4address [Тут пишем адрес, который вы рассчитали для своей подсети. В таблице этот адрес в BR-SRV - ens192]
```

```
default via 192.168.1.1 > /etc/net/ifaces/ens192/ipv4route [Тут пишем адрес, который вы рассчитали для своей подсети. В таблице этот адрес в BR-RTR - gi1/0/2]
```

```
nameserver 192.168.0.2 > /etc/net/ifaces/ens192/resolv.conf 
nameserver 192.168.0.2 > /etc/resolv.conf
```

```
поставить # в строчке nameserver > /etc/resolvconf.conf
```

```
systemctl restart network
```
