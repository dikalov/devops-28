## Домашнее задание к занятию 4 «Работа с roles»
#### 1. Создайте в старой версии playbook файл requirements.yml и заполните его содержимым:
```
---
  - src: git@github.com:AlexeySetevoi/ansible-clickhouse.git
    scm: git
    version: "1.13"
    name: clickhouse
```
#### 2. При помощи ansible-galaxy скачайте себе эту роль.
```
ansible-galaxy install -r requirements.yml
Starting galaxy role install process
- extracting clickhouse to /root/.ansible/roles/clickhouse
- clickhouse (1.11.0) was installed successfully
```
