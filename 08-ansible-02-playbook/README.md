## Домашнее задание к занятию 2 «Работа с Playbook»
#### 1. Подготовьте свой inventory-файл prod.yml.
```
---
clickhouse:
  hosts:
    clickhouse-01:
      ansible_host: 192.168.0.10
      ansible_ssh_user: vagrant
      ansible_ssh_pass: vagrant
      
vector:
  hosts:
    clickhouse-02:
      ansible_host: 192.168.0.12
      ansible_ssh_user: vagrant
      ansible_ssh_pass: vagrant
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
    - name: Install tar
      become: true
      ansible.builtin.yum:
        name: zip
    - name: Get vector distrib
      ansible.builtin.get_url:
        url: "https://packages.timber.io/vector/0.21.1/{{ vector_version }}.tar.gz"
        dest: "./{{ vector_version }}.tar.gz"
        mode: 0755
    - name: Creates directory /src/vector/
      become: true
      ansible.builtin.file:
        path: /src/vector/
        state: directory
        owner: vagrant
        group: vagrant
        mode: 0755
    - name: CP
      become: true
      ansible.builtin.copy:
        src: "./{{ vector_version }}.tar.gz"
        dest: /src/vector/{{ vector_version }}.tar.gz
        mode: 0755
        remote_src: yes
    - name: GUNZP
      ansible.builtin.shell: gunzip -f /src/vector/{{ vector_version }}.tar.gz
    - name: UNZIP
      become: true
      ansible.builtin.unarchive:      
        src: /src/vector/{{ vector_version }}.tar
        dest: /src/vector/
        extra_opts: [--strip-components=2]
        mode: 0755
        remote_src: yes
      ignore_errors: "{{ ansible_check_mode }}"        
    - name: Set environment vector
      become: true
      ansible.builtin.template:
        src: templates/vector.sh.j2
        dest: /etc/profile.d/vector.sh
        mode: 0755
    - block:
        - name: Get clickhouse distrib
          ansible.builtin.get_url:
            url: "https://packages.clickhouse.com/rpm/stable/{{ item }}-{{ clickhouse_version }}.noarch.rpm"
            dest: "./{{ item }}-{{ clickhouse_version }}.rpm"
          with_items: "{{ clickhouse_packages }}"
      rescue:
        - name: Get clickhouse distrib
          ansible.builtin.get_url:
            url: "https://packages.clickhouse.com/rpm/stable/clickhouse-common-static-{{ clickhouse_version }}.x86_64.rpm"
            dest: "./clickhouse-common-static-{{ clickhouse_version }}.rpm"
    - name: Install clickhouse packages
      become: true
      ansible.builtin.yum:
        name:
          - clickhouse-common-static-{{ clickhouse_version }}.rpm
          - clickhouse-client-{{ clickhouse_version }}.rpm
          - clickhouse-server-{{ clickhouse_version }}.rpm
      notify: Start clickhouse service
    - name: Flash handlers
      ansible.builtin.meta: flush_handlers
    - name: Pause for 10 second for start servises
      pause:
        seconds: 10
    - name: Create database
      ansible.builtin.command: "clickhouse-client -q 'create database logs;'"
      register: create_db
      failed_when: create_db.rc != 0 and create_db.rc !=82
      changed_when: create_db.rc == 0
```
#### 3. При создании tasks рекомендую использовать модули: get_url, template, unarchive, file.

#### 4. Tasks должны: скачать дистрибутив нужной версии, выполнить распаковку в выбранную директорию, установить vector.

#### 5. Запустите ansible-lint site.yml и исправьте ошибки, если они есть.
Ошибки были исправлены (лишние пробелы).

