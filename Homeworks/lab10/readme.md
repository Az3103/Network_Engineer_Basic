# Настройка протокола OSPFv2 для одной области.



###  Топология:

![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab10/lab10_screen01.png)


### Таблица адресации:


| Устройство       | Интерфейс                | IP-адрес   | Маска подсети |
| ------------- |:------------------| ----- | -----|
| R1     | G0/0/1    | 10.53.0.1 | 255.255.255.0 |
|      | Loopback1    | 172.16.1.1 | 255.255.255.0 |
| R2     | G0/0/1    | 10.53.0.2 | 255.255.255.0 |
|      | Loopback1    | 192.168.1.1 | 255.255.255.0 |




###  Задачи:

1. **Создание сети и настройка основных параметров устройства;**
2. **Настройка и проверка базовой работы протокола  OSPFv2 для одной области;**
3. **Оптимизация и проверка конфигурации OSPFv2 для одной области;**




### Решение:


1. Создадим сеть согласно топологии и настроим основные параметры устройств. Ниже пример настройки маршрутизатора R1:


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

2. Настроим адреса интерфейса и базового OSPFv2 на каждом маршрутизаторе:


```
enable
configure terminal
router ospf 56
router-id 1.1.1.1
exit
interface G 0/0/1
ip address 10.53.0.1 255.255.255.0
ip ospf 56 area 0
no shutdown
interface Loopback1
ip address 172.16.1.1 255.255.255.0
ip ospf 56 area 0
exit
```

Проверим смежность:



![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab10/lab10_screen02.png)



![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab10/lab10_screen03.png)




3. Оптимизируем и проверим конфигурации OSPFv2 для одной области. Сперва настроим оптимизацию на маршрутизаторах:


```
enable
configure terminal
interface G 0/0/1
ip ospf priority 50
ip ospf hello-interval 30
exit
ip route 0.0.0.0 0.0.0.0 Loopback1
router ospf 56
default-information originate
auto-cost reference-bandwidth 10000
end
clear ip ospf process
```






```
enable
configure terminal
interface G 0/0/1
ip ospf hello-interval 30
interface Loopback1
ip ospf network point-to-point
exit
router ospf 56
passive-interface Loopback1
auto-cost reference-bandwidth 10000
end
clear ip ospf process
```




Теперь убедимся в реализации нашей оптимизации:

![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab10/lab10_screen04.png)



![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab10/lab10_screen05.png)







Файл с настройками из PacketTracer находится [здесь](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab10/lab10.pkt)
