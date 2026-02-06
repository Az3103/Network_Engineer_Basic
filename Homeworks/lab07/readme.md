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
interface vlan 1
ip address 192.168.1.1 255.255.255.0
no shutdown
exit
exit
copy running-config startup-config
```


Командой ping проверим связность:

![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab07/lab07_screen02.png)




2. Определим корневой мост(коммутатор). Отключим все порты, настроим транки и включим обратно порты с номерами 2 и 4:


```
configure terminal
interface range f0/1-24, g0/1-2
switchport mode access
shutdown
exit
interface range f0/1-4
switchport mode trunk
switchport trunk allowed vlan 1
exit
interface range f0/2, f0/4
no shutdown
exit
```

Проверим работу протокола командой show spanning-tree:

![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab07/lab07_screen03.png)


![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab07/lab07_screen04.png)


![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab07/lab07_screen05.png)




3. Проверим работу протокола STP, поменяв стоимость портов:

На коммутаторе с заблокированным портом изменим стоимость порта корневого моста

```
interface f 0/4
spanning-tree vlan 1 cost 18
//////////
no spanning-tree vlan 1 cost 18
exit
```

После смены стоимости порта - порты на коммутаторе S3 выглядят следующим образом:

![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab07/lab07_screen06.png)


После отмены команды по смене стоимости все возвращается к исходным значениям. Смена стоимости влечет за собой уменьшение метрики, соответственно меняется маршрут на более оптимальный.

4. Проверим работу протокола STP, поменяв приоритет портов:
