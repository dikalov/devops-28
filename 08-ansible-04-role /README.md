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
vagrant@server1:~/an-home/playbook$ ansible-galaxy install -r requirements.yml
Starting galaxy role install process
- extracting clickhouse to /root/.ansible/roles/clickhouse
- clickhouse (1.11.0) was installed successfully
```
#### 3. Создайте новый каталог с ролью при помощи ansible-galaxy role init vector-role.
#### 4. На основе tasks из старого playbook заполните новую role. Разнесите переменные между vars и default.
#### 5. Перенести нужные шаблоны конфигов в templates.
#### 6. Опишите в README.md обе роли и их параметры. Пример качественной документации ansible role по ссылке.
#### 7. Повторите шаги 3–6 для LightHouse. Помните, что одна роль должна настраивать один продукт.
#### 8. Выложите все roles в репозитории. Проставьте теги, используя семантическую нумерацию. Добавьте roles в requirements.yml в playbook.
```
vagrant@server1:~/an-home/playbook$ cat requirements.yml
---
- name: clickhouse
  src: git@github.com:AlexeySetevoi/ansible-clickhouse.git
  scm: git
  version: "1.11.0"

- name: vector
  src: git@github.com:Firewal7/vector-role.git
  scm: git
  version: "1.0.1"

- name: lighthouse
  src: git@github.com:Firewal7/lighthouse-role.git
  scm: git
  version: "1.1.1"
```
#### 9. Переработайте playbook на использование roles. Не забудьте про зависимости LightHouse и возможности совмещения roles с tasks.
```
---
- name: Install Clickhouse
  hosts: clickhouse
  roles:
    - clickhouse

- name: Install Vector
  hosts: vector
  become: true
  roles:
    - vector

- name: Install lighthouse and Nginx
  hosts: lighthouse

  pre_tasks:
    - name: Lighthouse | Install git
      become: true
      ansible.builtin.yum:
        name: git
        state: present
  roles:
    - lighthouse
```
#### 10. Выложите playbook в репозиторий.

#### 11. В ответе дайте ссылки на оба репозитория с roles и одну ссылку на репозиторий с playbook.
