## Задание 1
### Опубликуйте созданный fork в своём репозитории и предоставьте ответ в виде ссылки
Скачиваем образ nginx:
```docker pull nginx```

Создаем dockerfile:

```
FROM nginx
RUN echo '<html><head>Hey, Netology</head><body><h1>I am DevOps Engineer!</h1></body></html>' > /usr/share/nginx/html/index.html
```



