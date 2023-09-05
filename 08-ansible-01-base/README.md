## Домашнее задание к занятию 1 «Введение в Ansible»
#### 1. Попробуйте запустить playbook на окружении из test.yml, зафиксируйте значение, которое имеет факт some_fact для указанного хоста при выполнении playbook.
![image](https://github.com/dikalov/devops-28/assets/126553776/4ab006ef-ea05-4bf3-b06f-6275a2bd7990)

Значение 12
#### 2. Найдите файл с переменными (group_vars), в котором задаётся найденное в первом пункте значение, и поменяйте его на all default fact.
В ```group_vars/all/examp.yml``` исправил ```some_fact: 12``` на ```some_fact: "all default fact"```

![image](https://github.com/dikalov/devops-28/assets/126553776/d73afcf5-a453-4de0-9081-47c635a89b74)

#### 3. Воспользуйтесь подготовленным (используется docker) или создайте собственное окружение для проведения дальнейших испытаний.
```
version: '3'
services:
  centos7:
    image: pycontribs/centos:7
    container_name: centos7
    restart: unless-stopped
    entrypoint: "sleep infinity"

  ubuntu:
    image: pycontribs/ubuntu
    container_name: ubuntu
    restart: unless-stopped
    entrypoint: "sleep infinity"
```
