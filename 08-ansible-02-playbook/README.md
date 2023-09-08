## Домашнее задание к занятию 2 «Работа с Playbook»
#### 1. Подготовьте свой inventory-файл prod.yml.
```
---
clickhouse:
  hosts:
    clickhouse-01:
      ansible_connection: docker
vector:
  hosts:
    vector-01:
      ansible_connection: docker
```
#### 2. Допишите playbook: нужно сделать ещё один play, который устанавливает и настраивает vector. Конфигурация vector должна деплоиться через template файл jinja2.
```
---
- name: Install Clickhouse
  hosts: clickhouse
  handlers:
    - name: Start clickhouse service
      become: true
      ansible.builtin.service:
        name: clickhouse-server
        state: restarted
  tasks:
    - name: Mess with clickhouse distrib
      block:
        - name: Get clickhouse distrib
          ansible.builtin.get_url:
            url: "https://packages.clickhouse.com/rpm/stable/{{ item }}-{{ clickhouse_version }}.noarch.rpm"
            dest: "./{{ item }}-{{ clickhouse_version }}.rpm"
            mode: "0644"
          with_items: "{{ clickhouse_packages }}"
      rescue:
        - name: Get clickhouse distrib (rescue)
          ansible.builtin.get_url:
            url: "https://packages.clickhouse.com/rpm/stable/clickhouse-common-static-{{ clickhouse_version }}.x86_64.rpm"
            dest: "./clickhouse-common-static-{{ clickhouse_version }}.rpm"
            mode: "0644"
    - name: Install clickhouse packages
      become: true
      ansible.builtin.yum:
        name:
          - clickhouse-common-static-{{ clickhouse_version }}.rpm
          - clickhouse-client-{{ clickhouse_version }}.rpm
          - clickhouse-server-{{ clickhouse_version }}.rpm
      notify: Start clickhouse service
    - name: Flush handlers to restart clickhouse
      ansible.builtin.meta: flush_handlers
    - name: Create database
      ansible.builtin.command: "clickhouse-client -q 'create database logs;'"
      become: true
      register: create_db
      failed_when: create_db.rc != 0 and create_db.rc != 82
      changed_when: create_db.rc == 0

- name: Install vector
  hosts: clickhouse
  handlers:
    - name: Start vector service
      become: true
      ansible.builtin.service:
        name: vector
        state: restarted
  tasks:
    - name: Get vector distrib
      ansible.builtin.get_url:
        url: "https://packages.timber.io/vector/{{ vector_version }}/vector-{{ vector_version }}-1.x86_64.rpm"
        dest: "./vector-{{ vector_version }}.rpm"
        mode: "0644"
      notify: Start vector service
    - name: Install vector packages
      become: true
      ansible.builtin.yum:
        name:
          - vector-{{ vector_version }}.rpm
    - name: Flush handlers to restart vector
      ansible.builtin.meta: flush_handlers
```
#### 3. При создании tasks рекомендую использовать модули: get_url, template, unarchive, file.

#### 4. Tasks должны: скачать дистрибутив нужной версии, выполнить распаковку в выбранную директорию, установить vector.

#### 5. Запустите ansible-lint site.yml и исправьте ошибки, если они есть.
Ошибки были исправлены (лишние пробелы).

#### 6. Попробуйте запустить playbook на этом окружении с флагом --check.
```
vagrant@server1:~/an-home/playbook$ ansible-playbook site.yml -i inventory/prod.yml --check
PLAY [Install Vector] ***********************************************************************************************************************************************************************************************************************
TASK [Gathering Facts] **********************************************************************************************************************************************************************************************************************
ok: [vector-01]
TASK [Get Vector distrib] *******************************************************************************************************************************************************************************************************************
ok: [vector-01]
TASK [Install Vector packages] **************************************************************************************************************************************************************************************************************
fatal: [vector-01]: FAILED! => {"changed": false, "module_stderr": "/bin/sh: sudo: command not found\n", "module_stdout": "", "msg": "MODULE FAILURE\nSee stdout/stderr for the exact error", "rc": 127}
PLAY RECAP **********************************************************************************************************************************************************************************************************************************
vector-01                  : ok=2    changed=0    unreachable=0    failed=1    skipped=0    rescued=0    ignored=0
```

