### Playbook содержит 2 блока задач:

Первый блок объединяет последовательность задач по инсталяции Clickhouse. Блоку соответствует тэг clickhouse. В блоке используются параметры:
clickhouse_version: "22.3.3.44" - версия Clickhouse
clickhouse_packages: ["clickhouse-client", "clickhouse-server", "clickhouse-common-static"] - список пакетов для установки
Task'и:
TASK [Clickhouse. Get clickhouse distrib] - скачивает deb-пакеты с дистрибутивами с помощью модуля ansible.builtin.get_url
TASK [Clickhouse. Install package clickhouse-common-static] - устанавливает deb-пакет с помощью модуля ansible.builtin.apt
TASK [Clickhouse. Install package clickhouse-client] - устанавливает deb-пакет с помощью модуля ansible.builtin.apt
TASK [Clickhouse. Install clickhouse package clickhouse-server] - устанавливает deb-пакеты с помощью модуля ansible.builtin.apt
TASK [Clickhouse. Flush handlers] - инициирует внеочередной запуск хандлера Start clickhouse service
RUNNING HANDLER [Start clickhouse service] - для старта сервера clickhouse в хандлере используется модуль ansible.builtin.service
TASK [Clickhouse. Waiting while clickhouse-server is available...] - устанавливает паузу в 10 секунд с помощью модуля ansible.builtin.pause, чтобы сервер Clickhouse успел запуститься. Иначе следующая задача по созданию БД может завершиться ошибкой, т.к. сервер еще не успел подняться
TASK [Clickhouse. Create database] - создает инстанс базы данных Clickhouse

Второй блок объединяет последовательность задач по инсталяции Vector. Блоку соответствует тэг vector. В блоке используются параметры:
vector_version: "0.21.1" - версия Vector
vector_os_arh: "x86_64" - архитектура ОС
vector_workdir: "/home/vagrant/vector" - рабочий каталог, в котором будут сохранены скачанные deb-пакеты
vector_os_user: "vector" - имя пользователя-владельца Vector в ОС
vector_os_group: "vector" - имя группы пользователя-владельца Vector в ОС
Task'и:
TASK [Vector. Create work directory] - создает рабочий каталог, в котором будут сохранены скачанные deb-пакеты, с помощью модуля ansible.builtin.file
TASK [Vector. Get Vector distributive] - скачивает архив с дистрибутивом с помощью модуля ansible.builtin.get_url
TASK [Vector. Unzip archive] - распаковывает скачанный архив с помощью модуля ansible.builtin.unarchive
TASK [Vector. Install vector binary file] - копирует исполняемый файл Vector в /usr/bin с помощью модуля ansible.builtin.copy
TASK [Vector. Check Vector installation] - проверяет, что бинарный файл Vector работает корректно, с помощью модуля ansible.builtin.command
TASK [Vector. Create Vector config vector.toml] - создает файл /etc/vector/vector.toml с конфигом Vector с помощью модуля ansible.builtin.copy
TASK [Vector. Create vector.service daemon] - создает файл юнита systemd /lib/systemd/system/vector.service с помощью модуля ansible.builtin.copy
TASK [Vector. Modify vector.service file] - редактирует файл юнита systemd /lib/systemd/system/vector.service с помощью модуля ansible.builtin.replace
TASK [Vector. Create user vector] - создает пользователя ОС с помощью модуля ansible.builtin.user
TASK [Vector. Create data_dir] - создает каталог дял данных Vector с помощью модуля ansible.builtin.file
TASK [Vector. Remove work directory] - удаляет рабочий каталог с помощью модуля ansible.builtin.file
RUNNING HANDLER [Start Vector service] - инициируется запуск хандлера Start Vector service, обновляющего конфигурацию systemd и стартующего сервис vector.service с помощью модуляansible.builtin.systemd
