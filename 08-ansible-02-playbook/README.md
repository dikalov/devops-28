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
vagrant@server1:~/an-home/playbook$ ansible-playbook -i inventory/prod.yml site.yml --check

PLAY [Install Clickhouse & Vector] *****************************************************************************************************************

TASK [Clickhouse. Get clickhouse distrib] **********************************************************************************************************
ok: [clickhouse-01] => (item=clickhouse-client)
ok: [clickhouse-01] => (item=clickhouse-server)
failed: [clickhouse-01] (item=clickhouse-common-static) => {"ansible_loop_var": "item", "changed": false, "dest": "./clickhouse-common-static_22.3.3.44_all.deb", "elapsed": 0, "item": "clickhouse-common-static", "msg": "Request failed", "response": "HTTP Error 404: Not Found", "status_code": 404, "url": "https://packages.clickhouse.com/deb/pool/stable/clickhouse-common-static_22.3.3.44_all.deb"}

TASK [Clickhouse. Get clickhouse distrib] **********************************************************************************************************
ok: [clickhouse-01] => (item=clickhouse-client)
ok: [clickhouse-01] => (item=clickhouse-server)
ok: [clickhouse-01] => (item=clickhouse-common-static)

TASK [Clickhouse. Install package clickhouse-common-static] ****************************************************************************************
changed: [clickhouse-01]

TASK [Clickhouse. Install package clickhouse-client] ***********************************************************************************************
fatal: [clickhouse-01]: FAILED! => {"changed": false, "msg": "Dependency is not satisfiable: clickhouse-common-static (= 22.3.3.44)\n"}

RUNNING HANDLER [Start clickhouse service] *********************************************************************************************************

PLAY RECAP *****************************************************************************************************************************************
clickhouse-01              : ok=2    changed=1    unreachable=0    failed=1    skipped=0    rescued=1    ignored=0
```

#### 7. Запустите playbook на prod.yml окружении с флагом --diff. Убедитесь, что изменения на системе произведены.
```
vagrant@server1:~/an-home/playbook$ ansible-playbook -i inventory/prod.yml site.yml --diff

PLAY [Install Clickhouse & Vector] *****************************************************************************************************************

TASK [Clickhouse. Get clickhouse distrib] **********************************************************************************************************
ok: [clickhouse-01] => (item=clickhouse-client)
ok: [clickhouse-01] => (item=clickhouse-server)
failed: [clickhouse-01] (item=clickhouse-common-static) => {"ansible_loop_var": "item", "changed": false, "dest": "./clickhouse-common-static_22.3.3.44_all.deb", "elapsed": 0, "item": "clickhouse-common-static", "msg": "Request failed", "response": "HTTP Error 404: Not Found", "status_code": 404, "url": "https://packages.clickhouse.com/deb/pool/stable/clickhouse-common-static_22.3.3.44_all.deb"}

TASK [Clickhouse. Get clickhouse distrib] **********************************************************************************************************
ok: [clickhouse-01] => (item=clickhouse-client)
ok: [clickhouse-01] => (item=clickhouse-server)
ok: [clickhouse-01] => (item=clickhouse-common-static)

TASK [Clickhouse. Install package clickhouse-common-static] ****************************************************************************************
Selecting previously unselected package clickhouse-common-static.
(Reading database ... 40625 files and directories currently installed.)
Preparing to unpack .../clickhouse-common-static_22.3.3.44_amd64.deb ...
Unpacking clickhouse-common-static (22.3.3.44) ...
Setting up clickhouse-common-static (22.3.3.44) ...
changed: [clickhouse-01]

TASK [Clickhouse. Install package clickhouse-client] ***********************************************************************************************
Selecting previously unselected package clickhouse-client.
(Reading database ... 40639 files and directories currently installed.)
Preparing to unpack .../clickhouse-client_22.3.3.44_all.deb ...
Unpacking clickhouse-client (22.3.3.44) ...
Setting up clickhouse-client (22.3.3.44) ...
changed: [clickhouse-01]

TASK [Clickhouse. Install clickhouse package clickhouse-server] ************************************************************************************
Selecting previously unselected package clickhouse-server.
(Reading database ... 40650 files and directories currently installed.)
Preparing to unpack .../clickhouse-server_22.3.3.44_all.deb ...
Unpacking clickhouse-server (22.3.3.44) ...
Setting up clickhouse-server (22.3.3.44) ...
Processing triggers for systemd (245.4-4ubuntu3.13) ...
changed: [clickhouse-01]

TASK [Clickhouse. Flush handlers] ******************************************************************************************************************

RUNNING HANDLER [Start clickhouse service] *********************************************************************************************************
changed: [clickhouse-01]

TASK [Clickhouse. Waiting while clickhouse-server is available...] *********************************************************************************
Pausing for 10 seconds (output is hidden)
(ctrl+C then 'C' = continue early, ctrl+C then 'A' = abort)
ok: [clickhouse-01]

TASK [Clickhouse. Create database] *****************************************************************************************************************
changed: [clickhouse-01]

