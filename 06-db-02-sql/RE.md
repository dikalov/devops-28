## Задание 1
### Используя Docker, поднимите инстанс PostgreSQL (версию 12) c 2 volume, в который будут складываться данные БД и бэкапы. Приведите получившуюся команду или docker-compose-манифест.

![image](https://github.com/dikalov/devops-28/assets/126553776/b8dfcae1-9a88-4c8c-9a5c-baba932da3a4)

![image](https://github.com/dikalov/devops-28/assets/126553776/91508738-cc10-4b5b-9333-933db3970ced)

Данная команда запускает новый Docker-контейнер на хосте с именем "postgres_test" на основе образа PostgreSQL версии 12. Контейнер будет запускаться в интерактивном режиме (-ti) и в фоновом режиме (-d). Контейнер будет подключаться к сети хоста (--network host), что позволит ему использовать сетевые ресурсы хоста напрямую, без необходимости настройки проброса портов. Контейнер будет настроен с паролем "postgres" для пользователя "postgres" (-e POSTGRES_PASSWORD=postgres). Также контейнер будет настроен с двумя томами данных, которые будут монтироваться в контейнер: 

- Том с именем "vol1" будет монтироваться в директорию "/var/lib/postgresql/data" в контейнере и будет использоваться для хранения данных PostgreSQL. 
- Том с именем "vol2" будет монтироваться в директорию "/var/lib/postgresql/backup" в контейнере и будет использоваться для хранения резервных копий данных PostgreSQL.

Таким образом, данная команда запускает контейнер PostgreSQL с настройками пароля и двумя томами данных, который будет работать на хосте, используя сетевые ресурсы хоста напрямую.

![image](https://github.com/dikalov/devops-28/assets/126553776/b4b4a1ad-6033-4ed5-88b6-3be307912d25)


![image](https://github.com/dikalov/devops-28/assets/126553776/5356bfee-c97a-4c77-9963-ef4603694f48)

## Задание 2

![image](https://github.com/dikalov/devops-28/assets/126553776/13f02c51-544b-4cf1-b5e6-044431c12fbe)

итоговый список БД после выполнения пунктов выше

![image](https://github.com/dikalov/devops-28/assets/126553776/48d3b9a8-9c8e-423c-b781-14ddd250ee9c)

описание таблиц (describe)

![image](https://github.com/dikalov/devops-28/assets/126553776/a7bcfd9c-3878-4864-9e12-71ec9007f015)

SQL-запрос для выдачи списка пользователей с правами над таблицами test_db, а также список пользователей с правами над таблицами test_db

![image](https://github.com/dikalov/devops-28/assets/126553776/7ce5544a-a721-4c9a-b5e8-829e3f7ef6b7)

## Задание 3
### Используя SQL-синтаксис, наполните таблицы следующими тестовыми данными:

Таблица orders

![image](https://github.com/dikalov/devops-28/assets/126553776/01210a9e-2560-4df4-ab5c-a2d1579b48b0)

Таблица clients

![image](https://github.com/dikalov/devops-28/assets/126553776/08a722cb-89a2-435f-97ff-0b981262519c)

вычислите количество записей для каждой таблицы:

![image](https://github.com/dikalov/devops-28/assets/126553776/32bd0539-0c07-4964-b764-52ea713051c1)

## Задание 4
### Часть пользователей из таблицы clients решили оформить заказы из таблицы orders. Используя foreign keys, свяжите записи из таблиц, согласно таблице.

Приведите SQL-запросы для выполнения этих операций.
```
update clients set booking = (select id from orders where title = 'book') where second_name = 'Иванов Иван Иванович';
update clients set booking = (select id from orders where title = 'monitor') where second_name = 'Петров Петр Петрович';
update clients set booking = (select id from orders where title = 'guitar') where second_name = 'Иоганн Себастьян Бах';
select c.* from clients c join orders o on c.booking = o.id;
```
![image](https://github.com/dikalov/devops-28/assets/126553776/95bec594-59f2-44f0-90d6-f0476f98a867)

## Задание 5
### Получите полную информацию по выполнению запроса выдачи всех пользователей из задачи 4 (используя директиву EXPLAIN). Приведите получившийся результат и объясните, что значат полученные значения.

```
explain select c.* from clients c join orders o on c.booking = o.id;
```
![image](https://github.com/dikalov/devops-28/assets/126553776/3179abd8-025c-4b7a-ab10-3255a1afa777)

1) Чтение таблицы orders
2) Создание хеша для поля id
3) Сканирование таблицы clients
4) Для каждой строки по полю booking будет проверено, соответствует ли она чему-то в кеше orders
5) При наличии соответствий формируется вывод

Так же указано примерное количество строк, вес и стоимость.

## Задание 6
### Создайте бэкап БД test_db и поместите его в volume, предназначенный для бэкапов (см. задачу 1). Остановите контейнер с PostgreSQL, но не удаляйте volumes. Поднимите новый пустой контейнер с PostgreSQL. Восстановите БД test_db в новом контейнере. Приведите список операций, который вы применяли для бэкапа данных и восстановления.

Подключаюсь к контейнеру
```
vagrant@server1:/var/lib$ docker exec -it postgres_test bash
```
Создаю бекап на нужный volume
```
root@server1:/var/lib/postgresql/backup# pg_dump -U postgres -W test_db > /var/lib/postgresql/backup/test_db.sql
```
Остановил старый контейнер
```
vagrant@server1:~$ docker stop postgres_test
```
Поднял новый контейнер и смонтировал volume с бекапом
```
vagrant@server1:~$ docker run --network host --name postgres_test2 -e POSTGRES_PASSWORD=postgres -ti -d -v vol2:/var/lib/postgresql/backup postgres:12
```
Подключаемся к новому контейнеру
```
vagrant@server1:~$ docker exec -it postgres_test2 bash
```
Востановливаем из бекапа необходимой базы
```
root@server1:/# psql -U postgres -W test_db < /var/lib/postgresql/backup/test_db.sql
```
Проверим наличие базы и таблиц:

![image](https://github.com/dikalov/devops-28/assets/126553776/ec771dc2-e8c6-49bc-9e02-0f25cc571ffe)

![image](https://github.com/dikalov/devops-28/assets/126553776/f211124d-ee3d-4935-8661-a6a5b2987466)