#### 7. Запустите playbook на prod.yml окружении с флагом --diff. Убедитесь, что изменения на системе произведены.
```
vagrant@server1:~/an-home/playbook$ ansible-playbook site.yml -i inventory/prod.yml --diff
PLAY [Install Clickhouse] **********************************************************************************************************************************************************************************

TASK [Gathering Facts] *************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Install tar] *****************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Get vector distrib] **********************************************************************************************************************************************************************************
changed: [clickhouse-01]

TASK [Creates directory /src/vector/] **********************************************************************************************************************************************************************
--- before
+++ after
@@ -1,6 +1,6 @@
 {
-    "group": 0,
-    "owner": 0,
+    "group": 1000,
+    "owner": 1000,
     "path": "/src/vector/",
-    "state": "absent"
+    "state": "directory"
 }

changed: [clickhouse-01]

TASK [CP] **************************************************************************************************************************************************************************************************
changed: [clickhouse-01]

TASK [GUNZP] ***********************************************************************************************************************************************************************************************
changed: [clickhouse-01]

TASK [UNZIP] ***********************************************************************************************************************************************************************************************
changed: [clickhouse-01]

TASK [Set environment vector] ******************************************************************************************************************************************************************************
--- before
+++ after: /home/panmonster/.ansible/tmp/ansible-local-22149x1zmqjpl/tmpitj7xst9/vector.sh.j2
@@ -0,0 +1,5 @@
+# Warning: This file is Ansible Managed, manual changes will be overwritten on next playbook run.
+#!/usr/bin/env bash
+
+export VECTOR_HOME=/src/vector
+export PATH=$PATH:$VECTOR_HOME/bin

changed: [clickhouse-01]
```
#### 8. Повторно запустите playbook с флагом --diff и убедитесь, что playbook идемпотентен.
```
vagrant@server1:~/an-home/playbook$ ansible-playbook site.yml -i inventory/prod.yml --diff

PLAY [Install Clickhouse] **********************************************************************************************************************************************************************************

TASK [Gathering Facts] *************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Install tar] *****************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Get vector distrib] **********************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Creates directory /src/vector/] **********************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [CP] **************************************************************************************************************************************************************************************************
changed: [clickhouse-01]

TASK [GUNZP] ***********************************************************************************************************************************************************************************************
changed: [clickhouse-01]

TASK [UNZIP] ***********************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Set environment vector] ******************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Get clickhouse distrib] ******************************************************************************************************************************************************************************
ok: [clickhouse-01] => (item=clickhouse-client)
ok: [clickhouse-01] => (item=clickhouse-server)
failed: [clickhouse-01] (item=clickhouse-common-static) => {"ansible_loop_var": "item", "changed": false, "dest": "./clickhouse-common-static-22.3.3.44.rpm", "elapsed": 0, "gid": 1000, "group": "vagrant", "item": "clickhouse-common-static", "mode": "0664", "msg": "Request failed", "owner": "vagrant", "response": "HTTP Error 404: Not Found", "size": 246310036, "state": "file", "status_code": 404, "uid": 1000, "url": "https://packages.clickhouse.com/rpm/stable/clickhouse-common-static-22.3.3.44.noarch.rpm"}

TASK [Get clickhouse distrib] ******************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Install clickhouse packages] *************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Flash handlers] **************************************************************************************************************************************************************************************

TASK [Pause for 10 second for start servises] **************************************************************************************************************************************************************
Pausing for 10 seconds
(ctrl+C then 'C' = continue early, ctrl+C then 'A' = abort)
ok: [clickhouse-01]

TASK [Create database] *************************************************************************************************************************************************************************************
ok: [clickhouse-01]

PLAY RECAP *************************************************************************************************************************************************************************************************
clickhouse-01              : ok=12   changed=2    unreachable=0    failed=0    skipped=0    rescued=1    ignored=0   


TASK [Get clickhouse distrib] ******************************************************************************************************************************************************************************
changed: [clickhouse-01] => (item=clickhouse-client)
changed: [clickhouse-01] => (item=clickhouse-server)
failed: [clickhouse-01] (item=clickhouse-common-static) => {"ansible_loop_var": "item", "changed": false, "dest": "./clickhouse-common-static-22.3.3.44.rpm", "elapsed": 0, "item": "clickhouse-common-static", "msg": "Request failed", "response": "HTTP Error 404: Not Found", "status_code": 404, "url": "https://packages.clickhouse.com/rpm/stable/clickhouse-common-static-22.3.3.44.noarch.rpm"}

TASK [Get clickhouse distrib] ******************************************************************************************************************************************************************************
changed: [clickhouse-01]

TASK [Install clickhouse packages] *************************************************************************************************************************************************************************
changed: [clickhouse-01]

TASK [Flash handlers] **************************************************************************************************************************************************************************************

RUNNING HANDLER [Start clickhouse service] *****************************************************************************************************************************************************************
changed: [clickhouse-01]

TASK [Pause for 10 second for start servises] **************************************************************************************************************************************************************
Pausing for 10 seconds
(ctrl+C then 'C' = continue early, ctrl+C then 'A' = abort)
ok: [clickhouse-01]

TASK [Create database] *************************************************************************************************************************************************************************************
changed: [clickhouse-01]

PLAY RECAP *************************************************************************************************************************************************************************************************
clickhouse-01              : ok=13   changed=10   unreachable=0    failed=0    skipped=0    rescued=1    ignored=0
```
#### 9. Подготовьте README.md-файл по своему playbook. В нём должно быть описано: что делает playbook, какие у него есть параметры и теги.
[Ссылка на README к playbook](https://github.com/dikalov/devops-28/blob/main/08-ansible-02-playbook/playbook/README.md)

#### 10. Готовый playbook выложите в свой [репозиторий](https://github.com/dikalov/devops-28/tree/main/08-ansible-02-playbook/playbook)
