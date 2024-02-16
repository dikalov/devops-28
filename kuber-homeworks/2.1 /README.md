# Домашнее задание к занятию «Хранение в K8s. Часть 1»
### Задание 1. Создать Deployment приложения, состоящего из двух контейнеров и обменивающихся данными.
1) Создать Deployment приложения, состоящего из контейнеров busybox и multitool.
2) Сделать так, чтобы busybox писал каждые пять секунд в некий файл в общей директории.
3) Обеспечить возможность чтения файла контейнером multitool.
4) Продемонстрировать, что multitool может читать файл, который периодоически обновляется.
5) Предоставить манифесты Deployment в решении, а также скриншоты или вывод команды из п. 4.

Создадим пространство имен:
```
vagrant@server:~/manifests/05_dz_kuber_2.1$ kubectl apply -f ~/manifests/05_dz_kuber_2.1/01_namespace.yml
namespace/dz6 created
vagrant@server:~/manifests/05_dz_kuber_2.1$ kubectl get ns
NAME              STATUS   AGE
kube-system       Active   4d11h
kube-public       Active   4d11h
kube-node-lease   Active   4d11h
default           Active   4d11h
lesson4           Active   4d11h
ingress           Active   4d11h
dz5               Active   2d22h
lesson5           Active   2d18h
dz6               Active   15s
```
Запустим манифест и проверим доступ общих файлов:
```
vagrant@server:~/manifests/05_dz_kuber_2.1$ kubectl apply -f ~/manifests/05_dz_kuber_2.1/02_deploy_busybox_multitool.yml
deployment.apps/app-multitool-busybox created
vagrant@server:~/manifests/05_dz_kuber_2.1$ kubectl get pods -n dz6
NAME                                     READY   STATUS    RESTARTS   AGE
app-multitool-busybox-5ef47ba9c5-wujhd   2/2     Running   0          11s
kubectl exec -n dz6  app-multitool-busybox-5ef47ba9c5-wujhd -c multitool -it -- bash
app-multitool-busybox-5ef47ba9c5-wujhd:/# ls -lah /
total 84K
drwxr-xr-x    1 root     root        4.0K Feb 16 12:01 .
drwxr-xr-x    1 root     root        4.0K Feb 16 12:01 ..
drwxr-xr-x    1 root     root        4.0K Sep 14 11:11 bin
drwx------    2 root     root        4.0K Sep 14 11:11 certs
drwxr-xr-x    5 root     root         360 Feb 16 12:01 dev
drwxr-xr-x    1 root     root        4.0K Sep 14 11:11 docker
drwxr-xr-x    1 root     root        4.0K Feb 16 12:01 etc
drwxr-xr-x    2 root     root        4.0K Aug  7 13:09 home
drwxr-xr-x    1 root     root        4.0K Sep 14 11:11 lib
drwxr-xr-x    5 root     root        4.0K Aug  7 13:09 media
drwxr-xr-x    2 root     root        4.0K Aug  7 13:09 mnt
drwxrwxrwx    2 root     root        4.0K Feb 16 12:01 multitool_dir
drwxr-xr-x    2 root     root        4.0K Aug  7 13:09 opt
dr-xr-xr-x  289 root     root           0 Feb 16 12:01 proc
drwx------    2 root     root        4.0K Aug  7 13:09 root
drwxr-xr-x    1 root     root        4.0K Feb 16 12:01 run
drwxr-xr-x    1 root     root        4.0K Sep 14 11:11 sbin
drwxr-xr-x    2 root     root        4.0K Aug  7 13:09 srv
dr-xr-xr-x   13 root     root           0 Feb 16 12:01 sys
drwxrwxrwt    2 root     root        4.0K Aug  7 13:09 tmp
drwxr-xr-x    1 root     root        4.0K Aug  7 13:09 usr
drwxr-xr-x    1 root     root        4.0K Sep 14 11:11 var
app-multitool-busybox-5ef47ba9c5-wujhd:/# ls -lah /multitool_dir/
total 12K
drwxrwxrwx    2 root     root        4.0K Feb 16 12:01 .
drwxr-xr-x    1 root     root        4.0K Feb 16 12:01 ..
-rw-r--r--    1 root     root        1.9K Feb 16 12:05 date.log
app-multitool-busybox-5ef47ba9c5-wujhd:/# cat /multitool_dir/date.log
pinhhhh date = Fri Feb 16 12:01:59 UTC 2024
pinhhhh date = Fri Feb 16 12:02:04 UTC 2024
pinhhhh date = Fri Feb 16 12:02:09 UTC 2024
pinhhhh date = Fri Feb 16 12:02:14 UTC 2024
pinhhhh date = Fri Feb 16 12:02:19 UTC 2024
pinhhhh date = Fri Feb 16 12:02:24 UTC 2024
pinhhhh date = Fri Feb 16 12:02:29 UTC 2024
pinhhhh date = Fri Feb 16 12:02:34 UTC 2024
pinhhhh date = Fri Feb 16 12:02:39 UTC 2024
pinhhhh date = Fri Feb 16 12:02:44 UTC 2024
pinhhhh date = Fri Feb 16 12:02:49 UTC 2024
pinhhhh date = Fri Feb 16 12:02:54 UTC 2024
pinhhhh date = Fri Feb 16 12:02:59 UTC 2024
pinhhhh date = Fri Feb 16 12:03:04 UTC 2024
pinhhhh date = Fri Feb 16 12:03:09 UTC 2024
pinhhhh date = Fri Feb 16 12:03:14 UTC 2024
pinhhhh date = Fri Feb 16 12:03:19 UTC 2024
pinhhhh date = Fri Feb 16 12:03:24 UTC 2024
pinhhhh date = Fri Feb 16 12:03:29 UTC 2024
pinhhhh date = Fri Feb 16 12:03:34 UTC 2024
pinhhhh date = Fri Feb 16 12:03:39 UTC 2024
pinhhhh date = Fri Feb 16 12:03:44 UTC 2024
pinhhhh date = Fri Feb 16 12:03:49 UTC 2024
pinhhhh date = Fri Feb 16 12:03:54 UTC 2024
pinhhhh date = Fri Feb 16 12:03:59 UTC 2024
pinhhhh date = Fri Feb 16 12:04:04 UTC 2024
pinhhhh date = Fri Feb 16 12:04:09 UTC 2024
pinhhhh date = Fri Feb 16 12:04:14 UTC 2024
pinhhhh date = Fri Feb 16 12:04:19 UTC 2024
pinhhhh date = Fri Feb 16 12:04:24 UTC 2024
pinhhhh date = Fri Feb 16 12:04:29 UTC 2024
pinhhhh date = Fri Feb 16 12:04:34 UTC 2024
pinhhhh date = Fri Feb 16 12:04:39 UTC 2024
pinhhhh date = Fri Feb 16 12:04:44 UTC 2024
pinhhhh date = Fri Feb 16 12:04:49 UTC 2024
pinhhhh date = Fri Feb 16 12:04:54 UTC 2024
pinhhhh date = Fri Feb 16 12:04:59 UTC 2024
pinhhhh date = Fri Feb 16 12:05:04 UTC 2024
pinhhhh date = Fri Feb 16 12:05:09 UTC 2024
pinhhhh date = Fri Feb 16 12:05:14 UTC 2024
pinhhhh date = Fri Feb 16 12:05:19 UTC 2024
pinhhhh date = Fri Feb 16 12:05:24 UTC 2024
pinhhhh date = Fri Feb 16 12:05:29 UTC 2024
pinhhhh date = Fri Feb 16 12:05:34 UTC 2024
pinhhhh date = Fri Feb 16 12:05:39 UTC 2024
pinhhhh date = Fri Feb 16 12:05:44 UTC 2024
pinhhhh date = Fri Feb 16 12:05:49 UTC 2024
pinhhhh date = Fri Feb 16 12:05:54 UTC 2024
pinhhhh date = Fri Feb 16 12:05:59 UTC 2024
app-multitool-busybox-5ef47ba9c5-wujhd:/# exit
exit
```
Всё хорошо!
### Задание 2. Создать DaemonSet приложения, которое может прочитать логи ноды.




