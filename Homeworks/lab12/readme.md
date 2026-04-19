# Настройка NAT для IPv4



###  Топология:

![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab12/lab12_screen01.png "Топология")



### Таблица адресации:


| Устройство       | Интерфейс                | IP-адрес   | Маска подсети |
| ------------- |:------------------| ----- | -----|
| R1     | G0/0/0    | 209.165.200.230 | 255.255.255.248 |
|        | G0/0/1    | 192.168.1.1 | 255.255.255.0 |
| R2     | G0/0/0    | 209.165.200.225 | 255.255.255.248 |
|        | Lo1    | 209.165.200.1 | 255.255.255.224 |
| S1     | VLAN 1    | 192.168.1.11 | 255.255.255.0 |
| S2     | VLAN 1    | 192.168.1.12 | 255.255.255.0 |
| PC-A   | NIC      | 192.168.1.2  | 255.255.255.0 |
| PC-B   | NIC      | 192.168.1.3  | 255.255.255.0 |




###  Задачи:

1. **Создание сети и настройка основных параметров устройства;**
2. **Настройка и проверка NAT для IPv4;**
3. **Настройка и проверка PAT для IPv4;**
4. **Настройка и проверка статического NAT для IPv4;**




### Решение:



1. Создадим сеть согласно топологии. Укажем необходимую адресацию на ПК. Далее настроим основные параметры на маршрутизаторе и коммутаторах. Ниже пример настройки роутера:


```
enable
configure terminal
no ip domain-lookup
hostname R2
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
interface g 0/0/0
ip address 209.165.200.225 255.255.255.248
no shutdown
interface Lo1
ip address 209.165.200.1 255.255.255.224
no shutdown
exit
ip route 0.0.0.0 0.0.0.0 209.165.200.230
exit
copy running-config startup-config
```


2. 	Настройка и проверка NAT для IPv4. Настроим NAT на R1, используя пул из трех адресов 209.165.200.226-209.165.200.228:


```
enable
configure terminal
acces-list 1 permit 192.168.1.0 0.0.0.255
ip nat pool PUBLIC_ACCESS 209.165.200.226 209.165.200.228 netmask 255.255.255.248
ip nat inside source list 1 pool PUBLIC_ACCESS
interface g 0/0/1
ip nat inside
interface g 0/0/0
ip nat outside
end
show ip nat translations
```
После эхо-запросов получаем следующий результат:


![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab12/lab12_screen02.png "Топология")


При попытке пинговать с четвертого устройства подряд - попытка оказывается неудачной. Порядок устройств не важен. Никаких доп сообщений в консоли не выводится. Для корректных пингов с коммутаторов необходимо прописать ip default-gateway. На пк соответственно так же указываем шлюз.

Обязательно очищаем трансляции командой - clear ip nat translations *. Команда clear ip nat statistics не поддерживается.

3. Настройка и проверка PAT для IPv4. Ниже корректировка настроек на R1:

```
enable
configure terminal
no ip nat inside source list 1 pool PUBLIC_ACCESS
ip nat inside source list 1 pool PUBLIC_ACCESS overload
exit
show ip nat translations
```

После одинарного эхо-запроса, через минуту статистика nat translations очищается и более не выводится в консоль.

После запускаем пинг с параметром -t с обоих ПК. Команда show ip nat translations verbose не поддерживается. На R1 проверяем результат командой show ip nat translations:

![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab12/lab12_screen03.png "Топология")

Затем очистим статистику и остановим пинг.

Скорректируем настройки R1, вызвав перегрузку внешнего интерфейса:

```
enable
configure terminal
no ip nat inside source list 1 pool PUBLIC_ACCESS overload
no ip nat pool PUBLIC_ACCESS
ip nat inside source list 1 interface g 0/0/0 overload
exit
show ip nat translations
```


После запускаем пинг с параметром -t с обоих ПК. Команда ping repeat на коммутаторах не поддерживается. На R1 проверяем результат командой show ip nat translations:

![alt-текст](https://github.com/Az3103/Network_Engineer_Basic/blob/main/Homeworks/lab12/lab12_screen04.png "Топология")


