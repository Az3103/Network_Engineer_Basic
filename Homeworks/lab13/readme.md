# Настройка протоколов CDP, LLDP и NTP




###  Топология:

![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab13/lab13_screen01.png "Топология")




### Таблица адресации:

| Устройство       | Интерфейс                | IP-адрес   | Маска подсети | Шлюз по умолчанию |
| ------------- |:------------------| ----- | -----| ----- |
| R1     | Loopback1    | 172.16.1.1 | 255.255.255.0 | - |
|        | G0/0/1    | 10.22.0.1 | 255.255.255.0 |   |
| S1     | SVI VLAN 1    | 10.22.0.2 | 255.255.255.0 | 10.22.0.1 |
| S2     | SVI VLAN 1    | 10.22.0.3 | 255.255.255.0 | 10.22.0.1 |




###  Задачи:

1. **Создание сети и настройка основных параметров устройства;**
2. **Обнаружение сетевых ресурсов с помощью протокола CDP;**
3. **Обнаружение сетевых ресурсов с помощью протокола LLDP;**
4. **Настройка и проверка NTP;**




### Решение:



1. Настроим основные параметры устройств:


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
interface g 0/0/1
ip address 10.22.0.1 255.255.255.0
no shutdown
interface Loopback1
ip address 172.16.1.1 255.255.255.0
exit
exit
copy running-config startup-config
```


2. Обнаружение сетевых ресурсов с помощью протокола CDP. Проверим информацию касательно CDP на маршрутизаторе, также определим версию IOS на коммутаторе S1:

```
show cdp interface
show cdp neighbors
show cdp entry S1
```

![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab13/lab13_screen02.png "Топология")

Интерфейсы CDP и версия операционной системы коммутатора видны на скриншоте выше. Теперь проверим выданные пакеты CDP с коммутатора S1:

![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab13/lab13_screen03.png "Топология")








