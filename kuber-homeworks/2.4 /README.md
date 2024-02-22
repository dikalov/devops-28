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
root@ansibleserv:~/.kube# kubectl config set-credentials dikalov --client-certificate=cert/dikalov.crt --client-key=cert/dikalov.key
User "dikalov" set.
root@ansibleserv:~/.kube# cd
root@ansibleserv:~# kubectl config set-context dikalov-context --cluster=microk8s-cluster --user=dikalov
Context "dikalov-context" created.
root@ansibleserv:~# kubectl config view
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://192.168.1.91:16443
  name: microk8s-cluster
contexts:
- context:
    cluster: microk8s-cluster
    user: admin
  name: microk8s
- context:
    cluster: microk8s-cluster
    user: dikalov
  name: dikalov-context
current-context: microk8s
kind: Config
preferences: {}
users:
- name: admin
  user:
    token: REDACTED
- name: dikalov
  user:
    client-certificate: cert/dikalov.crt
    client-key: cert/dikalov.key
```
##### Создайте роли и все необходимые настройки для пользователя.
[Role](https://github.com/dikalov/devops-28/blob/main/kuber-homeworks/2.4%20/file/role.yaml)

[Role_binding](https://github.com/dikalov/devops-28/blob/main/kuber-homeworks/2.4%20/file/role_binding.yaml)
```
$ kubectl apply -f rbac/role.yaml 
role.rbac.authorization.k8s.io/pod-desc-logs created

$ kubectl apply -f rbac/role_binding.yaml 
rolebinding.rbac.authorization.k8s.io/pod-reader created
```
##### Предусмотрите права пользователя. Пользователь может просматривать логи подов и их конфигурацию (kubectl logs pod <pod_id>, kubectl describe pod <pod_id>).
Для начала добавим в роль verbs: где ["watch", "list"]
```
$ kubectl get role pod-desc-logs -o yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"rbac.authorization.k8s.io/v1","kind":"Role","metadata":{"annotations":{},"name":"pod-desc-logs","namespace":"default"},"rules":[{"apiGroups":[""],"resources":["pods","pods/log"],"verbs":["watch","list"]}]}
  creationTimestamp: "2024-02-22T09:18:19Z"
  name: pod-desc-logs
  namespace: default
  resourceVersion: "285619"
  uid: d085e6ab-6bca-6e52-ba06-160098cbd387
rules:
- apiGroups:
  - ""
  resources:
  - pods
  - pods/log
  verbs:
  - watch
  - list
```


