## Задание 1
### Используя Docker, поднимите инстанс MySQL (версию 8). Данные БД сохраните в volume.

Подготавливаю образ(docker pull mysql:8.0), создаю volume, запускаю контейнер

![image](https://github.com/dikalov/devops-28/assets/126553776/75ad60e6-8245-4eed-9659-b4239635abf6)

![image](https://github.com/dikalov/devops-28/assets/126553776/04af17bb-1b90-444b-97af-208d017c9770)

Используя команду \h, получите список управляющих команд.

Найдите команду для выдачи статуса БД и приведите в ответе из её вывода версию сервера БД.

![image](https://github.com/dikalov/devops-28/assets/126553776/ce462394-416e-49bf-b7bd-b4288a86d33d)

Создаю базу и востанвливаю из дампа

![image](https://github.com/dikalov/devops-28/assets/126553776/2e538ae7-4ce3-4c0f-b08f-ec74a7b4d42d)

Копирую дамп

![image](https://github.com/dikalov/devops-28/assets/126553776/5c77eedd-3736-4e2c-8450-7e8766309d06)

