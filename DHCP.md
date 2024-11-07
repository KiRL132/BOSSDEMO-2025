# Настройка DHCP на HQ-RTR (EcoRouter)

```
ip pool HQ-NET200 1
 range 192.168.0.66-192.168.0.70 [Пишем пул для HQ-CLI из 16 адресов]
!
dhcp-server 1
 lease 86400
 mask 255.255.255.0
 pool HQ-NET200 1
  dns 192.168.0.2 [Адрес HQ-SRV]
  domain-name au-team.irpo
  gateway 192.168.0.65 [Адрес vlan200 в HQ-RTR]
  mask 255.255.255.240 [Маска у vlan200 в HQ-RTR]
!
interface HQ-CLI
 dhcp-server 1
!
sh dhcp-server clients HQ-CLI
```
* Проверить адрес на HQ-CLI командой **ip a**
