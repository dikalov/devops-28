# Домашнее задание к занятию «Управление доступом»
### Задание 1. Создайте конфигурацию для подключения пользователя
##### Создайте и подпишите SSL-сертификат для подключения к кластеру.
```
root@microk8s:~# mkdir cert && cd cert
root@microk8s:~/cert# openssl genrsa -out dikalov.key 2048
root@microk8s:~/cert# openssl req -new -key dikalov.key -out dikalov.csr -subj "/CN=dikalov/O=group1"
root@microk8s:~/cert# openssl x509 -req -in dikalov.csr -CA /var/snap/microk8s/4595/certs/ca.crt -CAkey /var/snap/microk8s/4595/certs/ca.key -CAcreateserial -out dikalov.crt -days 500
Certificate request self-signature ok
subject=CN = dikalov, O = group1
```
##### Настройте конфигурационный файл kubectl для подключения.
Перенесём на машину с которой будем подключаться и настраиваем конфигурационный файл. Добавим пользователя, добавим контекст и проверим конфигурацию.
```
root@ansibleserv:~/.kube# kubectl config set-credentials valdem88 --client-certificate=cert/valdem88.crt --client-key=cert/valdem88.key
User "valdem88" set.
root@ansibleserv:~/.kube# cd
root@ansibleserv:~# kubectl config set-context valdem88-context --cluster=microk8s-cluster --user=valdem88
Context "valdem88-context" created.
root@ansibleserv:~# kubectl config view
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://192.168.1.88:16443
  name: microk8s-cluster
contexts:
- context:
    cluster: microk8s-cluster
    user: admin
  name: microk8s
- context:
    cluster: microk8s-cluster
    user: valdem88
  name: valdem88-context
current-context: microk8s
kind: Config
preferences: {}
users:
- name: admin
  user:
    token: REDACTED
- name: valdem88
  user:
    client-certificate: cert/valdem88.crt
    client-key: cert/valdem88.key
```



