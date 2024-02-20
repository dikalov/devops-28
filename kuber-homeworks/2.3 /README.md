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
```
$ kubectl describe pod myapp-pod-6cb7edc758-we2dn
Name:         myapp-pod-6cb7edc758-we2dn
Namespace:    default
Priority:     0
Node:         microk8s/192.168.1.91
Start Time:   Tue, 20 Feb 2024 14:35:51 +0300
Labels:       app=myapp
              pod-template-hash=6cb7edc758
Annotations:  cni.projectcalico.org/containerID: 6593hc885743hs4736wy47g588qh366rt32676g992hst4653bv264wj733jhg36
              cni.projectcalico.org/podIP: 10.1.132.212/32
              cni.projectcalico.org/podIPs: 10.1.132.212/32
Status:       Running
IP:           10.1.132.212
IPs:
  IP:           10.1.132.212
Controlled By:  ReplicaSet/myapp-pod-5bb5fbc745
Init Containers:
  init-myservice:
    Container ID:  containerd://deef8650813540c4b342bc27a2b5795d1a09c16e378ade57dhh4729ngh45gsf4
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
      Started:      Tue, 20 Feb 2024 14:35:56 +0300
      Finished:     Tue, 20 Feb 2024 14:35:56 +0300
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-6h2b2 (ro)
Containers:
  network-multitool:
    Container ID:   containerd://be469c26ac4d72641408d4bb37dda03fdcc4fa9a2bf5816f21072d1436bc640a
    Image:          wbitt/network-multitool
    Image ID:       docker.io/wbitt/network-multitool@sha256:82a5ea955024390d6b43847g2ccc75c98b481bf00e57c13e9a9cc1458eb92652
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Tue, 20 Feb 2024 14:35:59 +0300
    Ready:          True
    Restart Count:  0
    Limits:
      cpu:     200m
      memory:  512Mi
    Requests:
      cpu:        100m
      memory:     256Mi
    Environment:  <none>
    Mounts:
      /usr/share/nginx/html/ from nginx-index-file (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-6h2b2 (ro)
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
  kube-api-access-7r5c6:
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
```
Проверим curl
```
$ curl 192.168.1.91:32000
<html>
<h1>Welcome</h1>
</br>
<h1>Hi! This is a configmap Index file </h1>
</html
```
И в браузере тоже:

![image](https://github.com/dikalov/devops-28/assets/126553776/566d38fa-485e-476b-a128-37be1d53f5d9)