TASK [Vector. Create work directory] ***************************************************************************************************************
--- before
+++ after
@@ -1,5 +1,5 @@
 {
-    "mode": "0775",
+    "mode": "0755",
     "path": "/home/vagrant/vector",
-    "state": "absent"
+    "state": "directory"
 }

changed: [clickhouse-01]

TASK [Vector. Get Vector distributive] *************************************************************************************************************
changed: [clickhouse-01]

TASK [Vector. Unzip archive] ***********************************************************************************************************************
changed: [clickhouse-01]

TASK [Vector. Install vector binary file] **********************************************************************************************************
changed: [clickhouse-01]

TASK [Vector. Check Vector installation] ***********************************************************************************************************
changed: [clickhouse-01]

TASK [Vector. Create Vector config vector.toml] ****************************************************************************************************
changed: [clickhouse-01]

TASK [Vector. Create vector.service daemon] ********************************************************************************************************
changed: [clickhouse-01]

TASK [Vector. Modify vector.service file] **********************************************************************************************************
--- before: /lib/systemd/system/vector.service
+++ after: /lib/systemd/system/vector.service
@@ -8,7 +8,7 @@
 User=vector
 Group=vector
 ExecStartPre=/usr/bin/vector validate
-ExecStart=/usr/bin/vector
+ExecStart=/usr/bin/vector --config /etc/vector/vector.toml
 ExecReload=/usr/bin/vector validate
 ExecReload=/bin/kill -HUP $MAINPID
 Restart=no

changed: [clickhouse-01]

TASK [Vector. Create user vector] ******************************************************************************************************************
changed: [clickhouse-01]

TASK [Vector. Create data_dir] *********************************************************************************************************************
--- before
+++ after
@@ -1,6 +1,6 @@
 {
-    "group": 0,
-    "owner": 0,
+    "group": 1001,
+    "owner": 1001,
     "path": "/var/lib/vector",
-    "state": "absent"
+    "state": "directory"
 }

changed: [clickhouse-01]

TASK [Vector. Remove work directory] ***************************************************************************************************************
--- before
+++ after
@@ -1,42 +1,4 @@
 {
     "path": "/home/vagrant/vector",
-    "path_content": {
-        "directories": [
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/bin",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/etc",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/config",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/etc/systemd",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/config/examples",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/config/examples/namespacing",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/config/examples/namespacing/sources",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/config/examples/namespacing/sinks",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/config/examples/namespacing/transforms"
-        ],
-        "files": [
-            "/home/vagrant/vector/vector-0.21.1-x86_64-unknown-linux-gnu.tar.gz",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/README.md",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/LICENSE",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/bin/vector",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/etc/systemd/hardened-vector.service",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/etc/systemd/vector.default",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/etc/systemd/vector.service",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/config/vector.toml",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/config/examples/wrapped_json.toml",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/config/examples/es_s3_hybrid.toml",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/config/examples/file_to_prometheus.toml",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/config/examples/prometheus_to_console.toml",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/config/examples/docs_example.toml",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/config/examples/environment_variables.toml",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/config/examples/file_to_cloudwatch_metrics.toml",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/config/examples/stdio.toml",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/config/examples/namespacing/vector.toml",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/config/examples/namespacing/sources/apache_logs.toml",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/config/examples/namespacing/sinks/s3_archives.toml",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/config/examples/namespacing/sinks/es_cluster.toml",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/config/examples/namespacing/transforms/apache_sample.toml",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/config/examples/namespacing/transforms/apache_parser.toml"
-        ]
-    },
-    "state": "directory"
+    "state": "absent"
 }

changed: [clickhouse-01]

RUNNING HANDLER [Start Vector service] *************************************************************************************************************
changed: [clickhouse-01]

PLAY RECAP *****************************************************************************************************************************************
clickhouse-01              : ok=19   changed=17   unreachable=0    failed=0    skipped=0    rescued=1    ignored=0
```
#### 8. Повторно запустите playbook с флагом --diff и убедитесь, что playbook идемпотентен.
```
vagrant@server1:~/an-home/playbook$ ansible-playbook -i inventory/prod.yml site.yml --diff

PLAY [Install Clickhouse & Vector] *****************************************************************************************************************

TASK [Clickhouse. Get clickhouse distrib] **********************************************************************************************************
ok: [clickhouse-01] => (item=clickhouse-client)
ok: [clickhouse-01] => (item=clickhouse-server)
failed: [clickhouse-01] (item=clickhouse-common-static) => {"ansible_loop_var": "item", "changed": false, "dest": "./clickhouse-common-static_22.3.3.44_all.deb", "elapsed": 0, "item": "clickhouse-common-static", "msg": "Request failed", "response": "HTTP Error 404: Not Found", "status_code": 404, "url": "https://packages.clickhouse.com/deb/pool/stable/clickhouse-common-static_22.3.3.44_all.deb"}

TASK [Clickhouse. Get clickhouse distrib] **********************************************************************************************************
ok: [clickhouse-01] => (item=clickhouse-client)
ok: [clickhouse-01] => (item=clickhouse-server)
ok: [clickhouse-01] => (item=clickhouse-common-static)

