## ДЗ-28 Архитектура сетей

#### Цель домашнего задания
Научиться менять базовые сетевые настройки в Linux-based системах.
Описание домашнего задания
1. Скачать и развернуть Vagrant-стенд https://github.com/erlong15/otus-linux/tree/network
2. Построить следующую сетевую архитектуру:
Сеть office1
- 192.168.2.0/26      - dev
- 192.168.2.64/26     - test servers
- 192.168.2.128/26    - managers
- 192.168.2.192/26    - office hardware

Сеть office2
- 192.168.1.0/25      - dev
- 192.168.1.128/26    - test servers
- 192.168.1.192/26    - office hardware

Сеть central
- 192.168.0.0/28     - directors
- 192.168.0.32/28    - office hardware
- 192.168.0.64/26    - wifi

#### Схема_лабораторной_сети
![Схема_лабораторной_сети](https://github.com/nn-otus/hw-28-1/blob/main/pisc/1_NetScheme.PNG)  
Итого должны получиться следующие серверы:
inetRouter  
centralRouter  
office1Router  
office2Router  
centralServer  
office1Server  
office2Server  

Задание состоит из 2-х частей: теоретической и практической.
В теоретической части требуется: 
Найти свободные подсети
Посчитать количество узлов в каждой подсети, включая свободные
Указать Broadcast-адрес для каждой подсети
Проверить, нет ли ошибок при разбиении
## Теоретическая часть 
<details>
<summary>В теоретической части нам необходимо продумать топологию сети, а также:
Найти свободные подсети
Посчитать количество узлов в каждой подсети, включая свободные
Указать Broadcast-адрес для каждой подсети
Проверить, нет ли ошибок при разбиении
</summary>

Первым шагом мы рассмотрим все сети, указанные в задании. Посчитаем для них количество узлов, найдём Broadcast-адрес, проверим, нет ли ошибок при разбиении.

Расчеты можно выполнить вручную, или воспользоваться калькулятором сетей. Для примера, посчитаем одну подсеть вручную:

У нас есть сеть directors 192.168.0.0/28
192.168.0.0 — это сама сеть, 28 — это маска. Маска показывает нам, границы сети 192.168.0.0. Маска может быть записана в 2-х видах: 
1) /28
2) 255.255.255.240


Пример перевода маски /28 в формат 255.255.255.240:

Маска Ipv4-адреса — это 4 октета, т.е. 4 блока по 8 цифр (1 или 0). 
/28 — это 28 единиц с начала маски: 11111111.11111111.11111111.11110000  
Всегда при разбиении сетей, после знака / указывается количество единиц с начала маски.

11111111.11111111.11111111.11110000 — это двоичный код маски, если мы переведем данное значение в десятичную систему счисления, то получим 255.255.255.240

Далее посчитаем количество устройств в сети: 
Количество устройств в сети рассчитывается по формуле = 232−маска−2
Таким образом, количество устройств для подсети /28 будет = 232−28−2=16−2=14

Цифра 2 вычитается, так как:
Первый адрес (192.168.0.0) — это наименование подсети, его нельзя задать устройству
Последний адрес (192.168.0.15) — это всегда broadcast-адрес. Broadcast-адрес нужен для рассылки всем устройствам сети. 

Таким образом мы можем сформировать таблицу топологии нашей сети
|:--------------:|:---------------:|:--------------:|:-------------------:|:-----------------------:|:------------------:|  
| Сеть           | Маска           | Кол-во адресов | Первый адрес в сети | Последний адрес в сети  |  Broadcast — адрес |  
|192.168.0.0/28  |255.255.255.240  |    14          | 192.168.0.1         |192.168.0.14             |  192.168.0.15      |  
|:--------------:|:---------------:|:--------------:|:-------------------:|:-----------------------:|:------------------:|  

