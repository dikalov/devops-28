## Задание 1
#### Изучите проект. Заполните файл personal.auto.tfvars Инициализируйте проект, выполните код (он выполнится даже если доступа к preview нет). Приложите скриншот входящих правил "Группы безопасности" в ЛК Yandex Cloud или скриншот отказа в предоставлении доступа к preview версии.

![image](https://github.com/dikalov/devops-28/assets/126553776/327de881-5e81-4519-922a-44d749c6aa16)

## Задание 2
#### Создайте файл count-vm.tf. Опишите в нем создание двух одинаковых ВМ web-1 и web-2(не web-0 и web-1!), с минимальными параметрами, используя мета-аргумент count loop. Назначьте ВМ созданную в 1-м задании группу безопасности.

![image](https://github.com/dikalov/devops-28/assets/126553776/e9eb8597-3e80-48e5-8a0d-b993085a31ed)

![image](https://github.com/dikalov/devops-28/assets/126553776/c8d4d363-bf74-4288-b03c-8c4a8f32eebc)

#### Создайте файл for_each-vm.tf. Опишите в нем создание 2 ВМ с именами "main" и "replica" разных по cpu/ram/disk , используя мета-аргумент for_each loop. Используйте для обеих ВМ одну, общую переменную типа list(object({ vm_name=string, cpu=number, ram=number, disk=number })). При желании внесите в переменную все возможные параметры. ВМ из пункта 2.2 должны создаваться после создания ВМ из пункта 2.1. Инициализируйте проект, выполните код.

```
resource "yandex_compute_instance" "fe_instance" {

depends_on = [ yandex_compute_instance.web ]

  for_each = { for vm in local.vms_fe: "${vm.vm_name}" => vm }
  name = each.key
  platform_id = "standard-v1"
  resources {
    cores          = each.value.cpu
    memory         = each.value.ram
    core_fraction  = each.value.frac
  }
```

![image](https://github.com/dikalov/devops-28/assets/126553776/aca1dbbb-b863-4736-9e32-cb1a6613a4a2)

## Задание 3
#### Создайте 3 одинаковых виртуальных диска, размером 1 Гб с помощью ресурса yandex_compute_disk и мета-аргумента count в файле disk_vm.tf . Создайте в том же файле одну ВМ c именем "storage" . Используйте блок dynamic secondary_disk{..} и мета-аргумент for_each для подключения созданных вами дополнительных дисков.

```
resource "yandex_compute_disk" "stor" {
  count   = 3
  name    = "disk-${count.index + 1}"
  size    = 1
}
```

![снимок](https://github.com/dikalov/devops-28/assets/126553776/839f751c-9074-4145-9f01-5ce2c276a20f)

## Задание 4
#### В файле ansible.tf создайте inventory-файл для ansible. Используйте функцию tepmplatefile и файл-шаблон для создания ansible inventory-файла из лекции. Готовый код возьмите из демонстрации к лекции demonstration2. Передайте в него в качестве переменных группы виртуальных машин из задания 2.1, 2.2 и 3.2.(т.е. 5 ВМ)


