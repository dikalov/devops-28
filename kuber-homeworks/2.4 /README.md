# Домашнее задание к занятию «Управление доступом»
### Задание 1. Создайте конфигурацию для подключения пользователя
##### Создайте и подпишите SSL-сертификат для подключения к кластеру.
```
root@microk8s:~# mkdir cert && cd cert
root@microk8s:~/cert# openssl genrsa -out valdem88.key 2048
root@microk8s:~/cert# openssl req -new -key valdem88.key -out valdem88.csr -subj "/CN=valdem88/O=group1"
root@microk8s:~/cert# openssl x509 -req -in valdem88.csr -CA /var/snap/microk8s/4595/certs/ca.crt -CAkey /var/snap/microk8s/4595/certs/ca.key -CAcreateserial -out valdem88.crt -days 500
Certificate request self-signature ok
subject=CN = valdem88, O = group1
```