После расчета всех сетей, мы должны получить следующую таблицу топологии  
|:--------------:|:---------------:|:--------------:|:-------------------:|:-----------------------:|:------------------:|:------------------:|  
| Имя            | Сеть            | Маска          | Кол-во адресов      | Первый адрес            | Последний адрес    | Broadcast          |  
|                |                 |                |** Central Network **|                         |                    |                    |  
| Directors      | 192.168.0.0/28  | 255.255.255.240| 14                  | 192.168.0.1             | 192.168.0.14       | 192.168.0.15       |  
|Office hardware | 192.168.0.32/28 | 255.255.255.240| 14                  | 192.168.0.33            | 192.168.0.46       | 192.168.0.47       |  
| Wifi           | 192.168.0.64/26 | 255.255.255.192| 62                  | 192.168.0.65            | 192.168.0.126      | 192.168.0.127      |  
| | | | Office 1 network | | | |  
| Dev            | 192.168.2.0/26  | 255.255.255.192| 62                  | 192.168.2.1             | 192.168.2.62       | 192.168.2.63       |  
| Test           | 192.168.1.128/26| 255.255.255.192| 62                  | 192.168.2.65            | 192.168.2.126      | 192.168.2.127      |  
| Managers       | 192.168.2.128/26| 255.255.255.192| 62                  | 192.168.2.129           | 192.168.2.190      | 192.168.2.191      |  
| Office hardware| 192.168.2.192/26| 255.255.255.192| 62                  | 192.168.2.193           | 192.168.2.254      | 192.168.2.255      |  
| | | | Office 2 network | | | |  
| Dev            | 192.168.1.0/25  | 255.255.255.128| 126                 | 192.168.1.1             | 192.168.1.126      | 192.168.1.127      |  
| Test           | 192.168.1.128/26| 255.255.255.192| 62                  | 192.168.1.129           | 192.168.1.190      | 192.168.1.191      |  
| Office         | 192.168.1.192/26| 255.255.255.192| 62                  | 192.168.1.193           | 192.168.1.254      | 192.168.1.255      |  
| | | | InetRouter — CentralRouter network | | | |   
| Inet — central | 192.168.255.0/30| 255.255.255.252 | 2                  | 192.168.255.1           | 192.168.255.2      | 192.168.255.3      |  

После создания таблицы топологии, мы видим, что ошибок в задании нет, также мы сразу видим следующие свободные сети: 

192.168.0.16/28 
192.168.0.48/28
192.168.0.128/25
192.168.255.64/26
192.168.255.32/27
192.168.255.16/28
192.168.255.8/29  
192.168.255.4/30 

На этом теоретическая часть заканчивается

</details>

В практической части требуется: 
+ Соединить офисы в сеть согласно логической схеме и настроить роутинг
+ Интернет-трафик со всех серверов должен ходить через inetRouter
+ Все сервера должны видеть друг друга (должен проходить ping)
+ У всех новых серверов отключить дефолт на NAT (eth0), который vagrant поднимает для связи
+ Добавить дополнительные сетевые интерфейсы, если потребуется

## Практическая часть
На серверах и маршрутизаторах не бывает много сетевых карт, обычно 2 - 5.  
Поэтому задание будет решено с помощью виртуальных сетей
#### 1. Настройка InetRouter
##### 1.1. Настраиваем сетевые интерфейсы на сервере InetRouter

```
root@InetRouter:~# cat /etc/netplan/01-netcfg.yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    ens18:                      # mgmt
      dhcp4: false
      addresses:
        - 192.168.250.101/24
      routes:
        - to: default
          via: 192.168.250.1    # uplink to Internet
      nameservers:
        addresses:
          - 8.8.8.8
          - 1.1.1.1

    ens19:                      # CentralRouter
      dhcp4: false
      addresses:
        - 192.168.255.1/30
      routes:
         - to: 192.168.0.0/16
           via: 192.168.255.2
root@InetRouter:~#

```

