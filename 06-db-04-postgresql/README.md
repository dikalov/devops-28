## Задание 1
### Используя Docker, поднимите инстанс PostgreSQL (версию 13). Данные БД сохраните в volume.
![image](https://github.com/dikalov/devops-28/assets/126553776/ef3acca3-5e97-4317-a948-dd0614530c29)

Подключился psql
```
vagrant@server1:/# psql -U postgres
psql (13.4 (Debian 13.4-4.pgdg110+1))
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
