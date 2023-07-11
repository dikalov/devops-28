## Задание 1
### Перейдите в каталог src. Скачайте все необходимые зависимости, использованные в проекте.
![image](https://github.com/dikalov/devops-28/assets/126553776/c8221174-99de-49f7-bba1-db1d6f1d823e)

![image](https://github.com/dikalov/devops-28/assets/126553776/9f4c0a95-0309-4fc6-9791-b73224efbf5f)

### Изучите файл .gitignore. В каком terraform файле согласно этому .gitignore допустимо сохранить личную, секретную информацию?
![image](https://github.com/dikalov/devops-28/assets/126553776/ed5b6310-bdc6-4220-8578-196d7d07c761)
personal.auto.tfvars - позволяет именовать файлы с переменными, в том числе секретными.

### Выполните код проекта. Найдите в State-файле секретное содержимое созданного ресурса random_password, пришлите в качестве ответа конкретный ключ и его значение.

![image](https://github.com/dikalov/devops-28/assets/126553776/868264df-19b1-459c-9a2b-b67d99c6fa03)

![image](https://github.com/dikalov/devops-28/assets/126553776/1da05e43-7408-462a-b2d8-305631bc654f)

### Раскомментируйте блок кода, примерно расположенный на строчках 29-42 файла main.tf. Выполните команду terraform validate. Объясните в чем заключаются намеренно допущенные ошибки? Исправьте их.

![image](https://github.com/dikalov/devops-28/assets/126553776/b20cdaeb-0472-4ad1-ad8b-4898d7dbbe27)

![image](https://github.com/dikalov/devops-28/assets/126553776/ccc0bd74-0470-4ddf-81b5-830345e37853)

Error: Missing name for resource (Ошибка: Отсутствует имя для ресурса). Неверное имя ресурса, а именно 1nginx. (Имя должно начинаться с буквы или подчеркивания и может содержать только буквы, цифры, подчеркивания и тире.)

![image](https://github.com/dikalov/devops-28/assets/126553776/4ddf6f37-1c7d-45f6-b6e2-d267eea42e86)

### Выполните код. В качестве ответа приложите вывод команды docker ps
![image](https://github.com/dikalov/devops-28/assets/126553776/dd3aa5d3-7669-4f70-851a-d61f2ad923ee)

### Замените имя docker-контейнера в блоке кода на hello_world, выполните команду terraform apply -auto-approve. Объясните своими словами, в чем может быть опасность применения ключа -auto-approve ? В качестве ответа дополнительно приложите вывод команды docker ps


