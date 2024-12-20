# Настройка DNS с помощью bind

## HQ-SRV

Устанавливаем bind:

```
apt-get install bind bind-utils
```

Редактируем конфиг:

```
nano  /var/lib/bind/etc/options.conf
```

Изменяем следующие параметры

```
В этом файле указываем сеть, которую нам дали. Пример: дали нам 10.0.0.0/23, разбили себе её на 10.0.0.0/24 и 10.0.1.0/24
Пишем их вместо 2 и 3 адресов. По идее если дали сеть 192.168.0.0/24 то пишем только её и все,
получится всего 2 сети. Лайфхак: если не поняли - молитесь и креститесь
(Адрес 127.0.0.1 указывается всегда, а дальше свои сети) 
```

![image](https://github.com/user-attachments/assets/8a78926c-b7de-4a61-8f44-593b42341ad4)
![image](https://github.com/user-attachments/assets/ebf23788-f13b-4f3a-a15c-c588d0c94bb8)

Проверяем на ошибки

```
named-checkconf
```

Если появилась вот такая ошибка:

![image](https://github.com/user-attachments/assets/36a79d9e-7a2f-4c9d-88c7-8734587e2a40)
 
Нужно скофигураровать ключи с помощью `rndc-confgen`

![image](https://github.com/user-attachments/assets/7147ebc4-6639-4b67-8947-60acc885f618)


Редактируем файл `var/lib/bind/etc/rndc.key `
```
nano /var/lib/bind/etc/rndc.key 
```

Встравляем в него ключ, который получили при помощи `rndc-confgen`

![image](https://github.com/user-attachments/assets/02c7a31d-44d3-49f7-92f2-43427a1cafe6)

Проверяем на ошибки

```
named-checkconf
```

Если ошибок нет, то запускаем `bind`

```
systemctl enable --now bind
systemctl status bind
```


Редактируем `resolv.conf`

```
nano /etc/net/ifaces/ens192/resolv.conf 
```

```
search au-team.irpo
nameserver 127.0.0.1
nameserver 192.168.0.2 [Адрес HQ-SRV]
nameserver 77.88.8.8
```

Перезагружаем сеть

```
systemctl restart network
dig ya.ru
```

![image](https://github.com/user-attachments/assets/43bdd4a0-2473-4419-8e14-246ca81a5a11)

### Создаем зону прямого просмотра

```
nano  /var/lib/bind/etc/local.conf

zone "au-team.irpo" {
        type master;
        file "au-team.irpo.db";
};
```

![image](https://github.com/user-attachments/assets/225eb54f-5674-4757-81c2-6faf1901433d)


Создаем копию файла-шаблона прямой зоны `/var/lib/bind/etc/zone/localdomain`

```
# cp /var/lib/bind/etc/zone/localdomain  /var/lib/bind/etc/zone/au-team.irpo.db
```

Задаем права на файл
```
chown named. /var/lib/bind/etc/zone/au-team.irpo.db

chmod 600 /var/lib/bind/etc/zone/au-team.irpo.db
```

Открываем для редактирования

```
nano /var/lib/bind/etc/zone/au-team.irpo.db
```

```
$TTL    1D
@       IN      SOA     au-team.irpo. root.au-team.irpo. (
                                2024102200      ; serial
                                12H             ; refresh
                                1H              ; retry
                                1W              ; expire
                                1H              ; ncache
                        )
        IN      NS      au-team.irpo.
        IN      A       192.168.0.2 [Адрес HQ-SRV, а дальше по названию машин понятно какие адреса сувать. Андрей осел кста]
hq-rtr  IN      A       192.168.0.1
br-rtr  IN      A       192.168.1.1
hq-srv  IN      A       192.168.0.2
hq-cli  IN      A       192.168.0.66
br-srv  IN      A       192.168.1.2
moodle  IN      CNAME   hq-rtr
wiki    IN      CNAME   hq-rtr
```

Проверяем, что зона настроена Предварительно

```
named-checkconf -z
```

![image](https://github.com/user-attachments/assets/dc7ae525-b388-4996-aded-bf9e398eb85b)

Перезагружаем `bind`

```
systemctl restart bind
```

Проверяем

```
dig hq-srv.au-team.irpo
```

![image](https://github.com/user-attachments/assets/ae64ef3e-cceb-4b4f-8706-e849cebb4d4c)


### Создаем зону обратного просмотра и PTR записи

```
nano  /var/lib/bind/etc/local.conf
```

```
zone "0.168.192.in-addr.arpa" {
        type master;
        file "au-team.irpo_rev.db";
};
```

Копируем шаблон файла

```
cp /var/lib/bind/etc/zone/{127.in-addr.arpa,au-team.irpo_rev.db}
```

Задаем права на файл
```
chown named. /var/lib/bind/etc/zone/au-team.irpo_rev.db

chmod 600 /var/lib/bind/etc/zone/au-team.irpo_rev.db
```

Открываем для редактирования

```
nano /var/lib/bind/etc/zone/au-team.irpo_rev.db
```

Вставляем в него следующее содержимое.

```
$TTL    1D
@       IN      SOA     au-team.irpo. root.au-team.irpo. (
                                2024102200      ; serial
                                12H             ; refresh
                                1H              ; retry
                                1W              ; expire
                                1H              ; ncache
                        )
        IN      NS      au-team.irpo.
1       IN      PTR     hq-rtr.au-team.irpo.
2       IN      PTR     hq-srv.au-team.irpo.
66      IN      PTR     hq-cli.au-team.irpo.
```

Проверяем

```
named-checkconf -z
```

![image](https://github.com/user-attachments/assets/f90d0e2e-3652-4d83-912d-e6e222a1e6bb)


Перезагружаем `bind`

```
systemctl restart bind
```

Проверяем

```
dig -x 192.168.0.2 [Адрес HQ-SRV]
```

![image](https://github.com/user-attachments/assets/60291e24-f8c2-4113-9162-a5168ec9dc6a)


### Комплекская проверка с HQ-CLI

![image](https://github.com/user-attachments/assets/4ed43108-dbc8-484e-8dc3-85eb39280113)

