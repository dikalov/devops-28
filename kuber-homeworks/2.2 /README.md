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
Проверим на network-multitool файл success.txt.
```
$ kubectl exec pvc-deployment-67f79c66cd-dzdq7 -c network-multitool -it -- sh
/ # cat static/success.txt
Success!
Success!
Success!
Success!
```
Проверим файл на Ноде, он расположен по пути path: /data/pv и удалим deployment.
```
$ kubectl delete deployment pvc-deployment
deployment.apps "pvc-deployment" deleted


$ kubectl get deployment
No resources found in default namespace.
$ kubectl get pod
No resources found in default namespace.
```
Можно проверить, что pv и pvc остались.
```
$ kubectl get pvc
NAME      STATUS   VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS   AGE
pvc-vol   Bound    pv       2Gi        RWO                           40m

$ kubectl get pv
NAME   CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM             STORAGECLASS   REASON   AGE
pv     2Gi        RWO            Retain           Bound    default/pvc-vol                           39m
```
После удаления deployment файлы pv остаются на локальной ноде. Можно объяснить, почему это происходит.

Файлы остаются так как никуда не делись pv и pvc. Также при конфигурировании pv используется режим ReclaimPolicy: Retain при котором "Retain - после удаления PV ресурсы из внешних провайдеров автоматически не удаляются". Даже после удаления pv файлы также останутся.

### Задание 2. Создать Deployment приложения, которое может хранить файлы на NFS с динамическим созданием PV.
1) Включить и настроить NFS-сервер на MicroK8S.
2) Создать Deployment приложения состоящего из multitool, и подключить к нему PV, созданный автоматически на сервере NFS.
3) Продемонстрировать возможность чтения и записи файла изнутри пода.
4) Предоставить манифесты, а также скриншоты или вывод необходимых команд.








