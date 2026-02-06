# Развертывание коммутируемой сети с резервными каналами.


###  Топология:

![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab07/lab07_screen01.png)


### Таблица адресации:

| Устройство       | Интерфейс                | IP-адрес   | Маска подсети |
| ------------- |:------------------| ----- | -----|
| S1     | VLAN1    | 192.168.1.1 | 255.255.255.0 |
| S1     | VLAN1    | 192.168.1.2 | 255.255.255.0 |
| S1     | VLAN1    | 192.168.1.3 | 255.255.255.0 |



###  Задачи:

1. **Создание сети и настройка основных параметров устройства;**
2. **Выбор корневого моста;**
3. **Наблюдение за процессом выбора протоколом STP порта, исходя из стоимости портов;**
4. **Наблюдение за процессом выбора протоколом STP порта, исходя из приоритета портов;**


### Решение:


1. Создадим сеть согласно топологии и настроим основные параметры устройств. Ниже пример настройки коммутатора S1:

```
enable
configure terminal
no ip domain-lookup
hostname S1
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
interface vlan 1
ip address 192.168.1.1 255.255.255.0
no shutdown
exit
exit
copy running-config startup-config
```


Командой ping проверим связность:

![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab07/lab07_screen02.png)
