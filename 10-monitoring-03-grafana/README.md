# Домашнее задание к занятию 14 «Средство визуализации Grafana»
### Задание 1.
1) Используя директорию help внутри этого домашнего задания, запустите связку prometheus-grafana.
2) Зайдите в веб-интерфейс grafana, используя авторизационные данные, указанные в манифесте docker-compose.
3) Подключите поднятый вами prometheus, как источник данных.
4) Решение домашнего задания — скриншот веб-интерфейса grafana со списком подключенных Datasource.

![image](https://github.com/dikalov/devops-28/assets/126553776/54f5266a-8b7e-4481-b475-4e765cd1a765)

### Задание 2.
Создайте Dashboard и в ней создайте Panels:

утилизация CPU для nodeexporter (в процентах, 100-idle);
```
100 - (avg by(instance) (rate(node_cpu_seconds_total{mode="idle", instance=~"nodeexporter:9100"}[1m])) * 100)
```
CPULA 1/5/15;
```
(avg by(instance) (node_load1{instance=~"nodeexporter:9100"}) * 100) / count by(instance) (count by(cpu, instance) (node_cpu_seconds_total{instance=~"nodeexporter:9100"}))
(avg by(instance) (node_load5{instance=~"nodeexporter:9100"}) * 100) / count by(instance) (count by(cpu, instance) (node_cpu_seconds_total{instance=~"nodeexporter:9100"}))
(avg by(instance) (node_load15{instance=~"nodeexporter:9100"}) * 100) / count by(instance) (count by(cpu, instance) (node_cpu_seconds_total{instance=~"nodeexporter:9100"}))
```
количество свободной оперативной памяти;
```
avg by (instance) (100 * ((avg_over_time(node_memory_MemFree_bytes{instance=~"nodeexporter:9100"}[5m]) + avg_over_time(node_memory_Cached_bytes{instance=~"nodeexporter:9100"}[5m]) + avg_over_time(node_memory_Buffers_bytes{instance=~"nodeexporter:9100"}[5m])) / avg_over_time(node_memory_MemTotal_bytes{instance=~"nodeexporter:9100"}[5m])))
```
количество места на файловой системе.
```
100 - (node_filesystem_avail_bytes{instance=~"nodeexporter:9100",mountpoint="/"} * 100 / node_filesystem_size_bytes{instance=~"nodeexporter:9100",mountpoint="/"})
```
Для решения этого задания приведите promql-запросы для выдачи этих метрик, а также скриншот получившейся Dashboard.
![image](https://github.com/dikalov/devops-28/assets/126553776/5b504280-7764-4107-a12a-962c1f58f3fa)

### Задание 3.
Создайте для каждой Dashboard подходящее правило alert — можно обратиться к первой лекции в блоке «Мониторинг».
В качестве решения задания приведите скриншот вашей итоговой Dashboard.
![image](https://github.com/dikalov/devops-28/assets/126553776/19759f90-0c2d-41a3-83b6-0727fd92780a)

### Задание 4.
Сохраните ваш Dashboard.Для этого перейдите в настройки Dashboard, выберите в боковом меню «JSON MODEL». Далее скопируйте отображаемое json-содержимое в отдельный файл и сохраните его.
В качестве решения задания приведите листинг этого файла.

[Файл dashboard.json](https://github.com/dikalov/devops-28/blob/main/10-monitoring-03-grafana/dashboard.json)

