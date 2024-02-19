# Домашнее задание к занятию «Хранение в K8s. Часть 2»
### Задание 1. Создать Deployment приложения, использующего локальный PV, созданный вручную.
1) Создать Deployment приложения, состоящего из контейнеров busybox и multitool.
2) Создать PV и PVC для подключения папки на локальной ноде, которая будет использована в поде.
3) Продемонстрировать, что multitool может читать файл, в который busybox пишет каждые пять секунд в общей директории.
4) Удалить Deployment и PVC. Продемонстрировать, что после этого произошло с PV. Пояснить, почему.
5) Продемонстрировать, что файл сохранился на локальном диске ноды. Удалить PV. Продемонстрировать что произошло с файлом после удаления PV. Пояснить, почему.
6) Предоставить манифесты, а также скриншоты или вывод необходимых команд.
```
$ kubectl get deployment
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
pvc-deployment   1/1     1            1           21m
$ kubectl get pod
NAME                              READY   STATUS    RESTARTS   AGE
pvc-deployment-38e52b74bf-dvdw7   2/2     Running   0          9m27s
```
[Файл Deployment](https://github.com/dikalov/devops-28/blob/main/kuber-homeworks/2.2%20/file%20/deployment.yaml)
```
$ kubectl get pv
NAME   CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM             STORAGECLASS   REASON   AGE
pv     2Gi        RWO            Retain           Bound    default/pvc-vol                           20m
```
[Файл pv-vol](https://github.com/dikalov/devops-28/blob/main/kuber-homeworks/2.2%20/file%20/pv-vol.yaml)
```
$ kubectl get pvc
NAME      STATUS   VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS   AGE
pvc-vol   Bound    pv       2Gi        RWO                           20m
```
[Файл pvc-vol](https://github.com/dikalov/devops-28/blob/main/kuber-homeworks/2.2%20/file%20/pvc-vol.yaml)
```
$ kubectl get pvc
NAME      STATUS   VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS   AGE
pvc-vol   Bound    pv       2Gi        RWO                           21m
```





