# Команды IOS. Базовая конфигурация устройств.


###  Топология:

![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab1/lab1_screen1.png "Топология")  



### Таблица адресации:

| Устройство       | Интерфейс                | IP-адрес. Маска подсети |
| ------------- |:------------------:| -----:|
| S1     | VLAN1    | 192.168.1.20 |
|        |          | 255.255.255.0 |
| PC-A   | NIC      | 192.168.1.10 |
|        |          | 255.255.255.0 |  



###  Задачи:

1. **Проверка конфигурации коммутатора по умолчанию;**
2. **Создание сети и настройка основных параметров устройства;**
    * Настройте базовые параметры коммутатора.
    * Настройте IP-адрес для ПК.
3. **Проверка сетевых подключений;**
    * Отобразите конфигурацию устройства.
    * Протестируйте сквозное соединение, отправив эхо-запрос.
    * Протестируйте возможности удаленного управления с помощью Telnet.  



### Решение:

1. Создадим консольное соединение согласно топологии на рисунке. Проверим настройки коммутатора по умолчанию:

```
enable
show running-config
show startup-config
```

В файле running configuration видно, что коммутатор имеет 24 интерфейса FastEthernet и 2 интерфейса GigabitEthernet. В vty-линиях два диапазона - от 0 до 4 и от 5 до 15. Файл strartup configuration отсутствует. Для сети VLAN1 не назначен IP-адрес, интерфейс отключен. Версия операционной системы - 15.0(2)SE4. Файл образа системы называется - C2960-LANBASEK9_M.

Присоединив Ethernet-кабелем исходный ПК с 6 портом коммутатора, изучим интерфейс и флэш-память.

```
enable
show interface f0/6
show flash
```

Интерфейс f0/6 включен(connected). Он имеет мак - 00e0.8f25.9106. Заданы настройки - фулл дуплекс, скорость - 100 Мбит/сек. Образу операционной системы во флэш памяти присвоено имя - 2960-lanbasek9-mz.150-2.SE4.bin.



2. Настроим базовые параметры коммутатора и сохраним файл конфигурации:

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
ip address 192.168.1.20 255.255.255.0
no shutdown
exit
line console 0
logging synchronos
password cisco
login
line vty 0 15
password cisco
login
transport input telnet
exit
copy running-config startup-config
```

Команда логин запускает процесс авторизации по заданному паролю. После настройки IP-адреса на компьютере можно переходить к проверкам конфигурации и удаленного доступа.



3. Проверяем конфигурацию коммутатора:

![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab1/lab01_screen2.png)

![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab1/lab01_screen3.png)

Затем смотрим свойства VLAN1:

![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab1/lab01_screen4.png)

Далее тестируем ping:

![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab1/lab01_screen5.png)

И наконец проверяем удаленное соединение:

![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab1/lab01_screen6.png)


Настраивать пароль для VTY необходимо, так как по умолчанию виртуальные каналы не защищены. Чтобы пароли отправлялись в зашифрованном виде, необходимо использовать протокол SSH.


Файл с настройками из PacketTracer находится [здесь](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab1/lab01.pkt)
