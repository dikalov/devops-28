## Задание 1
### Используя Docker, поднимите инстанс PostgreSQL (версию 13). Данные БД сохраните в volume.
![image](https://github.com/dikalov/devops-28/assets/126553776/ef3acca3-5e97-4317-a948-dd0614530c29)

Подключился psql
```
vagrant@0server:/# psql -U postgres
psql (13.6 (Debian 13.6-1.pgdg110+1))
Type "help" for help.

postgres=#
```
Вывод списка БД
```
postgres-# \l+
                                                                   List of databases
   Name    |  Owner   | Encoding |  Collate   |   Ctype    |   Access privileges   |  Size   | Tablespace |                Description
-----------+----------+----------+------------+------------+-----------------------+---------+------------+--------------------------------------------
 postgres  | postgres | UTF8     | en_US.utf8 | en_US.utf8 |                       | 7901 kB | pg_default | default administrative connection database
 template0 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +| 7753 kB | pg_default | unmodifiable empty database
           |          |          |            |            | postgres=CTc/postgres |         |            |
 template1 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +| 7753 kB | pg_default | default template for new databases
           |          |          |            |            | postgres=CTc/postgres |         |            |
(3 rows)
```
Подключение к БД
```
postgres=# \c postgres
You are now connected to database "postgres" as user "postgres".
```
Вывод списка таблиц
```
postgres=# \dtS
                    List of relations
   Schema   |          Name           | Type  |  Owner
------------+-------------------------+-------+----------
 pg_catalog | pg_aggregate            | table | postgres
 pg_catalog | pg_am                   | table | postgres
 pg_catalog | pg_amop                 | table | postgres
 pg_catalog | pg_amproc               | table | postgres
 pg_catalog | pg_attrdef              | table | postgres
.....
 pg_catalog | pg_user_mapping         | table | postgres
(62 rows)
```
Вывод описания содержимого таблиц
```
postgres=# \dS+
                                            List of relations
   Schema   |              Name               | Type  |  Owner   | Persistence |    Size    | Description
------------+---------------------------------+-------+----------+-------------+------------+-------------
 pg_catalog | pg_aggregate                    | table | postgres | permanent   | 56 kB      |
 pg_catalog | pg_am                           | table | postgres | permanent   | 40 kB      |
 pg_catalog | pg_amop                         | table | postgres | permanent   | 80 kB      |
 pg_catalog | pg_amproc                       | table | postgres | permanent   | 64 kB      |
.....
 pg_catalog | pg_views                        | view  | postgres | permanent   | 0 bytes    |
(129 rows)
```
Выход из psql
```
postgres=# \q
vagrant@server1:/#
```
## Задание 2
### Используя psql создайте БД test_database.

