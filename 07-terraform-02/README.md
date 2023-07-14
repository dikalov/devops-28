## Задание 1
#### Изучите проект. В файле variables.tf объявлены переменные для yandex provider. Переименуйте файл personal.auto.tfvars_example в personal.auto.tfvars. Заполните переменные (идентификаторы облака, токен доступа). Благодаря .gitignore этот файл не попадет в публичный репозиторий. Вы можете выбрать иной способ безопасно передать секретные данные в terraform. Сгенерируйте или используйте свой текущий ssh ключ. Запишите его открытую часть в переменную vms_ssh_root_key. Инициализируйте проект, выполните код. 

![image](https://github.com/dikalov/devops-28/assets/126553776/738adcae-0229-4e65-ac06-a56f094dab71)

#### Исправьте намеренное допущенные ошибки. Ответьте в чем заключается их суть? Ответьте, как в процессе обучения могут пригодиться параметры preemptible = true и core_fraction=5 в параметрах ВМ? Ответ в документации Yandex cloud.

Параметр preemptible = true устанавливается чтобы создать прерываемую ВМ. Она дешевле, хотя и имеет ряд ограничений: может быть остановлена в любой момент, может не запуститься (если в зоне доступности не хватает ресурсов), для неё не действует SLA. Но в процессе обучения на эти ограничения можно, в принципе, прищурить глаза.

core_fraction=5 - уровень производительности 5%. Он гарантирует максимальное выделение ресурсов согласно выбранной платформы в течение данной части времени. То есть в этом конкретном случае 5% процессорного времени ВМ может работать на максимальной частоте, предоставляемой выбранной платформой intel broadwell. Этот параметр тоже очень полезен в период обучения, так как в этом случае нет никакой необходимости платить лишние деньги за гарантированный уровень производительности 100%.

![image](https://github.com/dikalov/devops-28/assets/126553776/92013b58-7464-44bf-9cb5-9199f79da044)

![image](https://github.com/dikalov/devops-28/assets/126553776/b6826210-b1ab-4927-ad30-a024619043dd)

### В качестве решения приложите: скриншот ЛК Yandex Cloud с созданной ВМ, скриншот успешного подключения к консоли ВМ через ssh, ответы на вопросы.

![image](https://github.com/dikalov/devops-28/assets/126553776/bb9b9c2e-5175-4ae4-acb9-152edbfa5d57)

![image](https://github.com/dikalov/devops-28/assets/126553776/c03b22a4-1d43-45c1-ab37-ecb3677ff04a)

## Задание 2
#### Замените все "хардкод" значения для ресурсов yandex_compute_image и yandex_compute_instance на отдельные переменные. К названиям переменных ВМ добавьте в начало префикс vm_web_ . Пример: vm_web_name. Объявите нужные переменные в файле variables.tf, обязательно указывайте тип переменной. Заполните их default прежними значениями из main.tf.

![image](https://github.com/dikalov/devops-28/assets/126553776/8b82daea-0856-4509-8e44-5e11ef88506c)

![image](https://github.com/dikalov/devops-28/assets/126553776/6f5e7fc3-995b-4274-b8ad-67f0ad77244a)

#### Проверьте terraform plan (изменений быть не должно).

![image](https://github.com/dikalov/devops-28/assets/126553776/ab64e65d-9f00-4b43-853e-9dd233861cd2)

## Задание 3
#### Создайте в корне проекта файл 'vms_platform.tf' . Перенесите в него все переменные первой ВМ. Скопируйте блок ресурса и создайте с его помощью вторую ВМ(в файле main.tf): "netology-develop-platform-db" , cores = 2, memory = 2, core_fraction = 20. Объявите ее переменные с префиксом vm_db_ в том же файле('vms_platform.tf').

![image](https://github.com/dikalov/devops-28/assets/126553776/e55f4202-d979-4835-9036-0ec2b90d300d)

![image](https://github.com/dikalov/devops-28/assets/126553776/423cbafc-790e-4fd8-bf91-6d8ed0419ab2)

## Задание 4
#### Объявите в файле outputs.tf output типа map, содержащий { instance_name = external_ip } для каждой из ВМ. Примените изменения.

![image](https://github.com/dikalov/devops-28/assets/126553776/36ab8fd4-c47e-4c63-9ec5-1a3f1a6906ac)

В качестве решения приложите вывод значений ip-адресов команды terraform output

## Задание 5
#### В файле locals.tf опишите в одном local-блоке имя каждой ВМ, используйте интерполяцию ${..} с несколькими переменными по примеру из лекции. Замените переменные с именами ВМ из файла variables.tf на созданные вами local переменные. Примените изменения.

![image](https://github.com/dikalov/devops-28/assets/126553776/4a3d9191-1847-47c6-94de-93a0cbe7128f)

![image](https://github.com/dikalov/devops-28/assets/126553776/66e955e5-2e77-4abf-95e2-81c650b7fc3a)

![image](https://github.com/dikalov/devops-28/assets/126553776/38383e7f-c5e3-4909-af27-d313dea8f6e4)

![image](https://github.com/dikalov/devops-28/assets/126553776/580b504d-1e47-4148-8633-352dd78ad227)

## Задание 6
#### Вместо использования 3-х переменных ".._cores",".._memory",".._core_fraction" в блоке resources {...}, объедените их в переменные типа map с именами "vm_web_resources" и "vm_db_resources". В качестве продвинутой практики попробуйте создать одну map переменную vms_resources и уже внутри нее конфиги обеих ВМ(вложенный map). Так же поступите с блоком metadata {serial-port-enable, ssh-keys}, эта переменная должна быть общая для всех ваших ВМ. Найдите и удалите все более не используемые переменные проекта. Проверьте terraform plan (изменений быть не должно).

```
resource "yandex_compute_instance" "platform" {

 name        = "${local.web}"
  platform_id = "standard-v1"
  resources {
    cores         = var.vm_web_cores
    memory        = var.vm_web_memory
    core_fraction = var.vm_web_core_fraction
  }
```
```
resource "yandex_compute_instance" "platform_2" {

 name        = "${local.db}"
  platform_id = "standard-v1"
  resources {
    cores         = var.vm_web_cores-db
    memory        = var.vm_web_memory-db
    core_fraction = var.vm_web_core_fraction-db
  }
```



