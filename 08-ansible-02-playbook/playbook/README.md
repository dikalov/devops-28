### Clichouse and Vector
Данный playbook скачивает и устанавливает Clichouse и Vector.

### Версия ОС рабочих станций
Centos7

### Версия ПО
Clickhouse - версия 21.1.9.41-2 (указывается в group_vars\сlickhouse\vars.yml)

Vector - версия 0.21.1 (указывается в group_vars\vector\vars.yml)

### Описание Task
Download distr - скачивает дистрибутив clickhouse Install clickhouse packages - устанавливает пакеты для клиента сервера Create database - создает БД с таблицей

Install Vector - устанавливает дистрибутив Vector Config template - настраивает vector через файл конфигурации jinja2 из папки templates.
