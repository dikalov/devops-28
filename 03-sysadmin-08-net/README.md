## Задание 1
### Подключитесь к публичному маршрутизатору в интернет. Найдите маршрут к вашему публичному IP

```
telnet route-views.routeviews.org
Username: rviews
show ip route x.x.x.x/32
show bgp x.x.x.x/32
```
![image](https://user-images.githubusercontent.com/126553776/234511738-2de2b6e1-95c4-439f-9cdb-5d7c49ce69ca.png)

![image](https://user-images.githubusercontent.com/126553776/234512170-1e795140-aaa0-46b2-9a78-472f985f8ae8.png)

## Задание 2
### Создайте dummy0 интерфейс в Ubuntu. Добавьте несколько статических маршрутов. Проверьте таблицу маршрутизации.

![image](https://user-images.githubusercontent.com/126553776/234526209-41fdd0ad-f541-479b-b762-189e8c4bab31.png)

![image](https://user-images.githubusercontent.com/126553776/234526604-3866e554-40dd-4896-b115-4db8c7efb027.png)

'sudo ip route add 10.0.20.0/24 via 10.0.2.15' - Через шлюз

'sudo ip route add 10.0.30.0/24 dev eth0' - Через интерфейс eth0

