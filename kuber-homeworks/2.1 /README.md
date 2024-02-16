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



