## Домашнее задание к занятию 2 «Работа с Playbook»
#### 1. Подготовьте свой inventory-файл prod.yml.
```
---
clickhouse:
  hosts:
    centos7:
      ansible_connection: docker
vector:
  hosts:
    vector:
      ansible_connection: docker
```
#### 2. Допишите playbook: нужно сделать ещё один play, который устанавливает и настраивает vector. Конфигурация vector должна деплоиться через template файл jinja2.
```
---
- name: Install Vector
  hosts: vector
  tasks:
    - name: Get Vector distrib
      ansible.builtin.get_url: 
        url: "https://packages.timber.io/vector/{{ vector_version }}/vector-{{ vector_version }}-1.x86_64.rpm"
        dest: "./vector-{{ vector_version }}-1.x86_64.rpm"
    - name: Install vector packages
      become: true
      ansible.builtin.yum:
        name:
        - ./vector-{{ vector_version }}-1.x86_64.rpm
        state: present
    - name : Config template
      ansible.builtin.template:
        src: vector.j2
        dest: vector.yml
        mode: "0644"
        owner: "{{ ansible_user_id }}"
        group: "{{ ansible_user_gid }}"
        validate: vector validate --no-environment --config-yaml %s
```
#### 3. При создании tasks рекомендую использовать модули: get_url, template, unarchive, file.

#### 4. Tasks должны: скачать дистрибутив нужной версии, выполнить распаковку в выбранную директорию, установить vector.

#### 5. Запустите ansible-lint site.yml и исправьте ошибки, если они есть.
Ошибки были исправлены.

#### 6. Попробуйте запустить playbook на этом окружении с флагом --check.
```
vagrant@server1:~/an-home/playbook$ ansible-playbook -i inventory/prod.yml site.yml --check
TASK [Create database] *********************************************************************************************
skipping: [centos7]

PLAY [Install Vector] **********************************************************************************************

TASK [Gathering Facts] *********************************************************************************************
ok: [vector]

TASK [Get Vector distrib] ******************************************************************************************
ok: [vector]

TASK [Install vector packages] *************************************************************************************
ok: [vector]

TASK [Config template] *********************************************************************************************
ok: [vector]

PLAY RECAP *********************************************************************************************************
centos7                    : ok=3    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0   
vector                     : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

#### 7. Запустите playbook на prod.yml окружении с флагом --diff. Убедитесь, что изменения на системе произведены.
```
vagrant@server1:~/an-home/playbook$ ansible-playbook -i inventory/prod.yml site.yml --diff

TASK [Flush handlers] **********************************************************************************************

TASK [Create database] *********************************************************************************************
ok: [centos7]

PLAY [Install Vector] **********************************************************************************************

TASK [Gathering Facts] *********************************************************************************************
ok: [vector]

TASK [Get Vector distrib] ******************************************************************************************
ok: [vector]

TASK [Install vector packages] *************************************************************************************
ok: [vector]

TASK [Config template] *********************************************************************************************
ok: [vector]

PLAY RECAP *********************************************************************************************************
centos7                    : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
vector                     : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
#### 8. Повторно запустите playbook с флагом --diff и убедитесь, что playbook идемпотентен.

#### 9. Подготовьте README.md-файл по своему playbook. В нём должно быть описано: что делает playbook, какие у него есть параметры и теги.
[Ссылка на README к playbook](https://github.com/dikalov/devops-28/blob/main/08-ansible-02-playbook/playbook/README.md)

#### 10. Готовый playbook выложите в свой [репозиторий](https://github.com/dikalov/devops-28/tree/main/08-ansible-02-playbook/playbook)
