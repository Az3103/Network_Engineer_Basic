# Реализация DHCPv4/6.



###  Топология:

![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab08/lab08_screen01.png)


### Таблица адресации:

| Устройство       | Интерфейс                | IPv6-адрес   | 
| ------------- |:------------------| ----- | 
| R1     | G0/0/0    | 2001:db8:acad:2::1/64 |
|      |     | fe80::1 |
|      | G0/0/1    | 2001:db8:acad:1::1/64 |
|      |     | fe80::1 |
| R2     | G0/0/0    | 2001:db8:acad:2::2/64 |
|     |     | 192.168.1.3 |
|     | G0/0/1    | 2001:db8:acad:2::2/64 |
|      |     | 192.168.1.2 |
| PC-A     | NIC    | DHCP |
| PC-B     | NIC    | DHCP |




###  Задачи:

1. **Создание сети и настройка основных параметров устройства;**
2. **Проверка назначения адреса SLAAC от R1;**
3. **Настройка и проверка сервера DHCPv6 без гражданства на R1;**
4. **Настройка и проверка состояния DHCPv6 сервера на R1;**
5. **Настройка и проверка DHCPv6 Relay на R2;**


1. Создадим сеть согласно топологии и настроим основные параметры устройств. Ниже пример настройки маршрутизатора R1:

```
enable
configure terminal
no ip domain-lookup
hostname R1
ipv6 unicast-routing
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


Настроим интерфейсы и маршрут по умолчанию, проверим связность:


```
interface gigabitEthernet 0/0/0
ipv6 address 2001:db8:acad:2::1/64
no shutdown
ipv6 address fe80::1 link-local
interface gigabitEthernet 0/0/1
ipv6 address 2001:db8:acad:1::1/64
no shutdown
ipv6 address fe80::1 link-local
end
show ipv6 interface brief
ipv6 route 2001:db8:acad:3::1/64 2001:db8:acad:2::2
exit
copy running-config startup-config
```


![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab08/lab08_screen02.png)
