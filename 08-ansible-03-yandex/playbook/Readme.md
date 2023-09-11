### Install Clickhouse устанавливает clickhouse на все машины в группе clickhouse указанные в inventory файле.
```
- name: Install Clickhouse
  hosts: clickhouse
  handlers:
    - name: Start clickhouse service
      become: true
      ansible.builtin.service:
        name: clickhouse-server
        state: restarted
```
