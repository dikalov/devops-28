# Домашнее задание к занятию "13.Системы мониторинга"
### 1. Вас пригласили настроить мониторинг на проект. На онбординге вам рассказали, что проект представляет из себя платформу для вычислений с выдачей текстовых отчетов, которые сохраняются на диск. Взаимодействие с платформой осуществляется по протоколу http. Также вам отметили, что вычисления загружают ЦПУ. Какой минимальный набор метрик вы выведите в мониторинг и почему?

a) Мониторинг по оценке работоспособности ПО - мониторинг общего количества (http/https) запросов к данному приложению, количество неудачных ответов пользователям (ошибки 400/404), доступность ПО из различных регионов (при необходимости).

b) Мониторинг по оценке работоспособности оборудования - 

CPU: общая загрузка ЦПУ, часть нагрузки ЦПУ оказываемой непосредственно приложением, или каким либо важным рабочим процессом данного приложения

RAM: количество занятой и оставшейся памяти

HDD: нагрузка на диск, остаточная ёмкость, состояние диска (smart), график заполняемости свободного места на диске

LAN: количество проходящего трафика

c) Бизнес мониторинг - количество успешно выданных отчетов, Количество неудачных отчетов, количество отчетов в работе, мониторинг среднего времени формирования отчетов

d) Мониторинг безопасности ИС - нестандартное количество попыток авторизаций пользователя в промежуток времени, при отсутствии временного ограничения после нескольких неудачных авторизаций пользователя, актуальность существующих сертификатов, нестандартное количество запросов на формирование отчетов от одного пользователя в промежуток времени, если приложение используется в пределах страны, тогда можно отслеживать трафик приходящий из нестандартных регионов.

### 2. Менеджер продукта посмотрев на ваши метрики сказал, что ему непонятно что такое RAM/inodes/CPUla. Также он сказал, что хочет понимать, насколько мы выполняем свои обязанности перед клиентами и какое качество обслуживания. Что вы можете ему предложить?

Для того чтобы привести метрики в более простой и читаемый вид для менеджеров, необходимо утвердить SLA в рамках которого будут указаны SLO для тех или иных метрик. После чего менеджерам будет проще ориентироваться в состоянии продукта, так как их будут интересовать только разницы значений SLO и SLI. Если значения SLI той или иной метрики не противоречат установленным для неё SLO тогда проект в норме. Это позволяет менеджеру не знать для чего та или иная метрика, но позволяет им сложить общую картину состояния работоспособности проекта.

### 3. Вашей DevOps команде в этом году не выделили финансирование на построение системы сбора логов. Разработчики в свою очередь хотят видеть все ошибки, которые выдают их приложения. Какое решение вы можете предпринять в этой ситуации, чтобы разработчики получали ошибки приложения?

В основном все варианты отслеживания ошибок в логах упираются в возможности и бюджет.

Если приложение маленькое и нагрузка не высока, тогда можно использовать скрипты для вытягивания ошибок, к примеру по ключевым словам. Если приложение более востребовано и бюджеты позволяют приобрести мощности, тогда лучше воспользоваться специализированным ПО типа Sentry и др.

### 4. Вы, как опытный SRE, сделали мониторинг, куда вывели отображения выполнения SLA=99% по http кодам ответов. Вычисляете этот параметр по следующей формуле: summ_2xx_requests/summ_all_requests. Данный параметр не поднимается выше 70%, но при этом в вашей системе нет кодов ответа 5xx и 4xx. Где у вас ошибка?

В формуле из задания не используются значения кодов 1xx и 3xx

Правильный вариант формулы будет выглядеть так:  SLI = (summ_2xx_requests + summ_3xx_requests + summ_1xx_requests)/(summ_all_requests)

### 5. Опишите основные плюсы и минусы pull и push систем мониторинга.
#### Pull
Плюсы: легче контролировать подлинность данных. Есть гарантия опроса только тех агентов, которые настроены в системе мониторинга; упрощенная отладка получения данных с агентов. Так как данные запрашиваются посредством HTTP, можно самостоятельно запрашивать эти данные, используя ПО вне системы мониторинга; сервер собирает данные с агентов когда может, и если в данный момент нет свободных ресурсов - заберёт данные позже; безопасность при pull-модели гораздо выше. Не требует открытия порта сервера во вне; проще защитить трафик, т.к. часто используется HTTP/S.

Минусы: более высокие требования к ресурсам, особенно при использовании защищённых каналов связи; не работает за NAT. Надо ставить какой-нибудь прокси.

#### Push
Плюсы: настройка точек приёма метрик осуществляется на агентах, что позволяет настроить вывод метрик в несколько систем мониторирования и возможность реализовать репликацию; UDP является менее затратным способом передачи данных, вследствие чего может вырасти производительность сбора метрик; работает за NAT; более гибкая настройка отправки пакетов данных с метриками. На каждом клиенте можно задать нужный нам объем данных и частоту отправки.

Минусы: затрудняется верификация данных в системах мониторирования; эмулируя действия агента и можно размыть данные мониторинга ложной информацией; агенты могут зафлудить сервера запросами спровоцировав DDoS; протокол UDP не гарантирует доставку данных.

### 6. Какие из ниже перечисленных систем относятся к push модели, а какие к pull? А может есть гибридные?
1) Prometheus - pull
2) TICK - push
3) Zabbix - pull, push
4) VictoriaMetrics - push
5) Nagios - pull

### 7. Склонируйте себе репозиторий и запустите TICK-стэк, используя технологии docker и docker-compose.

