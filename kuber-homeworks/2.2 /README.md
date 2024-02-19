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
[Файл Deployment2](https://github.com/dikalov/devops-28/blob/main/kuber-homeworks/2.2%20/file%20/deployment2.yaml)
```
$ kubectl apply -f file/deployment2.yaml 
deployment.apps/nfs-deployment created

$ kubectl apply -f file/pvc-vol2.yaml 
persistentvolumeclaim/my-pvc-nfs created

$ kubectl apply -f file/SCl.yaml 
storageclass.storage.k8s.io/my-nfs1 created

$kubectl get pvc
NAME         STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
my-pvc-nfs   Bound    pvc-2765836c-c55a-84b6-c69c-3b231d15e1c2   1Gi        RWO            my-nfs1        50s

$ kubectl get pv
NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                STORAGECLASS   REASON   AGE
pvc-2765836c-c55a-84b6-c69c-3b231d15e1c2   1Gi        RWO            Delete           Bound    default/my-pvc-nfs   my-nfs1                 12s
```
```
$ kubectl get pod
NAME                              READY   STATUS    RESTARTS   AGE
nfs-deployment-6e73392a69-ndza6   1/1     Running   0          8m26s

$ kubectl describe  pod nfs-deployment-6e73392a69-ndza6
Name:         nfs-deployment-6e73392a69-ndza6
Namespace:    default
Priority:     0
Node:         microk8s/192.168.1.51
Start Time:   Mon, 19 Feb 2024 16:20:23 +0300
Labels:       app=multitool-b
              pod-template-hash=7f64997d46
Annotations:  cni.projectcalico.org/containerID: sk3m496gseuwtc6295mg75j3hb45ha72hd65gen4hd65hsjfl578snekrub34d34
              cni.projectcalico.org/podIP: 10.1.151.212/32
              cni.projectcalico.org/podIPs: 10.1.151.212/32
Status:       Running
IP:           10.1.151.212
IPs:
  IP:           10.1.151.212
Controlled By:  ReplicaSet/nfs-deployment-6e73392a69
Containers:
  network-multitool:
    Container ID:   containerd://he75is64n5bdgw53h4kcm68q0su3h3fs5q239d7ru5yslg75jzmfhs65djvn37r46
    Image:          wbitt/network-multitool
    Image ID:       docker.io/wbitt/network-multitool@sha256:48undj5yfhfhfjutigjdhuy67689ehncf7r7h5f8i3nbhw78s7d9g9gjtjtjgkv5
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Mon, 19 Feb 2024 16:24:56 +0300
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
      /static from my-vol-pvc (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-rwq6t (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  my-vol-pvc:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  my-pvc-nfs
    ReadOnly:   false
  kube-api-access-rwq6t:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3701
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   Burstable
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type     Reason       Age                  From     Message
  ----     ------       ----                 ----     -------
  Warning  FailedMount  8m12s                kubelet  Unable to attach or mount volumes: unmounted volumes=[my-vol-pvc], unattached volumes=[my-vol-pvc kube-api-access-swe6e]: timed out waiting for the condition
  Warning  FailedMount  8m12s (x9 over 11m)  kubelet  MountVolume.MountDevice failed for volume "pvc-2765836c-c55a-84b6-c69c-3b231d15e1c2" : kubernetes.io/csi: attacher.MountDevice failed to create newCsiDriverClient: driver name nfs.c
si.k8s.io not found in the list of registered CSI drivers
  Normal   Pulling      6m54s                kubelet  Pulling image "wbitt/network-multitool"
  Normal   Pulled       6m40s                kubelet  Successfully pulled image "wbitt/network-multitool" in 9.947509361s (9.947515362s including waiting)
  Normal   Created      6m40s                kubelet  Created container network-multitool
  Normal   Started      6m40s                kubelet  Started container network-multitool
```
3) Продемонстрировать возможность чтения и записи файла изнутри пода.

Создадим файл на ноде в директории где автоматически создался pv. И потом проверим доступность внутри пода.
```
$ kubectl exec nfs-deployment-6e73392a69-ndza6 -c network-multitool -it -- sh
/ # cat static/test.txt
test 123456
```
4) Предоставить манифесты, а также скриншоты или вывод необходимых команд.

[Файл deployment2](https://github.com/dikalov/devops-28/blob/main/kuber-homeworks/2.2%20/file%20/deployment2.yamlhttps://github.com/dikalov/devops-28/blob/main/kuber-homeworks/2.2%20/file%20/deployment2.yaml)


[Файл pvc-vol2](https://github.com/dikalov/devops-28/blob/main/kuber-homeworks/2.2%20/file%20/pvc-vol2.yaml)






