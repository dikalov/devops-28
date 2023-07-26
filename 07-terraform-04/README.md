# Домашнее задание к занятию "Продвинутые методы работы с Terraform"
## Задание 1
#### Возьмите из демонстрации к лекции готовый код для создания ВМ с помощью remote модуля. Создайте 1 ВМ, используя данный модуль. В файле cloud-init.yml необходимо использовать переменную для ssh ключа вместо хардкода. Передайте ssh-ключ в функцию template_file в блоке vars ={} . Воспользуйтесь примером. Обратите внимание что ssh-authorized-keys принимает в себя список, а не строку! Добавьте в файл cloud-init.yml установку nginx. Предоставьте скриншот подключения к консоли и вывод команды sudo nginx -t.

![image](https://github.com/dikalov/devops-28/assets/126553776/c407f379-480f-4bc1-9be0-882f819ee301)

![image](https://github.com/dikalov/devops-28/assets/126553776/3cd29dad-8a12-4df8-810d-b3de8566e1d9)


## Задание 2
#### Напишите локальный модуль vpc, который будет создавать 2 ресурса: одну сеть и одну подсеть в зоне, объявленной при вызове модуля. например: ru-central1-a. Вы должны передать в модуль переменные с названием сети, zone и v4_cidr_blocks . Модуль должен возвращать в виде output информацию о yandex_vpc_subnet Замените ресурсы yandex_vpc_network и yandex_vpc_subnet, созданным модулем. Не забудьте передать необходимые параметры сети из модуля vpc в модуль с виртуальной машиной. Откройте terraform console и предоставьте скриншот содержимого модуля. Пример: > module.vpc_dev. Сгенерируйте документацию к модулю с помощью terraform-docs.

[Локальный модуль vpc](https://github.com/dikalov/devops-28/tree/main/07-terraform-04-project/vps)

[Файл main.tf](https://github.com/dikalov/devops-28/blob/main/07-terraform-04-project/main.tf)

## Задание 3
#### Выведите список ресурсов в стейте.
```
vagrant@server1:~/ter-homeworks/04/demonstration1$ terraform state list
data.template_file.cloudinit
module.test-vm.data.yandex_compute_image.my_image
module.test-vm.yandex_compute_instance.vm[0]
module.vpc_dev.yandex_vpc_network.develop
module.vpc_dev.yandex_vpc_subnet.develop
```
#### Полностью удалите из стейта модуль vpc.
```
vagrant@server1:~/ter-homeworks/04/demonstration1$ terraform state rm "module.vpc_dev.yandex_vpc_network.develop"
Removed module.vpc_dev.yandex_vpc_network.develop
Successfully removed 1 resource instance(s).
vagrant@server1:~/ter-homeworks/04/demonstration1$ terraform state rm "module.vpc_dev.yandex_vpc_subnet.develop" 
Removed module.vpc_dev.yandex_vpc_subnet.develop
Successfully removed 1 resource instance(s).
```

#### Импортируйте все обратно. Проверьте terraform plan - изменений быть не должно. Приложите список выполненных команд и скриншоты процессы.
```
vagrant@server1:~/ter-homeworks/04/demonstration1$ terraform import "module.vpc_dev.yandex_vpc_network.develop" enpno4bepjcvji48uj1j      
data.template_file.cloudinit: Reading...
data.template_file.cloudinit: Read complete after 0s [id=*****]
module.vpc_dev.yandex_vpc_network.develop: Importing from ID "enpno4bepjcvji48uj1j"...
module.test-vm.data.yandex_compute_image.my_image: Reading...
module.vpc_dev.yandex_vpc_network.develop: Import prepared!
  Prepared yandex_vpc_network for import
module.vpc_dev.yandex_vpc_network.develop: Refreshing state... [id=enpno4bepjcvji48uj1j]
module.test-vm.data.yandex_compute_image.my_image: Read complete after 0s [id=fd852pbtueis1q0pbt4o]

Import successful!

The resources that were imported are shown above. These resources are now in
your Terraform state and will henceforth be managed by Terraform.

vagrant@server1:~/ter-homeworks/04/demonstration1$ terraform import "module.vpc_dev.yandex_vpc_subnet.develop" e9beha0h8tdn06m8lju4       
data.template_file.cloudinit: Reading...
data.template_file.cloudinit: Read complete after 0s [id=*****]
module.test-vm.data.yandex_compute_image.my_image: Reading...
module.vpc_dev.yandex_vpc_subnet.develop: Importing from ID "e9beha0h8tdn06m8lju4"...
module.vpc_dev.yandex_vpc_subnet.develop: Import prepared!
  Prepared yandex_vpc_subnet for import
module.vpc_dev.yandex_vpc_subnet.develop: Refreshing state... [id=e9beha0h8tdn06m8lju4]
module.test-vm.data.yandex_compute_image.my_image: Read complete after 1s [id=fd852pbtueis1q0pbt4o]

Import successful!

The resources that were imported are shown above. These resources are now in
your Terraform state and will henceforth be managed by Terraform.
PS 
vagrant@server1:~/ter-homeworks/04/demonstration1$ terraform plan
data.template_file.cloudinit: Reading...
data.template_file.cloudinit: Read complete after 0s [id=*****]
module.test-vm.data.yandex_compute_image.my_image: Reading...
module.vpc_dev.yandex_vpc_network.develop: Refreshing state... [id=enpno4bepjcvji48uj1j]
module.test-vm.data.yandex_compute_image.my_image: Read complete after 0s [id=fd852pbtueis1q0pbt4o]
module.vpc_dev.yandex_vpc_subnet.develop: Refreshing state... [id=e9beha0h8tdn06m8lju4]
module.test-vm.yandex_compute_instance.vm[0]: Refreshing state... [id=fhmvde3847dq3rd6rj4k]

No changes. Your infrastructure matches the configuration.

Terraform has compared your real infrastructure against your configuration and found no differences, so no changes are needed.
