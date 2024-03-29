## Задание 1
### Проверьте список доступных сетевых интерфейсов на вашем компьютере. Какие команды есть для этого в Linux и в Windows?

![image](https://user-images.githubusercontent.com/126553776/231386031-c77f76c0-f980-4b5c-96ac-1089f6691990.png)

В Windows это команда `ipconfig /all`

![image](https://user-images.githubusercontent.com/126553776/231387316-c1db8936-8f49-4a3e-bcb4-312db4866d1d.png)

Команда "ip a" используется в Linux-системах для вывода информации о сетевых интерфейсах и их состоянии. Она показывает IP-адреса и другую информацию о сетевых интерфейсах, таких как MAC-адреса, MTU (максимальный размер передаваемого пакета) и состояние интерфейса (активный или выключенный). Эта команда может быть полезна при настройке сетевых соединений или диагностировании проблем с сетью.

## Задание 2
### Какой протокол используется для распознавания соседа по сетевому интерфейсу? Какой пакет и команды есть в Linux для этого?

Протокол LLDP, пакет lldpd (sudo apt install lldpd).

Команда "lldpctl" выводит информацию о соседних устройствах, обнаруженных с помощью протокола LLDP (Link Layer Discovery Protocol). Эта команда отображает описание соседних устройств, такие как их тип, имя, порты и другую информацию о сетевом подключении, которую они передают с помощью LLDP. Кроме того, "lldpctl" может использоваться для отображения конфигурации LLDP на локальном устройстве. Эта команда может быть полезна для администраторов сетей при диагностировании проблем с сетевыми соединениями или при настройке сетевых устройств.

## Задание 3
### Какая технология используется для разделения L2-коммутатора на несколько виртуальных сетей? Какой пакет и команды есть в Linux для этого? Приведите пример конфига.

Технология VLAN (пакет vlan).

Пример конфига.
```

network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s3:
      optional: yes
      addresses: 
        - 192.168.0.52/24
  vlans:
    vlan10:
      id: 10
      link: enp0s3 
      addresses:
        - 192.168.1.2/24

```

## Задание 4
### Какие типы агрегации интерфейсов есть в Linux? Какие опции есть для балансировки нагрузки? Приведите пример конфига.

Вообще, если рассматривать данный вопрос достаточно обширно, то в  Linux существует несколько типов агрегации интерфейсов, вот некоторые из них:

1. Link Aggregation Control Protocol (LACP) - это протокол, который позволяет объединять несколько физических сетевых интерфейсов в один логический интерфейс. Этот тип агрегации интерфейсов используется для увеличения пропускной способности и повышения отказоустойчивости.

2. Multi-Channel - это тип агрегации интерфейсов, который используется для объединения нескольких физических интерфейсов в один логический интерфейс. Он также используется для увеличения пропускной способности и повышения отказоустойчивости.

3. Bonding - это метод объединения нескольких физических интерфейсов в один виртуальный интерфейс. Он используется для балансировки нагрузки, увеличения пропускной способности и повышения отказоустойчивости.

4. Virtual LAN (VLAN) - это метод разделения сетей на виртуальные сегменты. Он используется для улучшения безопасности и управления сетевым трафиком.Важно отметить, что наличие и поддержка различных типов агрегации интерфейсов может зависеть от конкретной операционной системы и оборудования.

Теперь перейдём к нашей системе и рассмотрим типы агрегации bonding:

![image](https://user-images.githubusercontent.com/126553776/231397496-379c80bc-8138-4b05-906d-c5378c9d5fc3.png)

balance-rr - пакеты отправляются последовательно (по кругу), начиная с первого доступного интерфейса и заканчивая последним. Эта политика применяется для балансировки нагрузки и отказоустойчивости. Обычно используется по-умолчанию.

active-backup - активный-резервный. Только один сетевой интерфейс из объединённых будет активным. Другой интерфейс может стать активным, только в том случае, когда упадёт текущий активный интерфейс. Эта политика применяется для отказоустойчивости.

balance-xor - передача распределяется между сетевыми картами используя формулу: [( «MAC адрес источника» XOR «MAC адрес назначения») по модулю «число интерфейсов»]. То есть, одна и та же сетевая карта передаёт пакеты одним и тем же получателям. Применяется для балансировки нагрузки и отказоустойчивости.

broadcast - Широковещательный режим. Передает всё на все сетевые интерфейсы. Применяется для отказоустойчивости.

802.3ad - агрегирование каналов по стандарту IEEE 802.3ad. Создаются агрегированные группы сетевых карт с одинаковой скоростью и дуплексом. При таком объединении передача задействует все каналы в активной агрегации, согласно стандарту IEEE 802.3ad.

balance-tlb - режим адаптивной балансировки нагрузки передачи. Исходящий трафик распределяется в зависимости от загруженности каждой сетевой карты (определяется скоростью загрузки). Не требует дополнительной настройки на коммутаторе. Входящий трафик приходит на текущую сетевую карту. Если она выходит из строя, то другая сетевая карта берёт себе MAC адрес вышедшей из строя карты.

balance-alb - режим адаптивной балансировки нагрузки. Включает в себя политику balance-tlb плюс осуществляет балансировку входящего трафика. Не требует дополнительной настройки на коммутаторе. Балансировка входящего трафика достигается путём ARP переговоров.


```
network:
   version: 2
   renderer: networkd
   ethernets:
     ens2:
       dhcp4: no 
       optional: true
     ens3: 
       dhcp4: no 
       optional: true
   bonds:
     bond0: 
       dhcp4: yes 
       interfaces:
         - ens2
         - ens3
       parameters:
         mode: active-backup
         primary: ens2
         mii-monitor-interval: 3
```
Данная конфигурация создает агрегированный интерфейс `bond0`, который объединяет два физических интерфейса `ens2` и `ens3`. В параметрах агрегированного интерфейса задан режим `active-backup`, что означает, что в случае отказа одного из интерфейсов, весь трафик будет перенаправлен на другой. Также задан параметр `primary: ens2`, который указывает, что интерфейс `ens2` будет использоваться в первую очередь, если оба интерфейса работают исправно. Для мониторинга состояния интерфейсов задан параметр `mii-monitor-interval: 3`, который указывает интервал мониторинга состояния интерфейсов в 3 секунды. Настройки для физических интерфейсов `ens2` и `ens3` заданы в блоке `ethernets`. В данной конфигурации для обоих интерфейсов указано `dhcp4: no`, что означает, что адреса будут заданы статически. Опция `optional: true` указывает, что интерфейсы не являются обязательными для работы системы и могут быть отключены. Для агрегированного интерфейса `bond0` задан параметр `dhcp4: yes`, что означает, что адрес будет получен по DHCP. Также указаны интерфейсы, которые входят в состав агрегированного интерфейса.

## Задание 5
### Сколько IP-адресов в сети с маской /29 ? Сколько /29 подсетей можно получить из сети с маской /24. Приведите несколько примеров /29 подсетей внутри сети 10.10.10.0/24.

![image](https://user-images.githubusercontent.com/126553776/231424664-feefb845-1b70-462d-b93d-d05534ca2707.png)

Всего 8 адресов. Из них 6 для хостов, 1 адрес сети и 1 широковещательный адрес.

![image](https://user-images.githubusercontent.com/126553776/231425393-be321a6d-3955-4d89-ad38-000b26e15b20.png)

Сеть с 24 маской можно разбить на 32 сети с 29 маской. Примеры этих сетей: 10.10.10.0/29; 10.10.10.8/29; 10.10.10.16/29 и т.д.

## Задание 6
### Задача: вас попросили организовать стык между двумя организациями. Диапазоны 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16 уже заняты. Из какой подсети допустимо взять частные IP-адреса? Маску выберите из расчёта — максимум 40–50 хостов внутри подсети.

![image](https://user-images.githubusercontent.com/126553776/231434988-364e0c23-6f8d-4b12-b07e-b8bca46a9d60.png)

Можно взять адреса из сети 100.64.0.0/10. Рационально использовать подсети /26 по 64 хоста. Например 100.64.0.0/26; 100.64.0.64/26 и т.д.

## Задание 7
### Как проверить ARP-таблицу в Linux, Windows? Как очистить ARP-кеш полностью? Как из ARP-таблицы удалить только один нужный IP?

В Windows это команда `arp -a`

![image](https://user-images.githubusercontent.com/126553776/231441145-aa0a45c0-b726-4351-becd-185b367a4f8c.png)

Команда `ip neigh flush all` удаляет все записи из ARP-таблицы, что может быть полезно, например, при изменении сетевой конфигурации или при возникновении проблем с соединением в локальной сети. Однако, очистка ARP-таблицы может привести к некоторому временному простою в работе сети, так как устройства должны повторно установить связь друг с другом и заполнить ARP-таблицу. (В Windows это команда `arp -d`)

А чтобы удалить только один адрес, то поможет команда `ip neigh delete <IP> dev <INTERFACE>`.

В Windows это команда `arp -d <IP>`


