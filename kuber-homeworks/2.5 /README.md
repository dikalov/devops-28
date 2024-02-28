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








