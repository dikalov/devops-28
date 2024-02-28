# Домашнее задание к занятию «Helm»
### Задание 1. Подготовить Helm-чарт для приложения
1. Необходимо упаковать приложение в чарт для деплоя в разные окружения.
2. Каждый компонент приложения деплоится отдельным deployment’ом или statefulset’ом.
3. В переменных чарта измените образ приложения для изменения версии.

Возьмём файлы для деплоя, которые применялись в лекции.
```
root@ansibleserv:~/helm# git clone https://github.com/aak74/kubernetes-for-beginners.git
Cloning into 'kubernetes-for-beginners'...
remote: Enumerating objects: 983, done.
remote: Counting objects: 100% (236/236), done.
remote: Compressing objects: 100% (173/173), done.
remote: Total 983 (delta 72), reused 173 (delta 45), pack-reused 747
Receiving objects: 100% (983/983), 2.64 MiB | 3.10 MiB/s, done.
Resolving deltas: 100% (362/362), done.
root@ansibleserv:~/helm# cp /root/helm/kubernetes-for-beginners/40-helm/ /root/helm/
cp: -r not specified; omitting directory '/root/helm/kubernetes-for-beginners/40-helm/'
root@ansibleserv:~/helm# cp -R /root/helm/kubernetes-for-beginners/40-helm/ /root/helm/
root@ansibleserv:~/helm# rm -R kubernetes-for-beginners/
root@ansibleserv:~/helm# ls -l
total 4
drwxr-xr-x 5 root root 4096 Feb  28 10:52 40-helm
```
Создадим шаблон на базе данных файлов.
```
root@ansibleserv:~/helm# cd /root/helm/40-helm/01-templating/charts/
root@ansibleserv:~/helm/40-helm/01-templating/charts# helm template 01-simple
---
# Source: hard/templates/service.yaml
apiVersion: v1
kind: Service
metadata:
  name: demo
  labels:
    app: demo
spec:
  ports:
    - port: 80
      name: http
  selector:
    app: demo
---
# Source: hard/templates/deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: demo
  labels:
    app: demo
spec:
  replicas: 1
  selector:
    matchLabels:
      app: demo
  template:
    metadata:
      labels:
        app: demo
    spec:
      containers:
        - name: hard
          image: "nginx:1.16.0"
          imagePullPolicy: IfNotPresent
          ports:
            - name: http
              containerPort: 80
              protocol: TCP
          resources:
            limits:
              cpu: 200m
              memory: 256Mi
            requests:
              cpu: 100m
              memory: 128Mi
```
В данном случае helm при запуске создаст service(port 80) и deployment(nginx version 1.16.0)

В переменных чарта редактируем образ приложения

В файле Chart.yaml изменим номер версии приложения
```
root@ansibleserv:~/helm/40-helm/01-templating/charts# nano 01-simple/Chart.yaml
root@ansibleserv:~/helm/40-helm/01-templating/charts# cat 01-simple/Chart.yaml
apiVersion: v2
name: hard
description: A minimal chart for demo

type: application

version: 0.1.2
appVersion: "1.19.0"
```
Проверим шаблон:
```
root@ansibleserv:~/helm/40-helm/01-templating/charts# helm template 01-simple
---
# Source: hard/templates/service.yaml
apiVersion: v1
kind: Service
metadata:
  name: demo
  labels:
    app: demo
spec:
  ports:
    - port: 80
      name: http
  selector:
    app: demo
---
# Source: hard/templates/deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: demo
  labels:
    app: demo
spec:
  replicas: 1
  selector:
    matchLabels:
      app: demo
  template:
    metadata:
      labels:
        app: demo
    spec:
      containers:
        - name: hard
          image: "nginx:1.19.0"
          imagePullPolicy: IfNotPresent
          ports:
            - name: http
              containerPort: 80
              protocol: TCP
          resources:
            limits:
              cpu: 200m
              memory: 256Mi
            requests:
              cpu: 100m
              memory: 128Mi
root@ansibleserv:~/helm/40-helm/01-templating/charts#
```
### Задание 2. Запустить две версии в разных неймспейсах
1. Подготовив чарт, необходимо его проверить. Запуститe несколько копий приложения.
2. Одну версию в namespace=app1, вторую версию в том же неймспейсе, третью версию в namespace=app2.
3. Продемонстрируйте результат.

