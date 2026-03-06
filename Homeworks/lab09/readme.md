# Принципы обеспечения безопасности сети.



###  Топология:

![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab09/lab09_screen01.png)


### Таблица адресации:


![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab09/lab09_screen02.png)



###  Задачи:


1. Настройка основного сетевого устройства

  Создайте сеть.

  Настройте маршрутизатор R1.
  
  Настройка и проверка основных параметров коммутатора
  
2. Настройка сетей VLAN

  Сконфигруриуйте VLAN 10.
  
  Сконфигруриуйте SVI для VLAN 10.
  
  Настройте VLAN 333 с именем Native на S1 и S2.
  
  Настройте VLAN 999 с именем ParkingLot на S1 и S2.
  
3. Настройки безопасности коммутатора.

  Реализация магистральных соединений 802.1Q.
  
  Настройка портов доступа
  
  Безопасность неиспользуемых портов коммутатора
  
  Документирование и реализация функций безопасности порта.
  
  Реализовать безопасность DHCP snooping .
  
  Реализация PortFast и BPDU Guard
  
  Проверка сквозной связанности.



1. Создадим сеть согласно топологии и инициализируем устройства. Загрузим конфигурационный скрипт на маршрутизатор R1:


```
enable
configure terminal
hostname R1
no ip domain lookup
ip dhcp excluded-address 192.168.10.1 192.168.10.9
ip dhcp excluded-address 192.168.10.201 192.168.10.202
ip dhcp relay information trust-all
!
ip dhcp pool Students
 network 192.168.10.0 255.255.255.0
 default-router 192.168.10.1
 domain-name CCNA2.Lab-11.6.1
!
interface Loopback0
 ip address 10.10.1.1 255.255.255.0
!
interface GigabitEthernet0/0/1
 description Link to S1
 ip address 192.168.10.1 255.255.255.0
 no shutdown
!
line con 0
 logging synchronous
 exec-timeout 0 0
```
  
Далее настроим основные параметры коммутаторов:

```
enable
configure terminal
hostname S1
no ip domain lookup
interface fa 0/1
description Link to S2
interface fa 0/6
description Link to PC-A
exit
ip default-gateway 192.168.10.1
```

2. Настроим сети VLAN на коммутаторах:

```
enable
configure terminal
vlan 10
name Management
exit
interface vlan 10
description Management_VLAN10
ip address 192.168.10.201 255.255.255.0
no shutdown
exit
vlan 333
name Native
exit
vlan 999
name ParkingLot
exit
```
3. Настроим безопасность коммутаторов:

Сперва реализуем магистральное соединение на обоих коммутаторах. Проверим результат командой show interface trunk. Также отключаем согласование DTP.

```
enable
configure terminal
interface f 0/1
switchport mode trunk
switchport trunk native vlan 333
switchport nonegotiate
```

![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab09/lab09_screen03.png)

Настроим порты доступа на обоих коммутаторах. Также отключим неиспользуемые порты. Проверим результат командой show interfaces status.

```
enable
configure terminal
interface range f 0/5-6
switchport mode access
switchport access vlan 10
exit
interface range f 0/2-4, f 0/7-24, g 0/1-2
switchport mode access
switchport access vlan 999
shutdown
exit
```

![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab09/lab09_screen04.png)



Настроим безопасность портов на портах доступа:

Параметры безопасности порта f 0/6 на S1 по умолчанию приведены ниже.


![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab09/lab09_screen05.png)


Настроим нужные параметры на портах.

```
enable
configure terminal
interface f 0/6
switchport port-security
switchport port-security maximum 3
switchport port-security violation restrict
switchport port-security aging time 60
switchport port-security aging type                                      !!!!команда отсутствует в программе!!!!
switchport port-security mac-address sticky
```

Проверим результат на примере порта f 0/18 на коммутаторе S2.

![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab09/lab09_screen06.png)


Реализуем безопасность DHCP-snooping:

```
enable
configure terminal
ip dhcp snooping
ip dhcp snooping vlan 10
ip dhcp snooping information option
interface f 0/1
ip dhcp snooping trust
interface f 0/18
ip dhcp snooping limit rate 5
```
Проверим результат на S2 командами - show ip dhcp snooping и, после обновления айпи адреса на PC-B, командой show ip dhcp snooping binding.



![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab09/lab09_screen07.png)

