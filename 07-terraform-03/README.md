## Задание 1
#### Изучите проект. Заполните файл personal.auto.tfvars Инициализируйте проект, выполните код (он выполнится даже если доступа к preview нет). Приложите скриншот входящих правил "Группы безопасности" в ЛК Yandex Cloud или скриншот отказа в предоставлении доступа к preview версии.

![image](https://github.com/dikalov/devops-28/assets/126553776/327de881-5e81-4519-922a-44d749c6aa16)

## Задание 2
#### Создайте файл count-vm.tf. Опишите в нем создание двух одинаковых ВМ web-1 и web-2(не web-0 и web-1!), с минимальными параметрами, используя мета-аргумент count loop. Назначьте ВМ созданную в 1-м задании группу безопасности.

![image](https://github.com/dikalov/devops-28/assets/126553776/e9eb8597-3e80-48e5-8a0d-b993085a31ed)

![image](https://github.com/dikalov/devops-28/assets/126553776/381b393d-fb71-4e4a-bbc2-74b6ea24183a)

#### Создайте файл for_each-vm.tf. Опишите в нем создание 2 ВМ с именами "main" и "replica" разных по cpu/ram/disk , используя мета-аргумент for_each loop. Используйте для обеих ВМ одну, общую переменную типа list(object({ vm_name=string, cpu=number, ram=number, disk=number })). При желании внесите в переменную все возможные параметры. ВМ из пункта 2.2 должны создаваться после создания ВМ из пункта 2.1. Инициализируйте проект, выполните код.

![image](https://github.com/dikalov/devops-28/assets/126553776/e34cd07a-8de6-4d24-adfe-e18bdc6ceed2)

![image](https://github.com/dikalov/devops-28/assets/126553776/b596d497-51b7-44dd-917b-884393f19c85)

![image](https://github.com/dikalov/devops-28/assets/126553776/3664abc2-3ad7-44ba-9d8f-9810e08c90ad)

## Задание 3
#### Создайте 3 одинаковых виртуальных диска, размером 1 Гб с помощью ресурса yandex_compute_disk и мета-аргумента count в файле disk_vm.tf . Создайте в том же файле одну ВМ c именем "storage" . Используйте блок dynamic secondary_disk{..} и мета-аргумент for_each для подключения созданных вами дополнительных дисков.

```
resource "yandex_compute_disk" "default_disk" {
  count      = 3
  name       = "default-disk-${count.index + 1}"
  type       = "network-hdd"
  zone       = var.default_zone
  size       = 5
  block_size = 4096
}

resource "yandex_compute_instance" "storage_server" {

  depends_on = [yandex_compute_disk.default_disk]

  name        = "storage"
  platform_id = "standard-v1"

  resources {
    cores         = var.vm_base.cores
    memory        = var.vm_base.memory
    core_fraction = var.vm_base.core_fraction
  }

  boot_disk {
    initialize_params {
      image_id = data.yandex_compute_image.ubuntu.image_id
    }
  }

  dynamic "secondary_disk" {
    for_each = toset(yandex_compute_disk.default_disk[*].id)
    content {
      disk_id     = secondary_disk.key
      auto_delete = true
    }
  }

  metadata = local.ssh_keys_and_serial_port

  scheduling_policy {
    preemptible = true
  }

  network_interface {
    subnet_id = yandex_vpc_subnet.develop.id
    nat       = true
  }

  allow_stopping_for_update = true

}

```

![image](https://github.com/dikalov/devops-28/assets/126553776/fab63f76-4479-49bd-a108-830487e1e91e)

## Задание 4
#### В файле ansible.tf создайте inventory-файл для ansible. Используйте функцию tepmplatefile и файл-шаблон для создания ansible inventory-файла из лекции. Готовый код возьмите из демонстрации к лекции demonstration2. Передайте в него в качестве переменных группы виртуальных машин из задания 2.1, 2.2 и 3.2.(т.е. 5 ВМ)

![image](https://github.com/dikalov/devops-28/assets/126553776/babefa2b-983a-47cd-8f46-c315036048dc)

![image](https://github.com/dikalov/devops-28/assets/126553776/723a0804-c622-4a8c-a712-73f3eee5d630)

#### Инвентарь должен содержать 3 группы [webservers], [databases], [storage] и быть динамическим, т.е. обработать как группу из 2-х ВМ так и 999 ВМ. Выполните код. Приложите скриншот получившегося файла.

![image](https://github.com/dikalov/devops-28/assets/126553776/c5245818-e681-419f-bfbd-1dc4298e4457)

#### Для общего зачета создайте в вашем GitHub репозитории новую ветку terraform-03. Закомитьте в эту ветку свой финальный код проекта, пришлите ссылку на коммит.

[Ссылка на проект](https://github.com/dikalov/devops-28/tree/main/07-terraform-03-project)
