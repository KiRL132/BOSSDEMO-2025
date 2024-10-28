# BOSSDEMO-2025

# Требования к машинам.

| Машина | RAM,ГБ | CPU | HDD/SDD, ГБ | Шаблон |
| ------ | ------ | --- | ----------- | ------ |
| ISP | 4 | 1 | 10 | «vESR» |
| HQ-RTR | 4 | 1 | 10 | «EcoRouter» |
| BR-RTR | 4 | 1 | 10 | «vESR» |
| HQ-SRV | 2 | 1 | 10 | «ALT-server» |
| BR-SRV | 2 | 1 | 10 | «ALT-server» |
| HQ-CLI | 3 | 2 | 15 | «ALT-workstatison» |

# IP-адресации

| Имя устройства | Интерфейс | IP          | Маска           | Шлюз        |
| -------------- | --------- | ----------  | --------------- | ----------- |
| ISP            | gi1/0/1   | DHCP        |                 |             |
|                | gi1/0/2   | 172.16.5.1  | 255.255.255.240 |             |
|                | gi1/0/3   | 172.16.4.1  | 255.255.255.240 |             |
| HQ-RTR         | ge0       | 172.16.4.2  | 255.255.255.240 | 172.16.4.1  |      
|                | ge1.100   | 192.168.0.1 | 255.255.255.192 |             |      
|                | ge1.200   | 192.168.0.65| 255.255.255.240 |             |      
|                | ge1.999   | 192.168.0.81| 255.255.255.248 |             |      
|                | tunnel.1  | 172.16.1.1  | 255.255.255.252 |             |      
| BR-RTR         | gi1/0/1   | 172.16.5.2  | 255.255.255.240 | 172.16.5.1  |      
|                | gi1/0/2   | 192.168.1.1 | 255.255.255.224 |             |
|                | gre1      | 172.16.1.2  | 255.255.255.252 |             |
| HQ-SRV         | ens192    | 192.168.0.2 | 255.255.255.192 | 192.168.0.1 |      
| HQ-CLI         | ens192    | DHCP        | 255.255.255.240 | 192.168.0.65|      
| BR-SRV         | ens192    | 192.168.1.2 | 255.255.255.224 | 192.168.1.1 |      


> [!TIP]
> **IP-Адресация** >>> **[НАСТРОЙКА](./IP_address.md)**

> [!TIP]
> **OSPF** >>> **[НАСТРОЙКА](./OSPF.md)**

> [!TIP]
> **NAT** >>> **[НАСТРОЙКА](./NAT.md)**

> [!TIP]
> **DHCP** >>> **[НАСТРОЙКА](./DHCP.md)**

> [!TIP]
> **DNS** >>> **[НАСТРОЙКА](./DNS.md)**

> [!TIP]
> **SSH** >>> **[НАСТРОЙКА](./SSH.md)**

> [!TIP]
> **Пользователи** >>> **[НАСТРОЙКА](./Users.md)**

> [!TIP]
> **Часовой пояс** >>> **[НАСТРОЙКА](./Time.md)**