Проверяем настройки:
```
root@InetRouter:~# ip -br a
lo               UNKNOWN        127.0.0.1/8 ::1/128
ens18            UP             192.168.250.101/24 fe80::be24:11ff:fe34:7430/64
ens19            UP             192.168.255.1/30 fe80::be24:11ff:fe56:16d1/64
root@InetRouter:~# ip route
default via 192.168.250.1 dev ens18 proto static
192.168.250.0/24 dev ens18 proto kernel scope link src 192.168.250.101
192.168.255.0/30 dev ens19 proto kernel scope link src 192.168.255.1

```
##### 1.2. Включаем маршрутизацию пакетов (IP forwarding)
```
nano /etc/sysctl.conf 
...
# Uncomment the next line to enable packet forwarding for IPv4
net.ipv4.ip_forward=1
...
root@InetRouter:~# sysctl -p
net.ipv4.ip_forward = 1
root@InetRouter:~#
```
##### 1.3. NAT - Настройка NAT и правил маршрутизации
Стартуем сервис nftables:  
```
root@InetRouter:~# systemctl enable nftables --now
Created symlink /etc/systemd/system/sysinit.target.wants/nftables.service → /usr/lib/systemd/system/nftables.service.
root@InetRouter:~# systemctl status nftables
● nftables.service - nftables
     Loaded: loaded (/usr/lib/systemd/system/nftables.service; enabled; preset: enabled)
     Active: active (exited) since Tue 2025-10-28 08:52:31 UTC; 4s ago
       Docs: man:nft(8)
             http://wiki.nftables.org
    Process: 1441 ExecStart=/usr/sbin/nft -f /etc/nftables.conf (code=exited, status=0/SUCCESS)
   Main PID: 1441 (code=exited, status=0/SUCCESS)
        CPU: 46ms

Oct 28 08:52:30 InetRouter systemd[1]: Starting nftables.service - nftables...
Oct 28 08:52:31 InetRouter systemd[1]: Finished nftables.service - nftables.
root@InetRouter:~#
```
Создаем файл конфигурации NAT:  
```
nano /etc/nftables.conf
#!/usr/sbin/nft -f

flush ruleset

table inet filter {
    chain input {
        type filter hook input priority 0;
        policy accept;
    }

    chain forward {
        type filter hook forward priority 0;
        policy drop;

        # Разрешаем форвард с внутреннего интерфейса наружу
        iif "enp19" oif "enp18" accept

        # Разрешаем ответы из внешней сети во внутреннюю
        iif "ens18" oif "enp0s8" ct state established,related accept
    }

    chain output {
        type filter hook output priority 0;
        policy accept;
    }
}

table ip nat {
    chain prerouting {
        type nat hook prerouting priority -100;
    }

    chain postrouting {
        type nat hook postrouting priority 100;
        # NAT (маскарадинг) для выхода во внешнюю сеть
        oif "ens18" masquerade
    }
}
```
Сохраняем и активируем:  
```
root@InetRouter:~# nft -f /etc/nftables.conf
root@InetRouter:~# systemctl restart nftables
root@InetRouter:~#
```
Проверяем:  
```
root@InetRouter:~# nft list ruleset
table inet filter {
        chain input {
                type filter hook input priority filter; policy accept;
        }

        chain forward {
                type filter hook forward priority filter; policy drop;
                iif "ens19" oif "ens18" accept
                iif "ens18" oif "ens19" ct state established,related accept
        }

        chain output {
                type filter hook output priority filter; policy accept;
        }
}
table ip nat {
        chain prerouting {
                type nat hook prerouting priority dstnat; policy accept;
        }

        chain postrouting {
                type nat hook postrouting priority srcnat; policy accept;
                oif "ens18" masquerade
        }
}

root@InetRouter:~#
root@InetRouter:~# ping ya.ru
PING ya.ru (5.255.255.242) 56(84) bytes of data.
64 bytes from ya.ru (5.255.255.242): icmp_seq=1 ttl=248 time=29.5 ms
64 bytes from ya.ru (5.255.255.242): icmp_seq=2 ttl=248 time=31.0 ms
```
#### 2. CentralRouter
##### 2.1. Настройка сети CentralRouter
```
root@CentralRouter:~# cat /etc/netplan/01-centralrouter.yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    ens18:
      dhcp4: false
      addresses: [192.168.250.122/24] # Mgmt
      routes:
        - to: 192.168.250.0/24
          via: 192.168.250.1

    ens19:
      dhcp4: false
      addresses: [192.168.255.2/30]
      routes:
        - to: default
          via: 192.168.255.1        # Uplink to InetRouter
      nameservers:
        addresses:
          - 8.8.8.8
          - 1.1.1.1
    ens20:
      dhcp4: false                  # Trunk port
      mtu: 1500

  vlans:
    vlan10:
      id: 10
      link: ens20
      addresses: [192.168.0.1/28]     # CentralServer

    vlan20:
      id: 20
      link: ens20
      addresses: [192.168.0.33/28]    # OfficeHardware network-1

    vlan30:
      id: 30
      link: ens20
      addresses: [192.168.0.65/26]    # OfficeHardware network-2

    vlan2200:
      id: 2200
      link: ens20
      addresses: [192.168.255.5/30]   # Office2-Router
      routes:
        - to: 192.168.1.0/24
          via:  192.168.255.6

    vlan100:
      id: 100
      link: ens20
      addresses: [192.168.255.9/30]   # Office1-Router
      routes:
        - to: 192.168.2.0/24
          via:  192.168.255.10
root@CentralRouter:~#
```
Включаем маршрутизацию.
```
root@CentralRouter:~# echo "net.ipv4.ip_forward=1" | sudo tee -a /etc/sysctl.conf
net.ipv4.ip_forward=1
root@CentralRouter:~# sysctl -p
net.ipv4.ip_forward = 1
```
Проверяем маршрут в Интернет - доступ осуществляется через InetRouter
```
root@CentralRouter:~# traceroute ya.ru
traceroute to ya.ru (77.88.44.242), 30 hops max, 60 byte packets
 1  _gateway (192.168.255.1)  0.629 ms  0.549 ms  0.484 ms
 2  192.168.250.1 (192.168.250.1)  1.519 ms  1.413 ms  1.334 ms^C
root@CentralRouter:~#

```
#### 3. R1 - Office1 Router
##### 3.1 Настройка сети R1
```
root@R1:~# cat /etc/netplan/01-r1-conf.yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    ens18:
      mtu: 1500 
      dhcp4: false
      addresses: [192.168.250.123/24] # mgmt

    ens19:
      dhcp4: false
      mtu: 1500

    ens20:
      dhcp4: false
      mtu: 1500

  vlans:
    vlan100:
      id: 100
      link: ens19
      addresses: [192.168.255.10/30]
      routes:
        - to: default
          via: 192.168.255.9            # Uplink to CentralRouter
      nameservers:
        addresses:
          - 8.8.8.8
          - 1.1.1.1

    vlan2210:
      id: 2210
      link: ens20
      addresses: [192.168.2.129/26]     # Office1Server Managers network

    vlan2220:
      id: 2220
      link: ens20
      addresses: [192.168.2.192/26]     # Office1Hardware network

    vlan2230:
      id: 2230
      link: ens20
      addresses: [192.168.2.65/26]      # Office1 test servers network

    vlan2240:
      id: 2240
      link: ens20
      addresses: [192.168.2.1/26]       # Office1 dev network
root@R1:~#
```
Проверяем включение маршрутизации
```
root@R1:~# sysctl -p
net.ipv4.ip_forward = 1
root@R1:~#
```
Проверяем маршрут доступа в Интернет - выполняется через InetRouter
```
traceroute to www.ru (31.177.80.70), 30 hops max, 60 byte packets
 1  _gateway (192.168.255.9)  0.488 ms  0.406 ms  0.394 ms
 2  192.168.255.1 (192.168.255.1)  0.880 ms  0.805 ms  0.532 ms
 3  192.168.250.1 (192.168.250.1)  1.773 ms  1.689 ms  1.608 ms^C
root@R1:~#
```


