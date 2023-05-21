## Задание 1
### Опубликуйте созданный fork в своём репозитории и предоставьте ответ в виде ссылки
Скачиваем образ nginx:
```docker pull nginx```

Создаем dockerfile:

```
FROM nginx
RUN echo '<html><head>Hey, Netology</head><body><h1>I am DevOps Engineer!</h1></body></html>' > /usr/share/nginx/html/index.html
```
Делаем fork образа: ```docker build -f Dockerfile -t dikalow/devops-28:5.3 .```

Пушим образ в наш репозиторий(предварительно залогинившись): ```docker push dikalow/devops-28:5.3```

Запустим контейнер с пробросом на 8080 порт хоста: ```docker run -d -p 8080:80 dikalow/devops-28/netology:5.3```

Ссылка на образ: https://hub.docker.com/repository/docker/dikalow/devops-28/tags?page=1&ordering=last_updated

![image](https://github.com/dikalov/devops-28/assets/126553776/4c743e33-e167-4654-9e60-4329ba8ab9c0)

## Задание 2
### Посмотрите на сценарий ниже и ответьте на вопрос: «Подходит ли в этом сценарии использование Docker-контейнеров или лучше подойдёт виртуальная машина, физическая машина? Может быть, возможны разные варианты?» Детально опишите и обоснуйте свой выбор.