#### 6. Попробуйте запустить playbook на этом окружении с флагом --check.
```
vagrant@server1:~/an-home/playbook$ ansible-playbook site.yml -i inventory/prod.yml --check
PLAY [Install Clickhouse] ******************************************************

TASK [Gathering Facts] *********************************************************
ok: [clickhouse-01]

TASK [Install tar] *************************************************************
ok: [clickhouse-01]

TASK [Get vector distrib] ******************************************************
ok: [clickhouse-01]

TASK [Creates directory /src/vector/] ******************************************
ok: [clickhouse-01]

TASK [CP] **********************************************************************
changed: [clickhouse-01]

TASK [GUNZP] *******************************************************************
skipping: [clickhouse-01]

TASK [UNZIP] *******************************************************************
skipping: [clickhouse-01]

TASK [Set environment vector] **************************************************
ok: [clickhouse-01]

TASK [Get clickhouse distrib] **************************************************
ok: [clickhouse-01] => (item=clickhouse-client)
ok: [clickhouse-01] => (item=clickhouse-server)
failed: [clickhouse-01] (item=clickhouse-common-static) => {"ansible_loop_var": "item", "changed": false, "dest": "./clickhouse-common-static-22.3.3.44.rpm", "elapsed": 0, "gid": 1000, "group": "vagrant", "item": "clickhouse-common-static", "mode": "0664", "msg": "Request failed", "owner": "vagrant", "response": "HTTP Error 404: Not Found", "size": 246310036, "state": "file", "status_code": 404, "uid": 1000, "url": "https://packages.clickhouse.com/rpm/stable/clickhouse-common-static-22.3.3.44.noarch.rpm"}

TASK [Get clickhouse distrib] **************************************************
ok: [clickhouse-01]

TASK [Install clickhouse packages] *********************************************
ok: [clickhouse-01]

TASK [Flash handlers] **********************************************************

TASK [Pause for 10 second for start servises] **********************************
Pausing for 10 seconds
(ctrl+C then 'C' = continue early, ctrl+C then 'A' = abort)
ok: [clickhouse-01]

TASK [Create database] *********************************************************
skipping: [clickhouse-01]

PLAY RECAP *********************************************************************
clickhouse-01              : ok=9    changed=1    unreachable=0    failed=0    skipped=3    rescued=1    ignored=0 
```

#### 7. Запустите playbook на prod.yml окружении с флагом --diff. Убедитесь, что изменения на системе произведены.

vagrant@server1:~/an-home/playbook$ ansible-playbook site.yml -i inventory/prod.yml --diff
PLAY [Install Clickhouse] *******************************************************************************************************************************************************************************************************************
TASK [Gathering Facts] **********************************************************************************************************************************************************************************************************************
ok: [clickhouse-01]
TASK [Get clickhouse distrib] ***************************************************************************************************************************************************************************************************************
ok: [clickhouse-01] => (item=clickhouse-client)
ok: [clickhouse-01] => (item=clickhouse-server)
failed: [clickhouse-01] (item=clickhouse-common-static) => {"ansible_loop_var": "item", "changed": false, "dest": "./clickhouse-common-static-22.3.3.44.rpm", "elapsed": 0, "gid": 0, "group": "root", "item": "clickhouse-common-static", "mode": "0644", "msg": "Request failed", "owner": "root", "response": "HTTP Error 404: Not Found", "size": 246310036, "state": "file", "status_code": 404, "uid": 0, "url": "https://packages.clickhouse.com/rpm/stable/clickhouse-common-static-22.3.3.44.noarch.rpm"}
TASK [Get clickhouse distrib] ***************************************************************************************************************************************************************************************************************
ok: [clickhouse-01]
TASK [Install clickhouse packages] **********************************************************************************************************************************************************************************************************
ok: [clickhouse-01]
TASK [Create database] **********************************************************************************************************************************************************************************************************************
ok: [clickhouse-01]
PLAY [Install vector] ***********************************************************************************************************************************************************************************************************************
TASK [Gathering Facts] **********************************************************************************************************************************************************************************************************************
ok: [vector-01]
TASK [Get vector distrib] *******************************************************************************************************************************************************************************************************************
ok: [vector-01]
TASK [Install vector packages] **************************************************************************************************************************************************************************************************************
ok: [vector-01]
PLAY RECAP **********************************************************************************************************************************************************************************************************************************
clickhouse-01              : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=1    ignored=0
vector-01                  : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
