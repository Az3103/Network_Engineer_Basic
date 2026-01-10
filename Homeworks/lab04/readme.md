# Настройка IPv6-адресов на сетевых устройствах.


###  Топология:

![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab04/lab04_screen01.png "Топология")



### Таблица адресации:

| Устройство	| Интерфейс	| IPv6-адрес	| Link local IPv6-адрес	| Длина префикса	| Шлюз по умолчанию |
| ------------- |:------------------:| ----- | ----- | ----- | -----:|
|R1	|G0/0/0	|2001:db8:acad:a::1	|fe80::1	|64	|— |
|  	|G0/0/1	|2001:db8:acad:1::1 |fe80::1	|64	|— |
|S1	|VLAN 1	|2001:db8:acad:1::b	|fe80::b	|64	|— |
|PC-A	|NIC	|2001:db8:acad:1::3	|SLACC	|64	|fe80::1 |
|PC-B	|NIC	|2001:db8:acad:a::3	|SLACC	|64	|fe80::1 |



###  Задачи:

1. **Настройка топологии и конфигурация основных параметров маршрутизатора и коммутатора;**
2. **Ручная настройка IPv6-адресов;**
3. **Проверка сквозного соединения;**



### Решение:

Настроим основные параметры оборудования и соединения согласно топологии. Начнем с коммутатора:

```
enable
configure terminal
no ip domain-lookup
hostname S1
sdm prefer dual-ipv4-and-ipv6 default
end
reload
enable
configure terminal
service password-encryption
enable secret class
banner motd #
DO NOT ENTER! #
interface vlan1
no shutdown
exit
line console 0
logging synchronous
password cisco
login
line vty 0 15
password cisco
login
transport input telnet
exit
exit
copy running-config startup-config
```

После настройки проверим себя командой show sdm prefer:

![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab04/lab04_screen02.png)



Продолжим настройками маршрутизатора. Пропишем ipv6 адреса на интерфейсы:

```
enable
configure terminal
hostname R1
interface gigabitEthernet 0/0/0
ipv6 address 2001:db8:acad:a::1/64
no shutdown
ipv6 address fe80::1 link-local
interface gigabitEthernet 0/0/1
ipv6 address 2001:db8:acad:1::1/64
no shutdown
ipv6 address fe80::1 link-local
end
show ipv6 interface brief 
```

![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab04/lab04_screen03.png)
