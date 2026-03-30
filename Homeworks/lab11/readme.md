# Настройка и проверка расширенных списков контроля доступа


###  Топология:

![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab11/lab11_screen01.png "Топология")



### Таблица адресации:

| Устройство       | Интерфейс                | IP-адрес   | Маска подсети | Шлюз по умолчанию |
| ------------- |:------------------| ----- | -----| ----- |
| R1     | G0/0/1    | - | - | - |
|        | G0/0/1.20    | 10.20.0.1 | 255.255.255.0 |   |
|        | G0/0/1.30    | 10.30.0.1 | 255.255.255.0 |   |
|        | G0/0/1.40    | 10.40.0.1 | 255.255.255.0 |   |
|        | G0/0/1.1000  | -            | -             |   |
|        | Loopback1  | 172.16.1.1           | 255.255.255.0 |   |
| R2     | G0/0/1    | 10.20.0.4 | 255.255.255.0 | - |
| S1     | VLAN20    | 10.20.0.2 | 255.255.255.0 | 10.20.0.1 |
| S2     | VLAN20    | 10.20.0.3 | 255.255.255.0 | 10.20.0.1 |
| PC-A   | NIC      | 10.30.0.10  | 255.255.255.0 | 10.30.0.1 |
| PC-B   | NIC      | 10.40.0.10  | 255.255.255.0 | 10.40.0.1 |



### Таблица VLAN:

| VLAN       | Имя                | Назначенный интерфейс   |
| ------------- |:------------------| ----- |
| 20         | Management         | S2: F0/5             |
| 30         | Operations         | S1: F0/6               |
| 40         | Sales              | S2: F0/18                |
| 999        | Parking_lot        | S1: F0/2-4, F0/7-24, G0/1-2    |
|            |                    | S2: F0/2-4, F0/6-17, F0/19-24, G0/1-2    |
| 1000       | Собственная        | -             |



###  Задачи:

1. **Создание сети и настройка основных параметров устройства;**
2. **Настройка и проверка списков расширенного контроля доступа;**




### Решение:

1. Создадим сеть согласно топологии. Укажем необходимую адресацию на ПК. Далее настроим основные параметры на маршрутизаторе и коммутаторах. Ниже пример настройки роутера:


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
login local
exit
exit
copy running-config startup-config
```





Настроим VLAN и назначим порты коммутаторов. Неиспользуемые порты отключим административно. Ниже пример настроек коммутатора S1:


```
enable
configure terminal
ip default-gateway 10.20.0.1
vlan 20
name Management
exit
vlan 30
name Operations
exit
vlan 40
name Sales
exit
vlan 999
name Parking_Lot
exit
vlan 1000
name Special
exit
interface vlan 20
ip address 10.20.0.2 255.255.255.0
no shutdown
exit
interface f 0/6
switchport mode access
switchport access vlan 30
exit
interface range f0/2-4, f0/7-24, g0/1-2
switchport mode access
switchport sccess vlan 999
shutdown
exit
```


Проверим правильность конфигураций командой show vlan brief:

![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab11/lab11_screen02.png)


![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab11/lab11_screen03.png)





3. Сконфигурируем магистральный канал между коммутаторами, а также магистральный канал до маршрутизатора. Ниже пример настроек коммутатора S1:


```
enable
configure terminal
interface f 0/1
switchport mode trunk
switchport trunk native vlan 1000
switchport trunk allowed vlan 20,30,40,1000
end
interface f 0/5
switchport mode trunk
switchport trunk native vlan 1000
switchport trunk allowed vlan 20,30,40,1000
end
write memory
```

Проверим правильность настроек командой show interfaces trunk:


![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab11/lab11_screen04.png)



Настроим маршрутизацию между сетями VLAN. Ниже приведены настройки маршрутизатора и результаты проверки подинтерфейсов:



```
enable
configure terminal
interface g 0/0/1
no shutdown
interface g 0/0/1.20
encapsulation dot1q 20
ip address 10.20.0.1 255.255.255.0
description Management
interface g 0/0/1.30
encapsulation dot1q 30
ip address 10.30.0.1 255.255.255.0
description Operations
interface g 0/0/1.40
encapsulation dot1q 40
ip address 10.40.0.1 255.255.255.0
description Sales
interface g 0/0/1.1000
encapsulation dot1q 1000 native
no ip address
interface Loopback 1
ip address 172.16.1.1 255.255.255.0
end
copy running-config startup-config
```

![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab11/lab11_screen05.png)
