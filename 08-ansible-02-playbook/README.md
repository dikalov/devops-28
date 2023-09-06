## Домашнее задание к занятию 2 «Работа с Playbook»
#### 1. Подготовьте свой inventory-файл prod.yml.
```
---
  elasticsearch:
    hosts:
      elastic:
        ansible_connection: docker
  kibana:
    hosts:
      kibana:
        ansible_connection: docker
```
#### 2. Допишите playbook: нужно сделать ещё один play, который устанавливает и настраивает vector. Конфигурация vector должна деплоиться через template файл jinja2.
