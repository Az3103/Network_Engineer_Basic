# Внедрение маршрутизации между виртуальными локальными сетями.


###  Топология:

![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab06/lab06_screen01.png "Топология")



### Таблица адресации:

| Устройство       | Интерфейс                | IP-адрес   | Маска подсети | Шлюз по умолчанию |
| ------------- |:------------------| ----- | -----| ----- |
| R1     | G0/0/1.10    | 192.168.10.1 | 255.255.255.0 | - |
|        | G0/0/1.20    | 192.168.20.1 | 255.255.255.0 |   |
|        | G0/0/1.30    | 192.168.30.1 | 255.255.255.0 |   |
|        | G0/0/1.1000  | -            | -             |   |
| S1     | VLAN10    | 192.168.10.11 | 255.255.255.0 | 192.168.10.1 |
| S2     | VLAN10    | 192.168.10.12 | 255.255.255.0 | 192.168.10.1 |
| PC-A   | NIC      | 192.168.20.3  | 255.255.255.0 | 192.168.20.1 |
| PC-B   | NIC      | 192.168.30.3  | 255.255.255.0 | 192.168.30.1 |


### Таблица VLAN:

| VLAN       | Имя                | Назначенный интерфейс   |
| ------------- |:------------------| ----- |
| 10         | Управление         | S1: VLAN 10             |
|            |                    | S2: VLAN 10             |
| 20         | Sales              | S1: F0/6                |
| 30         | Operations         | S2: F0/18               |
| 999        | Parking_lot        | C1: F0/2-4, F0/7-24, G0/1-2    |
|            |                    | C2: F0/2-17, F0/19-24, G0/1-2    |
| 1000       | Собственная        | -             |


###  Задачи:

1. **Создание сети и настройка основных параметров устройства;**
2. **Создание сетей VLAN и назначение портов коммутатора;**
3. **Настройка транка 802.1Q между коммутаторами;**
4. **Настройка маршрутизации между сетями VLAN;**
5. **Проверка, что маршрутизация между VLAN работает;**



### Решение:

1. Создадим сеть согласно топологии. Укажем необходимую адресацию на ПК. Далее настроим основные параметры на маршрутизаторе и коммутаторах. Ниже пример настройки роутера:

```
enable
configure terminal
no ip domain-lookup
hostname R1
ip domain-name bobr.com
crypto key generate rsa           ''1024-2048''
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
transport input ssh
ip ssh version 2
exit
exit
copy running-config startup-config
```


2. Настроим VLAN и назначим порты коммутаторов. Неиспользуемые порты отключим административно. Ниже пример настроек коммутатора S1:


```
enable
configure terminal
ip default-gateway 192.168.10.1
vlan 10
name Management
exit
vlan 20
name Sales
exit
vlan 999
name Parking_Lot
exit
vlan 1000
name Special
exit
interface vlan 10
ip address 192.168.10.11 255.255.255.0
no shutdown
exit
interface f0/1
switchport mode access
switchport access vlan 10
exit
interface f 0/6
switchport mode access
switchport access vlan 20
exit
interface range f0/2-4, f0/7-24, g0/1-2
switchport mode access
switchport sccess vlan 999
shutdown
exit
```


Проверим правильность конфигураций командой show vlan brief:

![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab06/lab06_screen02.png)


![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab06/lab06_screen03.png)


3. Сконфигурируем магистральный канал между коммутаторами, а также магистральный канал до маршрутизатора. Ниже пример настроек коммутатора S1:


```
enable
configure terminal
interface f 0/1
switchport mode trunk
switchport trunk native vlan 1000
switchport trunk allowed vlan 10,20,30,1000
end
interface f 0/5
switchport mode trunk
switchport trunk native vlan 1000
switchport trunk allowed vlan 10,20,30,1000
end
write memory
```

Проверим правильность настроек командой show interface f 0/5 switchport:

![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab06/lab06_screen04.png)



