# Проектная работа. Реализация подключения юридического лица к сетям провайдера.



###  Топология:

![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/project/project_screen01.png)




### Таблица адресации:


| Устройство       | Интерфейс                | IP-адрес   | Маска подсети | Шлюз по умолчанию |
| ------------- |:------------------:| ----- | -----:| -----: |
| R1     | G0/0/0    | 192.168.0.177 | 255.255.255.240 | - |
|        | G0/0/1    | 192.168.0.161 | 255.255.255.240 | - |
|        | Loopback1      | 10.30.0.1  | 255.255.255.0 | - |         
| MASTER_SWITCH1     | VLAN 100    | 192.168.0.2  | 255.255.255.192 | - |
|                    | VLAN 200    | 192.168.0.66 | 255.255.255.192 | - |
|                    | VLAN 300    | 192.168.0.130 | 255.255.255.224 | - |
|                    | g 0/1    | 192.168.0.162 | 255.255.255.240 | 192.168.0.161 |
| MASTER_SWITCH2     | VLAN 100    | 192.168.0.3  | 255.255.255.192 | - |
|                    | VLAN 200    | 192.168.0.67 | 255.255.255.192 | - |
|                    | VLAN 300    | 192.168.0.131 | 255.255.255.224 | - |
|                    | g 0/1    | 192.168.0.178 | 255.255.255.240 | 192.168.0.177 |
| SWITCH_SALES     | VLAN 100    | 192.168.0.1  | 255.255.255.192 | 192.168.0.3 |
| SWITCH_MANAGEMENT     | VLAN 200    | 192.168.0.65  | 255.255.255.192 | 192.168.0.67 |
| SWITCH_SERVERS     | VLAN 300    | 192.168.0.129  | 255.255.255.224 | 192.168.0.131 |
| SALES_PC1     | NIC    | 192.168.0.11 |255.255.255.192 | 192.168.0.1 |
| SALES_PC2     | NIC    | 192.168.0.12 |255.255.255.192 | 192.168.0.1 |
| SALES_PC3     | NIC    | 192.168.0.13 |255.255.255.192 | 192.168.0.1 |
| MANAGEMENT_PC1     | NIC    | 192.168.0.75 |255.255.255.192 | 192.168.0.65 |
| MANAGEMENT_PC2     | NIC    | 192.168.0.75 |255.255.255.192 | 192.168.0.65 |
| MANAGEMENT_PC3     | NIC    | 192.168.0.75 |255.255.255.192 | 192.168.0.65 |
| DNS     | NIC    | 192.168.0.139 |255.255.255.224 | 192.168.0.129 |
| WEB     | NIC    | 192.168.0.140 |255.255.255.224 | 192.168.0.129 |
| DHCP     | NIC    | 192.168.0.141 |255.255.255.224 | 192.168.0.129 |




###  Задачи:

Создание сети согласно топологии и отработка пройденных в ходе курса технологий.




###  Решение:

1. Создадим сеть согласно топологии. Назначим адресацию оконечных устройств. Далее пропишем основные параметры сетевых устройств:


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



2. Наша сеть разбита на 5 подсетей. Первые три предназначены для подключения различных устройств - ПК и серверов. Остальные две служат для выхода в интернет. На схеме прописаны соответствующие подсети и маски. Зададим адресацию согласно схемы и таблицы адресов. Обязательно укажем шлюзвы по умолчанию там, где это нужно.




3. Создадим привилигерованного пользователя с паролем для удаленного и безопасного доступа ко всем устройствам:

```
ip domain-name bobr.com
username admin privilege 15 secret password
crypto key generate rsa
ip ssh version 2
line vty 0 15
transport input ssh
login local
```

Попробуем получить доступ через SSH Client к коммутатору MASTER_SWITCH2 c помощью привилигерованного пользователя:


![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/project/project_screen02.png)




4. Настроим межвлановую маршрутизацию на L3 коммутаторах. Настроим каждый задействованный порт в режим Ассеss либо Trunk. Неиспользуемые порты переводятся в 999 Vlan и административно отключаются.


```
enable
configure terminal
vlan 100
name SALES
exit
vlan 200
name MANAGEMENT
exit
vlan 300
name SERVERS
exit
vlan 999
name EMPTY
exit
vlan 1000
name NATIVE
exit
interface vlan 100
ip address 192.168.0.2 255.255.255.192
no shutdown
exit
interface vlan 200
ip address 192.168.0.66 255.255.255.192
no shutdown
exit
interface vlan 300
ip address 192.168.0.130 255.255.255.224
no shutdown
exit
interface f0/11
switchport mode access
switchport access vlan 100
exit
interface f 0/12
switchport mode access
switchport access vlan 200
exit
interface f 0/13
switchport mode access
switchport access vlan 300
exit
interface range f0/4-10, f0/14-24, g0/2
switchport mode access
switchport access vlan 999
shutdown
interface port-channel 1
switchport mode trunk
switchport trunk native vlan 1000
switchport trunk allowed vlan 10,20,30,1000
end
exit
```

![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/project/project_screen03.png)





5. Протокол STP оставлен включеным по умолчанию. Немного скорректированы приоритеты во избежание колец. Ниже статистика spanning tree по коммутатору третьего кровня:




![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/project/project_screen04.png)








Файл с настройками из PacketTracer находится [здесь](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/project/project.pkt)
