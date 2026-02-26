# Реализация DHCPv4.



###  Топология:

![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab08.2/lab08.2_screen01.png)


### Таблица адресации:


| Устройство       | Интерфейс                | IP-адрес   | Маска подсети | Шлюз по умолчанию |
| ------------- |:------------------:| ----- | -----:| -----: |
| R1     | G0/0/0    | 10.0.0.1 | 255.255.255.252 | - |
|        | G0/0/1    | - | - |  |
|        | G0/0/1.100      | 192.168.1.1  |  |  |
|        | G0/0/1.200    | 192.168.1.59 |  |  |
|        | G0/0/1.1000    | - | - |  |
| R2   | G0/0      | 10.0.0.2  | 255.255.255.252 |  |
|      | G0/0/1    | 192.168.1.87 |  |  |
| S1     | VLAN 200    | 192.168.1.6  |  | 192.168.1.59 |
| S2   | VLAN 1      |   |  |  | 
| PC-A     | NIC    | DHCP |DHCP | DHCP |
| PC-B     | NIC    | DHCP | DHCP | DHCP |



### Таблица VLAN:


![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab08.2/lab08.2_screen03.png)



###  Задачи:

1. **Создание сети и настройка основных параметров устройства;**
2. **Настройка и проверка двух серверов DHCPv4 на R1;**
3. **Настройка и проверка DHCP-ретрансляции на R2;**




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
transport input ssh
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
interface g 0/0/1.10
encapsulation dot1q 10
ip address 192.168.10.1 255.255.255.0
interface g 0/0/1.20
encapsulation dot1q 20
ip address 192.168.20.1 255.255.255.0
interface g 0/0/1.30
encapsulation dot1q 30
ip address 192.168.30.1 255.255.255.0
interface g 0/0/1.1000
encapsulation dot1q 1000 native
no ip address
end
copy running-config startup-config
```

