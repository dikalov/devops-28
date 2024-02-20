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
kubernetes   ClusterIP   10.151.163.1     <none>        443/TCP                         17d
myservice    NodePort    10.151.163.181   <none>        80:32000/TCP                    31s

$ kubectl apply -f file/index-html-configmap.yaml 
configmap/index-html-configmap created

$ kubectl get cm
NAME                   DATA   AGE
kube-root-ca.crt       1      16d
index-html-configmap   1      41s

$ kubectl apply -f file/ConfigMapDep.yaml 
deployment.apps/myapp-pod created

$ kubectl get pod
NAME                         READY   STATUS    RESTARTS   AGE
myapp-pod-6cb7edc758-we2dn   1/1     Running   0          42s
```