В виде решения на это упражнение приведите скриншот веб-интерфейса ПО chronograf (http://localhost:8888).

P.S.: если при запуске некоторые контейнеры будут падать с ошибкой - проставьте им режим Z, например ./data:/var/lib:Z
![image](https://github.com/dikalov/devops-28/assets/126553776/caca6948-b5e6-451a-8e6e-ed767523f775)

### 8. Перейдите в веб-интерфейс Chronograf (http://localhost:8888) и откройте вкладку Data explorer.
Нажмите на кнопку Add a query

Изучите вывод интерфейса и выберите БД telegraf.autogen

В measurments выберите mem->host->telegraf_container_id , а в fields выберите used_percent. Внизу появится график утилизации оперативной памяти в контейнере telegraf.

Вверху вы можете увидеть запрос, аналогичный SQL-синтаксису. Поэкспериментируйте с запросом, попробуйте изменить группировку и интервал наблюдений.

Для выполнения задания приведите скриншот с отображением метрик утилизации места на диске (disk->host->telegraf_container_id) из веб-интерфейса.

![image](https://github.com/dikalov/devops-28/assets/126553776/6f59ee1e-c054-4e61-a0cd-6bb6fc1423cc)

![image](https://github.com/dikalov/devops-28/assets/126553776/5affbfd1-627d-4c95-bca0-99c9ed4c29eb)

### 9. Изучите список telegraf inputs. Добавьте в конфигурацию telegraf следующий плагин - docker:
```
[[inputs.docker]]
  endpoint = "unix:///var/run/docker.sock"
```
Дополнительно вам может потребоваться донастройка контейнера telegraf в docker-compose.yml дополнительного volume и режима privileged:
```
  telegraf:
    image: telegraf:1.4.0
    privileged: true
    volumes:
      - ./etc/telegraf.conf:/etc/telegraf/telegraf.conf:Z
      - /var/run/docker.sock:/var/run/docker.sock:Z
    links:
      - influxdb
    ports:
      - "8092:8092/udp"
      - "8094:8094"
      - "8125:8125/udp"
```
После настройке перезапустите telegraf, обновите веб интерфейс и приведите скриншотом список measurments в веб-интерфейсе базы telegraf.autogen . Там должны появиться метрики, связанные с docker.
![image](https://github.com/dikalov/devops-28/assets/126553776/e62c7e80-54d0-4cd2-9415-33c0fd454463)

![image](https://github.com/dikalov/devops-28/assets/126553776/5b882f5a-63d0-4ac6-8e4a-45bc32414513)

```
docker run -d --name=telegraf \
	-v $(pwd)/telegraf.conf:/etc/telegraf/telegraf.conf \
	-v /var/run/docker.sock:/var/run/docker.sock \
	--net=influxdb-net \
	--user telegraf:$(stat -c '%g' /var/run/docker.sock) \
	--env INFLUX_TOKEN=<your_api_key> \
	telegraf
```

Docker-compose.yml
```
version: '3'
services:
  influxdb:
    # Full tag list: https://hub.docker.com/r/library/influxdb/tags/
    build:
      context: ./images/influxdb/
      dockerfile: ./${TYPE}/Dockerfile
      args:
        INFLUXDB_TAG: ${INFLUXDB_TAG}
    image: "influxdb"
    volumes:
      # Mount for influxdb data directory
      - ./influxdb/data:/var/lib/influxdb
      # Mount for influxdb configuration
      - ./influxdb/config/:/etc/influxdb/
    ports:
      # The API for InfluxDB is served on port 8086
      - "8086:8086"
      - "8082:8082"
      # UDP Port
      - "8089:8089/udp"

  telegraf:
    # Full tag list: https://hub.docker.com/r/library/telegraf/tags/
    build:
      context: ./images/telegraf/
      dockerfile: ./${TYPE}/Dockerfile
      args:
        TELEGRAF_TAG: ${TELEGRAF_TAG}
    image: "telegraf"
    privileged: true
    user: telegraf:1001
    environment:
      HOSTNAME: "telegraf-getting-started"
    # Telegraf requires network access to InfluxDB
    links:
      - influxdb
    volumes:
      # Mount for telegraf configuration
      - ./telegraf/:/etc/telegraf/:Z
      # Mount for Docker API access
      - /var/run/docker.sock:/var/run/docker.sock:Z
    depends_on:
      - influxdb
    ports:
      - "8092:8092/udp"
      - "8094:8094"
      - "8125:8125/udp"

  kapacitor:
  # Full tag list: https://hub.docker.com/r/library/kapacitor/tags/
    build:
      context: ./images/kapacitor/
      dockerfile: ./${TYPE}/Dockerfile
      args:
        KAPACITOR_TAG: ${KAPACITOR_TAG}
    image: "kapacitor"
    volumes:
      # Mount for kapacitor data directory
      - ./kapacitor/data/:/var/lib/kapacitor:Z
      # Mount for kapacitor configuration
      - ./kapacitor/config/:/etc/kapacitor/:Z
    # Kapacitor requires network access to Influxdb
    links:
      - influxdb
    ports:
      # The API for Kapacitor is served on port 9092
      - "9092:9092"

  chronograf:
    # Full tag list: https://hub.docker.com/r/library/chronograf/tags/
    build:
      context: ./images/chronograf
      dockerfile: ./${TYPE}/Dockerfile
      args:
        CHRONOGRAF_TAG: ${CHRONOGRAF_TAG}
    image: "chrono_config"
    environment:
      RESOURCES_PATH: "/usr/share/chronograf/resources"
    volumes:
      # Mount for chronograf database
      - ./chronograf/data/:/var/lib/chronograf/
    links:
      # Chronograf requires network access to InfluxDB and Kapacitor
      - influxdb
      - kapacitor
    ports:
      # The WebUI for Chronograf is served on port 8888
      - "8888:8888"
    depends_on:
      - kapacitor
      - influxdb
      - telegraf

  documentation:
    build:
      context: ./documentation
    ports:
      - "3010:3000"
```


