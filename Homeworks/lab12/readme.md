# Настройка NAT для IPv4



###  Топология:

![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab12/lab12_screen01.png "Топология")



### Таблица адресации:


| Устройство       | Интерфейс                | IP-адрес   | Маска подсети |
| ------------- |:------------------| ----- | -----|
| R1     | G0/0/0    | 209.165.200.230 | 255.255.255.248 |
|        | G0/0/1    | 192.168.1.1 | 255.255.255.0 |
| R2     | G0/0/0    | 209.165.200.225 | 255.255.255.248 |
|        | Lo1    | 209.165.200.1 | 255.255.255.224 |
| S1     | VLAN 1    | 192.168.1.11 | 255.255.255.0 |
| S2     | VLAN 1    | 192.168.1.12 | 255.255.255.0 |
| PC-A   | NIC      | 192.168.1.2  | 255.255.255.0 |
| PC-B   | NIC      | 192.168.1.3  | 255.255.255.0 |




###  Задачи:

1. **Создание сети и настройка основных параметров устройства;**
2. **Настройка и проверка NAT для IPv4;**
3. **Настройка и проверка PAT для IPv4;**
4. **Настройка и проверка статического NAT для IPv4;**




### Решение:



1. Создадим сеть согласно топологии. Укажем необходимую адресацию на ПК. Далее настроим основные параметры на маршрутизаторе и коммутаторах. Ниже пример настройки роутера:


```
enable
configure terminal
no ip domain-lookup
hostname R2
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
interface g 0/0/0
ip address 209.165.200.225 255.255.255.248
no shutdown
interface Lo1
ip address 209.165.200.1 255.255.255.224
no shutdown
exit
ip route 0.0.0.0 0.0.0.0 209.165.200.230
exit
copy running-config startup-config
```


2. 	Настройка и проверка NAT для IPv4.
