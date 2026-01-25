# Основы сетевой безопасности.


###  Топология:

![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab05/lab05_screen01.png "Топология")



### Таблица адресации:

| Устройство       | Интерфейс                | IP-адрес   | Маска подсети | Шлюз по умолчанию |
| ------------- |:------------------:| ----- | -----:| -----: |
| R1     | G0/0/1    | 192.168.1.1 | 255.255.255.0 | - |
| S2     | VLAN1    | 192.168.1.11 | 255.255.255.0 | 192.168.1.1 |
| PC-A   | NIC      | 192.168.1.3  | 255.255.255.0 | 192.168.1.1 |



###  Задачи:

1. **Настройка основных параметров устройства;**
2. **Настройка маршрутизатора для доступа по протоколу SSH;**
3. **Настройка коммутатора для доступа по протоколу SSH;**
4. **SSH через интерфейс командной строки (CLI) коммутатора;**


### Решение:

1. После создания сети согласно топологии - настроим маршрутизатор:

```
enable
configure terminal
no ip domain-lookup
hostname R1
service password-encryption
enable secret class
banner motd #
DO NOT ENTER! #
interface G0/0/1
ip address 192.168.1.1 255.255.255.0
no shutdown
exit
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

Указываем на ПК его айпи адрес, маску подсети и шлюз по умолчанию, согласно таблицы адресации. Теперь отправим эхо запрос с ПК на маршрутизатор:

![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab05/lab05_screen02.png "Топология")



2. Настроим работу маршрутизатора для доступа по протоколу SSH:


```
ip domain-name Yapierdole.com
crypto key generate rsa           ''1024-2048''
username admin password Adm1nP@55
line vty 0 15
transport input all
login local
```

Далее пробуем установить SSH соединение роутера и пк. Для этого в SSH Client введем имя пользователя и айпи адрес маршрутизатора.

![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab05/lab05_screen03.png "Топология")




3. Настроим коммутатор для доступа по протоколу SSH:

```
enable
configure terminal
no ip domain-lookup
hostname S1
ip domain-name BobrKurwa.com
crypto key generate rsa           ''1024-2048''
username admin password Adm1nP@55
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
transport input ssh
login local
exit
exit
copy running-config startup-config
```


Теперь проверим SSH соединение между ПК и коммутатором. Для этого также вводим в SSH Client на ПК айпи адрес коммутатора и имя пользователя:

![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab05/lab05_screen04.png "Топология")



4.	Настроим протокол SSH с использованием интерфейса командной строки (CLI) коммутатора:

![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab05/lab05_screen05.png "Топология")


Файл с настройками из PacketTracer находится [здесь](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab05/lab05.pkt)