#### 4. R2 - Office2 Router
##### 4.1 Настройка сети R2
```
root@R2:~# cat /etc/netplan/01-r2-conf.yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    ens18:
      dhcp4: false
      addresses: [192.168.240.224/24] # mgmt

    ens19:
      dhcp4: false
      mtu: 1500

    ens20:                            # trunk port
      dhcp4: false
      mtu: 1500

  vlans:
    vlan2200:
      id: 2200
      link: ens19
      addresses: [192.168.255.6/30]
      routes:
        - to: default
          via: 192.168.255.5          # Uplink to CentralRouter
      nameservers:
        addresses:
          - 8.8.8.8
          - 1.1.1.1

    vlan310:
      id: 310
      link: ens20
      addresses: [192.168.1.1/25]     # Office2Server Managers network

    vlan320:
      id: 320
      link: ens20
      addresses: [192.168.1.193/26]   # Office2 Hardware network

    vlan330:
      id: 330
      link: ens20
      addresses: [192.168.1.129/26]   # Office2 test servers network
root@R2:~#
```
Проверяем, включена ли маршрутизация
```
root@R2:~# sysctl -p
net.ipv4.ip_forward = 1
root@R2:~#
```
Проверяем маршрут доступа в Интернет - выполняется через InetRouter
```
root@R2:~# traceroute www.ru
traceroute to www.ru (31.177.80.70), 30 hops max, 60 byte packets
 1  _gateway (192.168.255.5)  0.540 ms  0.418 ms  0.372 ms
 2  192.168.255.1 (192.168.255.1)  0.745 ms  0.611 ms  0.632 ms
 3  192.168.250.1 (192.168.250.1)  1.730 ms  1.575 ms  1.423 ms
```

#### 5. Настройка офисных серверов  

