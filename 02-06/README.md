## Задание 1
### Работа c HTTP через телнет.

Подключитесь утилитой telnet к сайту stackoverflow.com:

Для примера взял yandex.ru

Отправьте HTTP-запрос:

![image](https://user-images.githubusercontent.com/126553776/230900444-89a5a804-2411-4a91-9cb4-80eda98da5c4.png)

Код 301 говорит что запрошенный ресурс окончательно переехал по адресу указанному в строке: location: https://yandex.ru/maps

## Задание 2
###  Повторите задание 1 в браузере, используя консоль разработчика F12:

![image](https://user-images.githubusercontent.com/126553776/231110263-30197233-b48d-4473-a185-fcf467b3238b.png)

В ответ получили код 307 (Internal Redirect)

![image](https://user-images.githubusercontent.com/126553776/231110442-97dd9802-55c8-4acc-82b6-246391760793.png)

Страница загрузилась за 823 мс

![image](https://user-images.githubusercontent.com/126553776/231110862-fb05624e-1979-4e63-a8e0-f1456fb2adc6.png)

Самый долгий запрос был 454 мс - загрузка начальной страницы.

## Задание 3
###  Какой IP-адрес у вас в интернете?