Для начала, проверим:
```
root@ansibleserv:~/helm/40-helm/01-templating/charts# helm template 01-simple
---
# Source: hard/templates/service.yaml
apiVersion: v1
kind: Service
metadata:
  name: demo
  labels:
    app: demo
spec:
  ports:
    - port: 80
      name: http
  selector:
    app: demo
---
# Source: hard/templates/deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: demo
  labels:
    app: demo
spec:
  replicas: 1
  selector:
    matchLabels:
      app: demo
  template:
    metadata:
      labels:
        app: demo
    spec:
      containers:
        - name: hard
          image: "nginx:1.19.0"
          imagePullPolicy: IfNotPresent
          ports:
            - name: http
              containerPort: 80
              protocol: TCP
          resources:
            limits:
              cpu: 200m
              memory: 256Mi
            requests:
              cpu: 100m
              memory: 128Mi
root@ansibleserv:~/helm/40-helm/01-templating/charts#
root@ansibleserv:~/helm/40-helm/01-templating/charts# helm install demo1 01-simple
NAME: demo1
LAST DEPLOYED: Wed Feb  28 12:11:31 2023
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
---------------------------------------------------------

Content of NOTES.txt appears after deploy.
Deployed version 1.19.0.

---------------------------------------------------------
root@ansibleserv:~/helm/40-helm/01-templating/charts# kubectl get all
NAME                             READY   STATUS    RESTARTS      AGE
pod/myapp-pod-6c5b7a7cb2-4xgr6   1/1     Running   3 (76m ago)   6d
pod/demo-52a758b69-fsbac         1/1     Running   0             17s

NAME                 TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                         AGE
service/kubernetes   ClusterIP   10.163.121.1     <none>        443/TCP                         48d
service/np-mysvc     NodePort    10.163.121.212   <none>        9001:30080/TCP,9002:32080/TCP   36d
service/fe-svc       ClusterIP   10.163.121.160   <none>        80/TCP                          35d
service/be-svc       ClusterIP   10.163.121.212   <none>        80/TCP                          35d
service/myservice    NodePort    10.163.121.120   <none>        80:32000/TCP                    10d
service/demo         ClusterIP   10.163.121.118   <none>        80/TCP                          31s

NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/myapp-pod   1/1     1            1           6d
deployment.apps/demo        1/1     1            1           30s

NAME                                   DESIRED   CURRENT   READY   AGE
replicaset.apps/myapp-pod-6c5b7a7cb2   1         1         1       6d
replicaset.apps/demo-52a758b69         1         1         1       30s
```
```
root@ansibleserv:~/helm/40-helm/01-templating/charts# helm list
NAME    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART           APP VERSION
demo1   default         1               2024-02-28 12:56:43.564257789 +0300 MSK deployed        hard-0.1.2      1.19.0
```
Теперь можно запустить несколько версий приложения.
```
root@ansibleserv:~/helm/40-helm/01-templating/charts# helm upgrade demo1 --set replicaCount=3 01-simple
Release "demo1" has been upgraded. Happy Helming!
NAME: demo1
LAST DEPLOYED: Wed Feb  28 13:05:47 2024
NAMESPACE: default
STATUS: deployed
REVISION: 2
TEST SUITE: None
NOTES:
---------------------------------------------------------

Content of NOTES.txt appears after deploy.
Deployed version 1.19.0.

---------------------------------------------------------
root@ansibleserv:~/helm/40-helm/01-templating/charts# helm list
NAME    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART           APP VERSION
demo1   default         2               2024-02-28 13:05:47.969875869 +0300 MSK deployed        hard-0.1.2      1.19.0
root@ansibleserv:~/helm/40-helm/01-templating/charts# kubectl get pod
NAME                         READY   STATUS    RESTARTS      AGE
myapp-pod-6c5b7a7cb2-4xgr6   1/1     Running   3 (89m ago)   6d
demo-52a758b69-fsbac         1/1     Running   0             9m2s
demo-52a758b69-er6fg         1/1     Running   0             27s
demo-52a758b69-jdndv         1/1     Running   0             27s
```
Удаляем наш helm demo1, потом создаём новый в namespace по условию задачи
```
root@ansibleserv:~/helm/40-helm/01-templating/charts# helm uninstall demo1
release "demo1" uninstalled
root@ansibleserv:~/helm/40-helm/01-templating/charts# kubectl get pod
NAME                         READY   STATUS    RESTARTS       AGE
myapp-pod-6c5b7a7cb2-4xgr6   1/1     Running   3 (105m ago)   6d
```
Создаём новый:
```
root@ansibleserv:~/helm/40-helm/01-templating/charts# helm install demo2 --namespace app1 --create-namespace --wait --set replicaCount=2 01-simple                 NAME: demo2
LAST DEPLOYED: Wed Feb  28 14:17:42 2024
NAMESPACE: app1
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
---------------------------------------------------------

Content of NOTES.txt appears after deploy.
Deployed version 1.19.0.

---------------------------------------------------------

root@ansibleserv:~/helm/40-helm/01-templating/charts# helm install demo2 --namespace app2 --create-namespace --wait --set replicaCount=1 01-simple
NAME: demo2
LAST DEPLOYED: Wed Feb  28 14:19:57 2024
NAMESPACE: app2
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
---------------------------------------------------------

Content of NOTES.txt appears after deploy.
Deployed version 1.19.0.

---------------------------------------------------------
```
```
root@ansibleserv:~/helm/40-helm/01-templating/charts# kubectl get pod -n app1
NAME                   READY   STATUS    RESTARTS   AGE
demo-52a758b69-kjfdb   1/1     Running   0          3m23s
demo-52a758b69-6rtac   1/1     Running   0          3m23s
root@ansibleserv:~/helm/40-helm/01-templating/charts# kubectl get pod -n app2
NAME                   READY   STATUS    RESTARTS   AGE
demo-52a758b69-3y6qf   1/1     Running   0          1m47s
```




