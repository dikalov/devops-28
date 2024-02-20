# Домашнее задание к занятию «Конфигурация приложений»
### Задание 1. Создать Deployment приложения и решить возникшую проблему с помощью ConfigMap. Добавить веб-страницу
1) Создать Deployment приложения, состоящего из контейнеров nginx и multitool.
2) Решить возникшую проблему с помощью ConfigMap.
3) Продемонстрировать, что pod стартовал и оба конейнера работают.
4) Сделать простую веб-страницу и подключить её к Nginx с помощью ConfigMap. Подключить Service и показать вывод curl или в браузере.
5) Предоставить манифесты, а также скриншоты или вывод необходимых команд.

Из предыдущего задания можно взять Deployment приложения, состоящего из контейнеров busybox и multitool. Для проверки созданной страницы создадим Service(нужен выполнения условия поднятия контейнера multitool). И создадим ConfigMap для добавления моего файла - index.html

Манифесты:

[Файл Deployment](https://github.com/dikalov/devops-28/blob/main/kuber-homeworks/2.3%20/file%20/ConfigMapDep.yaml)

[Файл Service](https://github.com/dikalov/devops-28/blob/main/kuber-homeworks/2.3%20/file%20/myservice.yaml)

[Файл configmap](https://github.com/dikalov/devops-28/blob/main/kuber-homeworks/2.3%20/file%20/index-html-configmap.yaml)

```
$ kubectl apply -f file/myservice.yaml 
service/myservice created

$ kubectl get service
NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                         AGE
kubernetes   ClusterIP   10.152.183.1     <none>        443/TCP                         38d
myservice    NodePort    10.152.183.153   <none>        80:32000/TCP                    10s

$ kubectl apply -f file/index-html-configmap.yaml 
configmap/index-html-configmap created

$ kubectl get cm
NAME                   DATA   AGE
kube-root-ca.crt       1      38d
index-html-configmap   1      13s

$ kubectl apply -f file/ConfigMapDep.yaml 
deployment.apps/myapp-pod created

$ kubectl get pod
NAME                         READY   STATUS    RESTARTS   AGE
myapp-pod-5bb5fbc745-tn2nx   1/1     Running   0          20s
```


