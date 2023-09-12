### Clichouse and Vector
Данный playbook скачивает и устанавливает Clichouse и Vector.

### Версия ОС рабочих станций
Centos7

### Версия ПО
Clickhouse - версия 21.1.9.41-2 (указывается в group_vars\сlickhouse\vars.yml)

Vector - версия 0.21.1 (указывается в group_vars\vector\vars.yml)

### Описание Task
Download distr - скачивает дистрибутив clickhouse Install clickhouse packages - устанавилвает пакеты для клиента сервара Create database - создает БД с таблицей

Install Vector - устанавилвает дистрибутив Vector Config template - настроит vector используя файл конфигурации jinja2 из папки templates
