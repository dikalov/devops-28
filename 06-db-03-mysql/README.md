## Задание 1
### Используя Docker, поднимите инстанс MySQL (версию 8). Данные БД сохраните в volume.

Подготавливаю образ(docker pull mysql:8.0), создаю volume, запускаю контейнер

![image](https://github.com/dikalov/devops-28/assets/126553776/75ad60e6-8245-4eed-9659-b4239635abf6)

![image](https://github.com/dikalov/devops-28/assets/126553776/04af17bb-1b90-444b-97af-208d017c9770)

Используя команду \h, получите список управляющих команд.

Найдите команду для выдачи статуса БД и приведите в ответе из её вывода версию сервера БД.

![image](https://github.com/dikalov/devops-28/assets/126553776/ce462394-416e-49bf-b7bd-b4288a86d33d)

Создаю базу и востаналиваю из дампа

![image](https://github.com/dikalov/devops-28/assets/126553776/2e538ae7-4ce3-4c0f-b08f-ec74a7b4d42d)

Копирую дамп

![image](https://github.com/dikalov/devops-28/assets/126553776/5c77eedd-3736-4e2c-8450-7e8766309d06)

Восстанавливаем базу данных
```
/etc/mysql# mysql -u root -p test_db < test_dump.sql
mysql> use test_db;
```
![image](https://github.com/dikalov/devops-28/assets/126553776/5d101e83-c85c-40fd-b2f1-a501af7f9ce7)

## Задание 2
### Создайте пользователя test в БД c паролем test-pass, используя:
плагин авторизации mysql_native_password; срок истечения пароля - 180 дней; количество попыток авторизации - 3; максимальное количество запросов в час - 100.

аттрибуты пользователя: 
Фамилия “Pretty”

Имя “James”

Предоставьте привелегии пользователю test на операции SELECT базы test_db.

Используя таблицу INFORMATION_SCHEMA.USER_ATTRIBUTES получите данные по пользователю test и приведите в ответе к задаче.

![image](https://github.com/dikalov/devops-28/assets/126553776/fb9e074c-1a1c-4bed-a617-641a42adea4b)

## Задание 3
### Установите профилирование SET profiling = 1. Изучите вывод профилирования команд SHOW PROFILES;. Исследуйте, какой engine используется в таблице БД test_db и приведите в ответе. 

![image](https://github.com/dikalov/devops-28/assets/126553776/2d66abec-c816-45a4-9d36-15194d75927b)

### Измените engine и приведите время выполнения и запрос на изменения из профайлера в ответе: на MyISAM, на InnoDB.

![image](https://github.com/dikalov/devops-28/assets/126553776/0da19d8f-3560-425a-bd75-7bcaceff43f5)

## Задание 4
### Изучите файл my.cnf в директории /etc/mysql.

![image](https://github.com/dikalov/devops-28/assets/126553776/c43379c2-23c9-4547-804f-4def24a05b2c)

### Измените его согласно ТЗ (движок InnoDB):

#### скорость IO важнее сохранности данных;
```
# 0 - скорость
# 1 - сохранность
# 2 - универсальный параметр
innodb_flush_log_at_trx_commit = 0
```
#### нужна компрессия таблиц для экономии места на диске;
```
# Barracuda - формат файла с сжатием путем увеличения нагрузки на цпу
innodb_file_format=Barracuda
```
#### размер буффера с незакомиченными транзакциями 1 Мб;
```
innodb_log_buffer_size	= 1M
```
#### буффер кеширования 30% от ОЗУ;
```
key_buffer_size = 300М
```
#### размер файла логов операций 100 Мб.
```
max_binlog_size	= 100M
```

```
bash-4.4# cat /etc/my.cnf
# For advice on how to change settings please see
# http://dev.mysql.com/doc/refman/8.0/en/server-configuration-defaults.html

[mysqld]
#
# Remove leading # and set to the amount of RAM for the most important data
# cache in MySQL. Start at 70% of total RAM for dedicated server, else 10%.
# innodb_buffer_pool_size = 128M
#
# Remove leading # to turn on a very important data integrity option: logging
# changes to the binary log between backups.
# log_bin
#
# Remove leading # to set options mainly useful for reporting servers.
# The server defaults are faster for transactions and fast SELECTs.
# Adjust sizes as needed, experiment to find the optimal values.
# join_buffer_size = 128M
# sort_buffer_size = 2M
# read_rnd_buffer_size = 2M

# Remove leading # to revert to previous value for default_authentication_plugin,
# this will increase compatibility with older clients. For background, see:
# https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_default_authentication_plugin
# default-authentication-plugin=mysql_native_password
skip-host-cache
skip-name-resolve
datadir=/var/lib/mysql
socket=/var/run/mysqld/mysqld.sock
secure-file-priv=/var/lib/mysql-files
user=mysql

pid-file=/var/run/mysqld/mysqld.pid
[client]
socket=/var/run/mysqld/mysqld.sock

# Custom config should go here
!includedir /etc/mysql/conf.d/
innodb_flush_log_at_trx_commit = 0
innodb_file_format=Barracuda
innodb_log_buffer_size= 1M
key_buffer_size = 300M
max_binlog_size= 100M
```




