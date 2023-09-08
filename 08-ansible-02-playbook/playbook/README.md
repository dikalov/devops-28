### Данный playbook:

Устанавливает clichouse и vector на две виртуальные машины Centos7 docker, собранные с помощью docker-compose файла, запускает службу clichouse-server и vector, а также создает базу logs в clichouse.

### Для работы playbook:

В каталоге group_vars задаются необходимые версии дистрибутивов.

Запустить из docker-compose.yml файла две виртуальные машины ```docker-compose up; docker ps```

Запустить ansible-playbook ```ansible-playbook -i inventory/prod.yml site.yml```
