# Домашнее задание к занятию 15 «Система сбора логов Elastic Stack»
### Задание 1.
Вам необходимо поднять в докере и связать между собой:

elasticsearch (hot и warm ноды); logstash; kibana; filebeat.

Logstash следует сконфигурировать для приёма по tcp json-сообщений.
```
vagrant@server1:~/an-home/docker$ docker ps -a
CONTAINER ID   IMAGE                    COMMAND                  CREATED          STATUS          PORTS                                                                                            NAMES
74hfbjc77rhd   kibana:8.7.0             "/bin/tini -- /usr/l…"   3 seconds ago    Up 2 seconds    0.0.0.0:5601->5601/tcp, :::5601->5601/tcp                                                        kibana
5d6ryd63y4gf   elastic/filebeat:8.7.0   "/usr/bin/tini -- /u…"   13 minutes ago   Up 13 minutes                                                                                                    filebeat
4jfu75746r6h   logstash:8.7.0           "/usr/local/bin/dock…"   13 minutes ago   Up 13 minutes   0.0.0.0:5044->5044/tcp, :::5044->5044/tcp, 0.0.0.0:5046->5046/tcp, :::5046->5046/tcp, 9600/tcp   logstash
2syc6hhd6778   elasticsearch:8.7.0      "/bin/tini -- /usr/l…"   13 minutes ago   Up 13 minutes   0.0.0.0:9200->9200/tcp, :::9200->9200/tcp, 9300/tcp                                              es-hot
9sg647fhf773   elasticsearch:8.7.0      "/bin/tini -- /usr/l…"   13 minutes ago   Up 13 minutes   9200/tcp, 9300/tcp                                                                               es-warm
```
![image](https://github.com/dikalov/devops-28/assets/126553776/9fa5910a-59e2-4dea-87a9-5ffddb9da527)

### Задание 2.
Перейдите в меню создания index-patterns в kibana и создайте несколько index-patterns из имеющихся.

Перейдите в меню просмотра логов в kibana (Discover) и самостоятельно изучите, как отображаются логи и как производить поиск по логам.

В манифесте директории help также приведенно dummy-приложение, которое генерирует рандомные события в stdout-контейнера. Эти логи должны порождать индекс logstash-* в elasticsearch. Если этого индекса нет — воспользуйтесь советами и источниками из раздела «Дополнительные ссылки» этого задания.

![image](https://github.com/dikalov/devops-28/assets/126553776/06c26780-b5de-44a8-bc1f-cc808c06b8c6)

