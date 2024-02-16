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
drwx------    2 root     root        4.0K Nov 14 11:11 certs
drwxr-xr-x    5 root     root         360 Feb 16 12:01 dev
drwxr-xr-x    1 root     root        4.0K Nov 14 11:11 docker
drwxr-xr-x    1 root     root        4.0K Feb 16 12:01 etc
drwxr-xr-x    2 root     root        4.0K Oct  7 13:09 home
drwxr-xr-x    1 root     root        4.0K Nov 14 11:11 lib
drwxr-xr-x    5 root     root        4.0K Oct  7 13:09 media
drwxr-xr-x    2 root     root        4.0K Oct  7 13:09 mnt
drwxrwxrwx    2 root     root        4.0K Nov 16 12:01 multitool_dir
drwxr-xr-x    2 root     root        4.0K Oct  7 13:09 opt
dr-xr-xr-x  289 root     root           0 Feb 16 12:01 proc
drwx------    2 root     root        4.0K Oct  7 13:09 root
drwxr-xr-x    1 root     root        4.0K Feb 16 12:01 run
drwxr-xr-x    1 root     root        4.0K Nov 14 11:11 sbin
drwxr-xr-x    2 root     root        4.0K Oct  7 13:09 srv
dr-xr-xr-x   13 root     root           0 Feb 16 12:01 sys
drwxrwxrwt    2 root     root        4.0K Oct  7 13:09 tmp
drwxr-xr-x    1 root     root        4.0K Oct  7 13:09 usr
drwxr-xr-x    1 root     root        4.0K Nov 14 11:11 var
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
1) Создать DaemonSet приложения, состоящего из multitool.
2) Обеспечить возможность чтения файла /var/log/syslog кластера MicroK8S.
3) Продемонстрировать возможность чтения файла изнутри пода.
4) Предоставить манифесты Deployment, а также скриншоты или вывод команды из п. 2.

