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
И проверим:
```
root@ansibleserv:~# kubectl config use-context dikalov-context
Switched to context "dikalov-context".
root@ansibleserv:~# kubectl describe pod myapp-pod-6c5b7a7cb2-4xgr6
Error from server (Forbidden): pods "myapp-pod-6c5b7a7cb2-4xgr6" is forbidden: User "dikalov" cannot get resource "pods" in API group "" in the namespace "default"
root@ansibleserv:~# kubectl logs myapp-pod-6c5b7a7cb2-4xgr6
Error from server (Forbidden): pods "myapp-pod-6c5b7a7cb2-4xgr6" is forbidden: User "dikalov" cannot get resource "pods" in API group "" in the namespace "default"
```
Для выполнения команд logs и describe пользователю нужно добавить в роль verbs: ["get"]
```
$ kubectl apply -f rbac/role.yaml 
role.rbac.authorization.k8s.io/pod-desc-logs configured

$ kubectl get role pod-desc-logs -o yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"rbac.authorization.k8s.io/v1","kind":"Role","metadata":{"annotations":{},"name":"pod-desc-logs","namespace":"default"},"rules":[{"apiGroups":[""],"resources":["pods","pods/log"],"verbs":["watch","list","get"]}]}   
  creationTimestamp: "2024-02-22T09:18:19Z"
  name: pod-desc-logs
  namespace: default
  resourceVersion: "286986"
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
  - get
```
Проверим, что изменилось с тестовой машины
```
root@ansibleserv:~# kubectl describe pod myapp-pod-6c5b7a7cb2-4xgr6
Name:             myapp-pod-6c5b7a7cb2-4xgr6
Namespace:        default
Priority:         0
Service Account:  default
Node:             microk8s/192.168.1.91
Start Time:       Wed, 21 Feb 2024 12:32:12 +0300
Labels:           app=myapp
                  pod-template-hash=6c5b7a7cb2
Annotations:      cni.projectcalico.org/containerID: b436753fc7a2ed7ca3f356dd1e1a17ca45185a03ecc30c0f9842a3b24bfe9ae2
                  cni.projectcalico.org/podIP: 10.1.163.212/32
                  cni.projectcalico.org/podIPs: 10.1.163.212/32
Status:           Running
IP:               10.1.163.212
IPs:
  IP:           10.1.163.212
Controlled By:  ReplicaSet/myapp-pod-6c5b7a7cb2
Init Containers:
  init-myservice:
    Container ID:  containerd://12f8fd4562c238f87744c46958327323187818aa270ffc266387f701b6264a09
    Image:         busybox:1.28
    Image ID:      docker.io/library/busybox@sha256:141c253bc4c3fd0a201d32dc1f493bcf3fff003b6df416dea4f41046e0f37d47
    Port:          <none>
    Host Port:     <none>
    Command:
      sh
      -c
      until nslookup myservice.$(cat /var/run/secrets/kubernetes.io/serviceaccount/namespace).svc.cluster.local; do echo waiting for myservice; sleep 2; done
    State:          Terminated
      Reason:       Completed
      Exit Code:    0
      Started:      Thu, 22 Feb 2024 09:41:35 +0300
      Finished:     Thu, 22 Feb 2024 09:42:12 +0300
    Ready:          True
    Restart Count:  1
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-jn9xg (ro)
Containers:
  network-multitool:
    Container ID:   containerd://c276efc0dd6eacebeb90de63911d1698043185b0b616b35750ed1006728dd51a
    Image:          wbitt/network-multitool
    Image ID:       docker.io/wbitt/network-multitool@sha256:82a5ea955024390d6b438ce22ccc75c98b481bf00e57c13e9a9cc1458eb92652
    Ports:          80/TCP, 443/TCP
    Host Ports:     0/TCP, 0/TCP
    State:          Running
      Started:      Wed, 29 Mar 2023 14:02:11 +0300
    Last State:     Terminated
      Reason:       Unknown
      Exit Code:    255
      Started:      Wed, 21 Feb 2024 12:32:14 +0300
      Finished:     Thu, 22 Feb 2024 09:40:57 +0300
    Ready:          True
    Restart Count:  1
    Limits:
      cpu:     200m
      memory:  512Mi
    Requests:
      cpu:     100m
      memory:  256Mi
    Environment:
      HTTP_PORT:   80
      HTTPS_PORT:  443
    Mounts:
      /usr/share/nginx/html/ from nginx-index-file (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-jn9xg (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  nginx-index-file:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      index-html-configmap
    Optional:  false
  kube-api-access-jn9xg:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   Burstable
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:                      <none>
root@ansibleserv:~# kubectl logs myapp-pod-6c5b7a7cb2-4xgr6
Defaulted container "network-multitool" out of: network-multitool, init-myservice (init)
The directory /usr/share/nginx/html is a volume mount.
Therefore, will not over-write index.html
Only logging the container characteristics:
WBITT Network MultiTool (with NGINX) - myapp-pod-6c5b7a7cb2-4xgr6 -  - HTTP: 80 , HTTPS: 443 . (Formerly praqma/network-multitool)
Replacing default HTTP port (80) with the value specified by the user - (HTTP_PORT: 80).
Replacing default HTTPS port (443) with the value specified by the user - (HTTPS_PORT: 443).
10.1.163.211 - - [22/Feb/2024:09:08:32 +0000] "GET / HTTP/1.1" 200 82 "-" "curl/7.78.0" "192.168.1.89"
10.1.163.211 - - [22/Feb/2024:09:10:29 +0000] "GET / HTTP/1.1" 200 82 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/111.0.0.0 Safari/537.36" "192.168.1.89"
10.1.163.211 - - [22/Feb/2024:09:10:29 +0000] "GET /favicon.ico HTTP/1.1" 404 555 "https://my-app.com/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/111.0.0.0 Safari/537.36" "192.168.1.89"
2024/02/22 09:10:30 [error] 12#12: *2 open() "/usr/share/nginx/html/favicon.ico" failed (2: No such file or directory), client: 10.1.163.211, server: localhost, request: "GET /favicon.ico HTTP/1.1", host: "my-app.com", referrer: "https://my-app.com/"
root@ansibleserv:~#
```
Команды logs и describe от уч. записи dikalov работают.

Попробуем сразу после этого удалить pod - получим ошибку для пользователя ```dikalov```.
```
Error from server (Forbidden): pods "myapp-pod-6c5b7a7cb2-4xgr6" is forbidden: User "dikalov" cannot delete resource "pods" in API group "" in the namespace "default"
```
##### Предоставьте манифесты и скриншоты и/или вывод необходимых команд.
[Role](https://github.com/dikalov/devops-28/blob/main/kuber-homeworks/2.4%20/file/role.yaml)




