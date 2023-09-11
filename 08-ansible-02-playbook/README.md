## Домашнее задание к занятию 2 «Работа с Playbook»
#### 1. Подготовьте свой inventory-файл prod.yml.
```
---
clickhouse:
  hosts:
    clickhouse-01:
      ansible_host: "192.168.10.10"
```
#### 2. Допишите playbook: нужно сделать ещё один play, который устанавливает и настраивает vector. Конфигурация vector должна деплоиться через template файл jinja2.
```
---
- name: Install Clickhouse & Vector
  hosts: clickhouse
  gather_facts: false

  handlers:
    - name: Start clickhouse service
      become: true
      ansible.builtin.service:
        name: clickhouse-server
        state: restarted

    - name: Start Vector service
      become: true
      ansible.builtin.systemd:
        daemon_reload: true
        enabled: false
        name: vector.service
        state: started

  tasks:
    - block:
        - block:
            - name: Clickhouse. Get clickhouse distrib
              ansible.builtin.get_url:
                url: "https://packages.clickhouse.com/deb/pool/stable/{{ item }}_{{ clickhouse_version }}_all.deb"
                dest: "./{{ item }}_{{ clickhouse_version }}_all.deb"
                mode: 0644
              with_items: "{{ clickhouse_packages }}"
          rescue:
            - name: Clickhouse. Get clickhouse distrib
              ansible.builtin.get_url:
                url: "https://packages.clickhouse.com/deb/pool/stable/clickhouse-common-static_{{ clickhouse_version }}_amd64.deb"
                dest: "./clickhouse-common-static_{{ clickhouse_version }}_amd64.deb"
                mode: 0644
              with_items: "{{ clickhouse_packages }}"

        - name: Clickhouse. Install package clickhouse-common-static
          become: true
          ansible.builtin.apt:
            deb: ./clickhouse-common-static_{{ clickhouse_version }}_amd64.deb
          notify: Start clickhouse service

        - name: Clickhouse. Install package clickhouse-client
          become: true
          ansible.builtin.apt:
            deb: ./clickhouse-client_{{ clickhouse_version }}_all.deb
          notify: Start clickhouse service

        - name: Clickhouse. Install clickhouse package clickhouse-server
          become: true
          ansible.builtin.apt:
            deb: ./clickhouse-server_{{ clickhouse_version }}_all.deb
          notify: Start clickhouse service

        - name: Clickhouse. Flush handlers
          ansible.builtin.meta: flush_handlers

        - name: Clickhouse. Waiting while clickhouse-server is available...
          ansible.builtin.pause:
            seconds: 10
            echo: false

        - name: Clickhouse. Create database
          ansible.builtin.command: "clickhouse-client -q 'create database logs;'"
          register: create_db
          failed_when: create_db.rc != 0 and create_db.rc !=82
          changed_when: create_db.rc == 0
      tags: clickhouse

    - block:
        - name: Vector. Create work directory
          ansible.builtin.file:
            path: "{{ vector_workdir }}"
            state: directory
            mode: 0755

        - name: Vector. Get Vector distributive
          ansible.builtin.get_url:
            url: "https://packages.timber.io/vector/{{ vector_version }}/vector-{{ vector_version }}-{{ vector_os_arh }}-unknown-linux-gnu.tar.gz"
            dest: "{{ vector_workdir }}/vector-{{ vector_version }}-{{ vector_os_arh }}-unknown-linux-gnu.tar.gz"
            mode: 0644

        - name: Vector. Unzip archive
          ansible.builtin.unarchive:
            remote_src: true
            src: "{{ vector_workdir }}/vector-{{ vector_version }}-{{ vector_os_arh }}-unknown-linux-gnu.tar.gz"
            dest: "{{ vector_workdir }}"

        - name: Vector. Install vector binary file
          become: true
          ansible.builtin.copy:
            remote_src: true
            src: "{{ vector_workdir }}/vector-{{ vector_os_arh }}-unknown-linux-gnu/bin/vector"
            dest: "/usr/bin/"
            mode: 0755
            owner: root
            group: root

        - name: Vector. Check Vector installation
          ansible.builtin.command: "vector --version"
          register: var_vector
          failed_when: var_vector.rc != 0
          changed_when: var_vector.rc == 0

        - name: Vector. Create Vector config vector.toml
          become: true
          ansible.builtin.copy:
            remote_src: true
            src: "{{ vector_workdir }}/vector-{{ vector_os_arh }}-unknown-linux-gnu/config/vector.toml"
            dest: "/etc/vector/"
            mode: 0644
            owner: root
            group: root

        - name: Vector. Create vector.service daemon
          become: true
          ansible.builtin.copy:
            remote_src: true
            src: "{{ vector_workdir }}/vector-{{ vector_os_arh }}-unknown-linux-gnu/etc/systemd/vector.service"
            dest: "/lib/systemd/system/"
            mode: 0644
            owner: root
            group: root
          notify: Start Vector service

        - name: Vector. Modify vector.service file
          become: true
          ansible.builtin.replace:
            backup: true
            path: "/lib/systemd/system/vector.service"
            regexp: "^ExecStart=/usr/bin/vector$"
            replace: "ExecStart=/usr/bin/vector --config /etc/vector/vector.toml"
          notify: Start Vector service

        - name: Vector. Create user vector
          become: true
          ansible.builtin.user:
            create_home: false
            name: "{{ vector_os_user }}"
            # group: "{{ vector_os_group }}"

        - name: Vector. Create data_dir
          become: true
          ansible.builtin.file:
            path: "/var/lib/vector"
            state: directory
            mode: 0755
            owner: "{{ vector_os_user }}"
            group: "{{ vector_os_group }}"


        - name: Vector. Remove work directory
          ansible.builtin.file:
            path: "{{ vector_workdir }}"
            state: absent

      tags: vector
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

```
cat /etc/vector/vector.toml
#                                    __   __  __
#                                    \ \ / / / /
#                                     \ V / / /
#                                      \_/  \/
#
#                                    V E C T O R
#                                   Configuration
#
# ------------------------------------------------------------------------------
# Website: https://vector.dev
# Docs: https://vector.dev/docs
# Chat: https://chat.vector.dev
# ------------------------------------------------------------------------------
# Change this to use a non-default directory for Vector data storage:
# data_dir = "/var/lib/vector"
# Random Syslog-formatted logs
[sources.dummy_logs]
type = "demo_logs"
format = "syslog"
interval = 1
# Parse Syslog logs
# See the Vector Remap Language reference for more info: https://vrl.dev
[transforms.parse_logs]
type = "remap"
inputs = ["dummy_logs"]
source = '''
. = parse_syslog!(string!(.message))
'''
# Print parsed logs to stdout
[sinks.print]
type = "console"
inputs = ["parse_logs"]
encoding.codec = "json"
# Vector's GraphQL API (disabled by default)
# Uncomment to try it out with the `vector top` command or
# in your browser at http://localhost:8686
#[api]
#enabled = true
#address = "127.0.0.1:8686"
```
#### 8. Повторно запустите playbook с флагом --diff и убедитесь, что playbook идемпотентен.
```
vagrant@server1:~/an-home/playbook$ ansible-playbook -i inventory/prod.yml site.yml --diff
PLAY [Install Clickhouse] ******************************************************************************************************************************************************
TASK [Gathering Facts] *********************************************************************************************************************************************************
ok: [clickhouse-01]
TASK [Get clickhouse distrib] **************************************************************************************************************************************************
ok: [clickhouse-01] => (item=clickhouse-client)
ok: [clickhouse-01] => (item=clickhouse-server)
failed: [clickhouse-01] (item=clickhouse-common-static) => {"ansible_loop_var": "item", "changed": false, "dest": "./clickhouse-common-static-22.3.3.44.rpm", "elapsed": 0, "gid": 0, "group": "root", "item": "clickhouse-common-static", "mode": "0644", "msg": "Request failed", "owner": "root", "response": "HTTP Error 404: Not Found", "size": 246310036, "state": "file", "status_code": 404, "uid": 0, "url": "https://packages.clickhouse.com/rpm/stable/clickhouse-common-static-22.3.3.44.noarch.rpm"}
TASK [Get clickhouse distrib] **************************************************************************************************************************************************
ok: [clickhouse-01]
TASK [Install clickhouse packages] *********************************************************************************************************************************************
ok: [clickhouse-01]
TASK [Create database] *********************************************************************************************************************************************************
ok: [clickhouse-01]
PLAY [Install vector] **********************************************************************************************************************************************************
TASK [Gathering Facts] *********************************************************************************************************************************************************
ok: [vector-01]
TASK [Get vector distrib] ******************************************************************************************************************************************************
ok: [vector-01]
TASK [Install vector packages] *************************************************************************************************************************************************
ok: [vector-01]
PLAY RECAP *********************************************************************************************************************************************************************
clickhouse-01              : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=1    ignored=0
vector-01                  : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
#### 9. Подготовьте README.md-файл по своему playbook. В нём должно быть описано: что делает playbook, какие у него есть параметры и теги.
[Ссылка на README к playbook](https://github.com/dikalov/devops-28/blob/main/08-ansible-02-playbook/playbook/README.md)

#### 10. Готовый playbook выложите в свой [репозиторий](https://github.com/dikalov/devops-28/tree/main/08-ansible-02-playbook/playbook)