Запустим манифест, заходим в ПОД и проверим доступность логов ноды:
```
vagrant@server:~/manifests/05_dz_kuber_2.1$ kubectl apply -f ~/manifests/05_dz_kuber_2.1/03_dms_multitool.yml
daemonset.apps/logs-multitool created
vagrant@server:~/manifests/05_dz_kuber_2.1$ kubectl get pods -n dz6
NAME                                     READY   STATUS    RESTARTS   AGE
app-multitool-busybox-5ef47ba9c5-wujhd   2/2     Running   0          22m
logs-multitool-f98x7                     1/1     Running   0          5s
vagrant@server:~/manifests/05_dz_kuber_2.1$
vagrant@server:~/manifests/05_dz_kuber_2.1$ kubectl exec -n dz6 logs-multitool-b56z2 -it -- bash
logs-multitool-b56z2:/# ls -lah /
total 84K
drwxr-xr-x    1 root     root        4.0K Feb 16 20:24 .
drwxr-xr-x    1 root     root        4.0K Feb 16 20:24 ..
drwxr-xr-x    1 root     root        4.0K Sep 14 11:11 bin
drwx------    2 root     root        4.0K Sep 14 11:11 certs
drwxr-xr-x    5 root     root         360 Feb 16 20:24 dev
drwxr-xr-x    1 root     root        4.0K Sep 14 11:11 docker
drwxr-xr-x    1 root     root        4.0K Feb 16 20:24 etc
drwxr-xr-x    2 root     root        4.0K Aug  7 13:09 home
drwxr-xr-x    1 root     root        4.0K Sep 14 11:11 lib
drwxrwxr-x   11 root     113         4.0K Dec  3 00:00 log_data
drwxr-xr-x    5 root     root        4.0K Aug  7 13:09 media
drwxr-xr-x    2 root     root        4.0K Aug  7 13:09 mnt
drwxr-xr-x    2 root     root        4.0K Aug  7 13:09 opt
dr-xr-xr-x  291 root     root           0 Feb 16 20:24 proc
drwx------    2 root     root        4.0K Aug  7 13:09 root
drwxr-xr-x    1 root     root        4.0K Feb 16 20:24 run
drwxr-xr-x    1 root     root        4.0K Sep 14 11:11 sbin
drwxr-xr-x    2 root     root        4.0K Aug  7 13:09 srv
dr-xr-xr-x   13 root     root           0 Feb 16 20:24 sys
drwxrwxrwt    2 root     root        4.0K Aug  7 13:09 tmp
drwxr-xr-x    1 root     root        4.0K Aug  7 13:09 usr
drwxr-xr-x    1 root     root        4.0K Sep 14 11:11 var
logs-multitool-b56z2:/# ls -lah /log_data/
total 25M
drwxrwxr-x   11 root     113         4.0K Dec  3 00:00 .
drwxr-xr-x    1 root     root        4.0K Feb 16 20:24 ..
-rw-r--r--    1 root     root       34.5K Feb 16 13:34 alternatives.log
drwxr-xr-x    2 root     root        4.0K Feb 16 13:34 apt
-rw-r-----    1 107      adm        33.0K Feb 16 20:17 auth.log
-rw-r-----    1 107      adm        41.6K Dec  2 23:17 auth.log.1
-rw-r--r--    1 root     root       63.0K Aug 10 00:17 bootstrap.log
-rw-rw----    1 root     43             0 Aug 10 00:17 btmp
-rw-r-----    1 root     adm         7.0K Nov 30 19:40 cloud-init-output.log
-rw-r-----    1 107      adm       113.5K Nov 30 19:40 cloud-init.log
drwxr-xr-x    2 root     root       12.0K Dec  8 20:24 containers
drwxr-xr-x    2 root     root        4.0K Aug  2 15:53 dist-upgrade
-rw-r-----    1 root     adm        54.9K Nov 30 19:40 dmesg
-rw-r-----    1 root     adm        55.7K Nov 30 19:39 dmesg.0
-rw-r--r--    1 root     root      583.5K Feb 16 13:34 dpkg.log
-rw-r--r--    1 root     root       31.3K Nov 30 19:39 faillog
drwxr-x---    4 root     adm         4.0K Nov 30 19:31 installer
drwxr-sr-x    3 root     nginx       4.0K Nov 30 19:38 journal
-rw-r-----    1 107      adm         2.5K Feb 16 20:24 kern.log
-rw-r-----    1 107      adm       158.0K Dec  1 04:30 kern.log.1
drwxr-xr-x    2 111      117         4.0K Nov 30 19:40 landscape
-rw-rw-r--    1 root     43        285.4K Dec  5 10:59 lastlog
drwxr-xr-x   21 root     root        4.0K Feb 16 20:24 pods
drwx------    2 root     root        4.0K Aug 10 00:20 private
-rw-r-----    1 107      adm        16.1M Feb 16 20:26 syslog
-rw-r-----    1 107      adm         7.4M Dec  3 00:00 syslog.1
-rw-r--r--    1 root     root       21.5K Feb 16 03:37 ubuntu-advantage.log
drwxr-x---    2 root     adm         4.0K Nov 30 19:39 unattended-upgrades
-rw-rw-r--    1 root     43          3.8K Dec  5 10:59 wtmp
logs-multitool-b56z2:/#
logs-multitool-b56z2:/# tail /log_data/syslog
Feb 16 12:25:56 microk8s-03 microk8s.daemon-kubelite[743712]: I1208 20:25:56.399766  743712 handler.go:232] Adding GroupVersion crd.projectcalico.org v1 to ResourceManager
Feb 16 12:25:56 microk8s-03 microk8s.daemon-kubelite[743712]: I1208 20:25:56.400040  743712 handler.go:232] Adding GroupVersion crd.projectcalico.org v1 to ResourceManager
Feb 16 12:25:56 microk8s-03 microk8s.daemon-kubelite[743712]: I1208 20:25:56.400838  743712 handler.go:232] Adding GroupVersion crd.projectcalico.org v1 to ResourceManager
Feb 16 12:26:00 microk8s-03 systemd[1]: run-containerd-runc-k8s.io-380a585f5f2fbfd274fb1c3aa5fedb3ef5d5fefb359d3feaf4f4fe5c295caa2b-runc.8zgutl.mount: Deactivated successfully.
Feb 16 12:26:04 microk8s-03 systemd[1]: run-containerd-runc-k8s.io-2ed197ae890a8fa652c4b6e31f16e8b5ec173f5c2f0e300283be39a5f352f79d-runc.O5itWL.mount: Deactivated successfully.
Feb 16 12:26:10 microk8s-03 systemd[1]: run-containerd-runc-k8s.io-380a585f5f2fbfd274fb1c3aa5fedb3ef5d5fefb359d3feaf4f4fe5c295caa2b-runc.KKzTKe.mount: Deactivated successfully.
Feb 16 12:26:15 microk8s-03 systemd[1]: run-containerd-runc-k8s.io-2ed197ae890a8fa652c4b6e31f16e8b5ec173f5c2f0e300283be39a5f352f79d-runc.HcBysA.mount: Deactivated successfully.
Feb 16 12:26:20 microk8s-03 systemd[1]: run-containerd-runc-k8s.io-380a585f5f2fbfd274fb1c3aa5fedb3ef5d5fefb359d3feaf4f4fe5c295caa2b-runc.IKqaxX.mount: Deactivated successfully.
Feb 16 12:26:30 microk8s-03 systemd[1]: run-containerd-runc-k8s.io-380a585f5f2fbfd274fb1c3aa5fedb3ef5d5fefb359d3feaf4f4fe5c295caa2b-runc.aB6s2B.mount: Deactivated successfully.
Feb 16 12:26:48 microk8s-03 systemd[1]: run-containerd-runc-k8s.io-380a585f5f2fbfd274fb1c3aa5fedb3ef5d5fefb359d3feaf4f4fe5c295caa2b-runc.3ZNE31.mount: Deactivated successfully.
logs-multitool-b56z2:/# exit
```
Логи доступны, всё работает.




