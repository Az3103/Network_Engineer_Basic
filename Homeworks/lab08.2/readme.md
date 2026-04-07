# Реализация DHCPv4.



###  Топология:

![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab08.2/lab08.2_screen01.png)


### Таблица адресации:


| Устройство       | Интерфейс                | IP-адрес   | Маска подсети | Шлюз по умолчанию |
| ------------- |:------------------:| ----- | -----:| -----: |
| R1     | G0/0/0    | 10.0.0.1 | 255.255.255.252 | - |
|        | G0/0/1    | - | - |  |
|        | G0/0/1.100      | 192.168.1.1  | 255.255.255.128 |  |         ///Clients R1
|        | G0/0/1.200    | 192.168.1.129 | 255.255.255.192 |  |            ///Management
|        | G0/0/1.1000    | - | - |  |
| R2   | G0/0      | 10.0.0.2  | 255.255.255.252 | - |
|      | G0/0/1    | 192.168.1.193 | 255.255.255.224 |  |               ///Clients R2
| S1     | VLAN 200    |   |  |  |
| S2   | VLAN 1      |   |  |  | 
| PC-A     | NIC    | DHCP |DHCP | DHCP |
| PC-B     | NIC    | DHCP | DHCP | DHCP |



### Таблица VLAN:


![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab08.2/lab08.2_screen03.png)



###  Задачи:

1. **Создание сети и настройка основных параметров устройства;**
2. **Настройка и проверка двух серверов DHCPv4 на R1;**
3. **Настройка и проверка DHCP-ретрансляции на R2;**





###  Решение:


1. Настроим основные параметры маршрутизаторов:

```
enable
configure terminal
no ip domain-lookup
hostname R1
service password-encryption
enable secret class
banner motd #
DO NOT ENTER!  #
line console 0
logging synchronous
password cisco
login
line vty 0 15
password cisco
login
exit
exit
copy running-config startup-config
```


Далее включим и настроим интерфейсы маршрутизаторов:


```
enable
configure terminal
interface g 0/0/1
no shutdown
interface g 0/0/1.100
encapsulation dot1q 100
description CLIENTS_R1
ip address 192.168.1.1 255.255.255.128
interface g 0/0/1.200
encapsulation dot1q 200
description MANAGEMENT_R1
ip address 192.168.1.129 255.255.255.192
interface g 0/0/1.1000
encapsulation dot1q 1000 native
no ip address
interface g 0/0/0
no shutdown
ip address 10.0.0.1 255.255.255.252
end
ip route 0.0.0.0 0.0.0.0 10.0.0.2
copy running-config startup-config
```

