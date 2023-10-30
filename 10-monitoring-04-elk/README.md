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

