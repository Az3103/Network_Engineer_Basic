# MAC-адреса сетевых устройств.


###  Топология:

![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab02/lab02_screen01.png "Топология")



### Таблица адресации:

| Устройство       | Интерфейс                | IP-адрес   | Маска подсети |
| ------------- |:------------------:| ----- | -----:|
| S1     | VLAN1    | 192.168.1.11 | 255.255.255.0 |
| S2     | VLAN1    | 192.168.1.12 | 255.255.255.0 |
| PC-A   | NIC      | 192.168.1.1  | 255.255.255.0 |
| PC-B   | NIC      | 192.168.1.2  | 255.255.255.0 |



###  Задачи:

1. **Создание и настройка сети;**
2. **Изучение таблицы МАС-адресов коммутатора;**



### Решение:

1. Создадим консольное соединение согласно топологии на рисунке. Меняем имена компьютеров и прописываем их айпи-адреса с маской подсети. Далее проводим инициализацию и перезагрузку коммутаторов:


```
enable
show flash
delete vlan.dat
erase startup-config
reload
```

После этого пропишем основные параметры коммутаторов. Ниже показан пример настройки коммутатора S1. Аналогично, меняя только айпи и имя хоста, настраиваем коммутатор S2:


```
enable
configure terminal
no ip domain-lookup
hostname S1
service password-encryption
enable secret class
banner motd #
DO NOT ENTER! #
interface vlan1
ip address 192.168.1.11 255.255.255.0
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

2. Теперь изучим таблицы MAC-адресов коммутаторов:


С помощью команды ipconfig /all в командной строке компьютеров - изучим физические адреса Ethernet и проверим айпи-адреса.

![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab02/lab02_screen02.png)

С помощью команды show interface f0/1 в консоли коммутаторов - изучим мак-адреса коммутаторов.


