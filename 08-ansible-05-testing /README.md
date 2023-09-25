## Домашнее задание к занятию 5 «Тестирование roles»
![image](https://github.com/dikalov/devops-28/assets/126553776/e940b3b4-b099-42d2-a0d9-504d2750ef49)

![image](https://github.com/dikalov/devops-28/assets/126553776/5baa5780-e59d-4b2e-aabc-2c579750cc48)

### Molecule

#### 1. Запустите molecule test -s centos_7 внутри корневой директории clickhouse-role, посмотрите на вывод команды. Данная команда может отработать с ошибками, это нормально. Наша цель - посмотреть как другие в реальном мире используют молекулу.
```
# molecule test -s centos_7
INFO     centos_7 scenario test matrix: dependency, lint, cleanup, destroy, syntax, create, prepare, converge, idempotence, side_effect, verify, cleanup, destroy
INFO     Performing prerun...
INFO     Set ANSIBLE_LIBRARY=/home/vagrant/.cache/ansible-compat/7e099f/modules:/home/vagrant/.ansible/plugins/modules:/usr/share/ansible/plugins/modules
INFO     Set ANSIBLE_COLLECTIONS_PATH=/home/vagrant/.cache/ansible-compat/7e099f/collections:/home/vagrant/.ansible/collections:/usr/share/ansible/collections
INFO     Set ANSIBLE_ROLES_PATH=/home/vagrant/.cache/ansible-compat/7e099f/roles:/home/vagrant/.ansible/roles:/usr/share/ansible/roles:/etc/ansible/roles
INFO     Inventory /home/vagrant/.ansible/roles/clickhouse/molecule/centos_7/../resources/inventory/hosts.yml linked to /home/vagrant/.cache/molecule/clickhouse/centos_7/inventory/hosts
INFO     Inventory /home/vagrant/.ansible/roles/clickhouse/molecule/centos_7/../resources/inventory/group_vars/ linked to /home/vagrant/.cache/molecule/clickhouse/centos_7/inventory/group_vars
INFO     Inventory /home/vagrant/.ansible/roles/clickhouse/molecule/centos_7/../resources/inventory/host_vars/ linked to /home/vagrant/.cache/molecule/clickhouse/centos_7/inventory/host_vars
INFO     Running centos_7 > dependency
WARNING  Skipping, missing the requirements file.
WARNING  Skipping, missing the requirements file.
INFO     Inventory /home/vagrant/.ansible/roles/clickhouse/molecule/centos_7/../resources/inventory/hosts.yml linked to /home/vagrant/.cache/molecule/clickhouse/centos_7/inventory/hosts
INFO     Inventory /home/vagrant/.ansible/roles/clickhouse/molecule/centos_7/../resources/inventory/group_vars/ linked to /home/vagrant/.cache/molecule/clickhouse/centos_7/inventory/group_vars
INFO     Inventory /home/vagrant/.ansible/roles/clickhouse/molecule/centos_7/../resources/inventory/host_vars/ linked to /home/vagrant/.cache/molecule/clickhouse/centos_7/inventory/host_vars
INFO     Running centos_7 > lint
COMMAND: yamllint .
ansible-lint
flake8
```
#### 2. Перейдите в каталог с ролью vector-role и создайте сценарий тестирования по умолчанию при помощи molecule init scenario --driver-name docker.
```
# molecule init scenario --driver-name docker
INFO     Initializing new scenario default...
INFO     Initialized scenario in /root/ansible_role/roles/vector-role/molecule/default successfully
```
#### 3. Добавьте несколько разных дистрибутивов (centos:8, ubuntu:latest) для инстансов и протестируйте роль, исправьте найденные ошибки, если они есть.
```
# molecule init scenario centos8 --driver-name docker
INFO     Initializing new scenario centos8...
INFO     Initialized scenario in /home/vagrant/.ansible/roles/vector-role/molecule/centos8 successfully.

# molecule init scenario ubuntu_latest --driver-name docker
INFO     Initializing new scenario ubuntu_latest...
INFO     Initialized scenario in /home/vagrant/.ansible/roles/vector-role/molecule/ubuntu_latest successfully.
```
#### 4. Добавьте несколько assert в verify.yml-файл для проверки работоспособности vector-role (проверка, что конфиг валидный, проверка успешности запуска и др.).
```
# This is an example playbook to execute Ansible tests.

- name: Verify
  hosts: all
  gather_facts: false
  vars:
    vector_config_path: /etc/vector/vector.yaml
  tasks:

  - name: Get Vector version
    ansible.builtin.command: "vector --version"
    changed_when: false
    register: vector_version
  - name: Assert Vector instalation
    assert:
      that: "'{{ vector_version.rc }}' == '0'"

  - name: Validation Vector configuration
    ansible.builtin.command: "vector validate --no-environment --config-yaml {{ vector_config_path }}"
    changed_when: false
    register: vector_validate
  - name: Assert Vector validate config
    assert:
      that: "'{{ vector_validate.rc }}' == '0'"
```
#### 5. Запустите тестирование роли повторно и проверьте, что оно прошло успешно.
```
PLAY [Verify] ******************************************************************

TASK [Get Vector version] ******************************************************
ok: [instance]

TASK [Assert Vector instalation] ***********************************************
ok: [instance] => {
    "changed": false,
    "msg": "All assertions passed"
}

TASK [Validation Vector configuration] *****************************************
ok: [instance]

TASK [Assert Vector validate config] *******************************************
ok: [instance] => {
    "changed": false,
    "msg": "All assertions passed"
}

PLAY RECAP *********************************************************************
instance                   : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

INFO     Verifier completed successfully.
INFO     Running ubuntu_latest > cleanup
WARNING  Skipping, cleanup playbook not configured.
INFO     Running ubuntu_latest > destroy

PLAY [Destroy] *****************************************************************

TASK [Set async_dir for HOME env] **********************************************
ok: [localhost]

TASK [Destroy molecule instance(s)] ********************************************
changed: [localhost] => (item=instance)

TASK [Wait for instance(s) deletion to complete] *******************************
FAILED - RETRYING: [localhost]: Wait for instance(s) deletion to complete (300 retries left).
changed: [localhost] => (item=instance)

TASK [Delete docker networks(s)] ***********************************************

PLAY RECAP *********************************************************************
localhost                  : ok=3    changed=2    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
```
#### 6. Добавьте новый тег на коммит с рабочим сценарием в соответствии с семантическим версионированием.

[Новый тэг на vector](https://github.com/dikalov/vector-role)