На серверах Central, Office1 и Office2 настраиваем маршрут по умолчанию
##### 5.1 CentralServer (Alpine Linux)
```
Central-Srv:~# cat /etc/network/interfaces
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
        address 192.168.240.118 #mgmt
        netmask 255.255.255.0
        
auto eth1
iface eth1 inet static
        address 192.168.0.2
        netmask 255.255.255.240
        gateway 192.168.0.1     #to CentralRouter
Central-Srv:~#
Central-Srv:~# traceroute www.ru
traceroute to www.ru (31.177.76.70), 30 hops max, 46 byte packets
 1  192.168.0.1 (192.168.0.1)  0.709 ms  0.498 ms  0.404 ms
 2  192.168.255.1 (192.168.255.1)  0.951 ms  0.753 ms  0.817 ms # Доступ в Интернет выполняется через InetRouter
```
##### 5.2 Office1-Srv
```
Office1-Srv:~# cat /etc/network/interfaces
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
        address 192.168.240.122        # mgmt
        netmask 255.255.255.0

auto eth1
iface eth1 inet static
        address 192.168.2.130
        netmask 255.255.255.192
        gateway 192.168.2.129          # default gateway
Office1-Srv:~#
Office1-Srv:~# traceroute 1.1.1.1
traceroute to 1.1.1.1 (1.1.1.1), 30 hops max, 46 byte packets
 1  192.168.2.129 (192.168.2.129)  0.884 ms  0.636 ms  0.478 ms
 2  192.168.255.9 (192.168.255.9)  1.218 ms  0.971 ms  0.635 ms
 3  192.168.255.1 (192.168.255.1)  1.360 ms  1.363 ms  1.353 ms  # Доступ в Интернет выполняется через InetRouter
```
##### 5.3 Office2-Srv
```
Office2-Srv:~# cat /etc/network/interfaces
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
        address 192.168.240.117      # mgmt
        netmask 255.255.255.0
        #gateway 192.168.240.1

auto eth1
iface eth1 inet static
        address 192.168.1.2          
        netmask 255.255.255.128
        gateway 192.168.1.1          # default gateway
Office2-Srv:~#
Office2-Srv:~# traceroute 1.1.1.1
traceroute to 1.1.1.1 (1.1.1.1), 30 hops max, 46 byte packets
 1  192.168.1.1 (192.168.1.1)  1.341 ms  0.446 ms  0.360 ms
 2  192.168.255.5 (192.168.255.5)  0.707 ms  0.954 ms  0.905 ms
 3  192.168.255.1 (192.168.255.1)  1.452 ms  1.190 ms  0.905 ms  # Доступ в Интернет выполняется через InetRouter
```
##### 5.4 Проверяем связность между серверами 

Central-Srv - Проверяем доступ в Интернет и к другим серверам
```
Central-Srv:~# ping -c1 ya.ru
PING ya.ru (77.88.55.242): 56 data bytes
64 bytes from 77.88.55.242: seq=0 ttl=55 time=34.815 ms

Central-Srv:~# ping -c1 Office1-Srv
PING Office1-Srv (192.168.2.130): 56 data bytes
64 bytes from 192.168.2.130: seq=0 ttl=62 time=1.659 ms

Central-Srv:~# ping -c1 Office2-Srv
PING Office2-Srv (192.168.1.2): 56 data bytes
64 bytes from 192.168.1.2: seq=0 ttl=62 time=2.318 ms
Central-Srv:~#
```
Office1-Srv - Проверяем доступ в Интернет и к другим серверам
```
Office1-Srv:/etc/apk$ ping -c1 ya.ru
PING ya.ru (77.88.44.242): 56 data bytes
64 bytes from 77.88.44.242: seq=0 ttl=42 time=30.903 ms

Office1-Srv:/etc/apk$ ping -c1 Central-Srv
PING Central-Srv (192.168.0.2): 56 data bytes
64 bytes from 192.168.0.2: seq=0 ttl=42 time=1.967 ms

Office1-Srv:/etc/apk$ ping -c1 Office2-Srv
PING Office2-Srv (192.168.1.2): 56 data bytes
64 bytes from 192.168.1.2: seq=0 ttl=42 time=2.555 ms
Office1-Srv:/etc/apk$
```
##### Office2-Srv (Alpine Linux)
```
Office2-Srv:~# ping www.ru
PING www.ru (31.177.76.70): 56 data bytes
64 bytes from 31.177.76.70: seq=0 ttl=55 time=29.396 ms
64 bytes from 31.177.76.70: seq=1 ttl=55 time=28.888 ms

Office2-Srv:~# ping -c1 Central-Srv
PING Central-Srv (192.168.0.2): 56 data bytes
64 bytes from 192.168.0.2: seq=0 ttl=62 time=1.804 ms

Office2-Srv:~# ping -c1 Office1-Srv
PING Office1-Srv (192.168.2.130): 56 data bytes
64 bytes from 192.168.2.130: seq=0 ttl=61 time=2.560 ms
```
![Скриншот пинга Интернет-ресурсов](https://github.com/nn-otus/hw-28-1/blob/main/pisc/2_srv_ping_inet.PNG)
Результат проверки: со всех серверов есть доступ в Интернет и связь с другими серверами

## ДЗ-28 выполнено





