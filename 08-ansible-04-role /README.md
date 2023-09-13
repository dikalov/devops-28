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