TASK [Clickhouse. Install package clickhouse-common-static] ****************************************************************************************
ok: [clickhouse-01]

TASK [Clickhouse. Install package clickhouse-client] ***********************************************************************************************
ok: [clickhouse-01]

TASK [Clickhouse. Install clickhouse package clickhouse-server] ************************************************************************************
ok: [clickhouse-01]

TASK [Clickhouse. Flush handlers] ******************************************************************************************************************

TASK [Clickhouse. Waiting while clickhouse-server is available...] *********************************************************************************
Pausing for 10 seconds (output is hidden)
(ctrl+C then 'C' = continue early, ctrl+C then 'A' = abort)
ok: [clickhouse-01]

TASK [Clickhouse. Create database] *****************************************************************************************************************
ok: [clickhouse-01]

TASK [Vector. Create work directory] ***************************************************************************************************************
--- before
+++ after
@@ -1,5 +1,5 @@
 {
-    "mode": "0775",
+    "mode": "0755",
     "path": "/home/vagrant/vector",
-    "state": "absent"
+    "state": "directory"
 }

changed: [clickhouse-01]

TASK [Vector. Get Vector distributive] *************************************************************************************************************
changed: [clickhouse-01]

TASK [Vector. Unzip archive] ***********************************************************************************************************************
changed: [clickhouse-01]

TASK [Vector. Install vector binary file] **********************************************************************************************************
ok: [clickhouse-01]

TASK [Vector. Check Vector installation] ***********************************************************************************************************
changed: [clickhouse-01]

TASK [Vector. Create Vector config vector.toml] ****************************************************************************************************
ok: [clickhouse-01]

TASK [Vector. Create vector.service daemon] ********************************************************************************************************
changed: [clickhouse-01]

TASK [Vector. Modify vector.service file] **********************************************************************************************************
--- before: /lib/systemd/system/vector.service
+++ after: /lib/systemd/system/vector.service
@@ -8,7 +8,7 @@
 User=vector
 Group=vector
 ExecStartPre=/usr/bin/vector validate
-ExecStart=/usr/bin/vector
+ExecStart=/usr/bin/vector --config /etc/vector/vector.toml
 ExecReload=/usr/bin/vector validate
 ExecReload=/bin/kill -HUP $MAINPID
 Restart=no

changed: [clickhouse-01]

TASK [Vector. Create user vector] ******************************************************************************************************************
ok: [clickhouse-01]

TASK [Vector. Create data_dir] *********************************************************************************************************************
ok: [clickhouse-01]

TASK [Vector. Remove work directory] ***************************************************************************************************************
--- before
+++ after
@@ -1,42 +1,4 @@
 {
     "path": "/home/vagrant/vector",
-    "path_content": {
-        "directories": [
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/bin",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/etc",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/config",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/etc/systemd",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/config/examples",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/config/examples/namespacing",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/config/examples/namespacing/sources",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/config/examples/namespacing/sinks",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/config/examples/namespacing/transforms"
-        ],
-        "files": [
-            "/home/vagrant/vector/vector-0.21.1-x86_64-unknown-linux-gnu.tar.gz",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/README.md",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/LICENSE",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/bin/vector",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/etc/systemd/hardened-vector.service",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/etc/systemd/vector.default",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/etc/systemd/vector.service",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/config/vector.toml",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/config/examples/wrapped_json.toml",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/config/examples/es_s3_hybrid.toml",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/config/examples/file_to_prometheus.toml",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/config/examples/prometheus_to_console.toml",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/config/examples/docs_example.toml",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/config/examples/environment_variables.toml",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/config/examples/file_to_cloudwatch_metrics.toml",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/config/examples/stdio.toml",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/config/examples/namespacing/vector.toml",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/config/examples/namespacing/sources/apache_logs.toml",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/config/examples/namespacing/sinks/s3_archives.toml",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/config/examples/namespacing/sinks/es_cluster.toml",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/config/examples/namespacing/transforms/apache_sample.toml",
-            "/home/vagrant/vector/vector-x86_64-unknown-linux-gnu/config/examples/namespacing/transforms/apache_parser.toml"
-        ]
-    },
-    "state": "directory"
+    "state": "absent"
 }

changed: [clickhouse-01]

RUNNING HANDLER [Start Vector service] *************************************************************************************************************
ok: [clickhouse-01]

PLAY RECAP *****************************************************************************************************************************************
clickhouse-01              : ok=18   changed=7    unreachable=0    failed=0    skipped=0    rescued=1    ignored=0
```
#### 9. Подготовьте README.md-файл по своему playbook. В нём должно быть описано: что делает playbook, какие у него есть параметры и теги.
[Ссылка на README к playbook](https://github.com/dikalov/devops-28/blob/main/08-ansible-02-playbook/playbook/README.md)

#### 10. Готовый playbook выложите в свой [репозиторий](https://github.com/dikalov/devops-28/tree/main/08-ansible-02-playbook/playbook)
