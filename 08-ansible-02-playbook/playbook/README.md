#### Данный playbook:

Загружает дистрибутивы указанные в одноимённых group_vars/{компонента}/vars.yml

Устанавливает clickhouse

Запускает clickhouse-server

Устанавливает vector

#### Для работы playbook:

IP хоста нужно задать в файле инвентаризации prod.yml

Отредактировать версии ПО возможно в group_vars\clickhouse

Запустить playbook
