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


