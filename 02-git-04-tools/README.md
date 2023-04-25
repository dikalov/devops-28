## Задание 1
### Найдите полный хеш и комментарий коммита, хеш которого начинается на aefea.

Ответ

aefead2207ef7e2aa5dc81a34aedf0cad4c32545

Update CHANGELOG.md

![image](https://user-images.githubusercontent.com/126553776/234281663-6604667e-4718-4a1d-81bf-cdbdb4d7045a.png)

## Задание 2
### Какому тегу соответствует коммит 85024d3?

commit 85024d3100126de36331c6982bfaac02cdab9e76 (tag: v0.12.23)

![image](https://user-images.githubusercontent.com/126553776/234282351-bd55e00a-fdc1-46d5-ae1e-9c40f0a02ce4.png)

## Задание 3
### Сколько родителей у коммита b8d720? Напишите их хеши.

 56cd7859e05c36c06b56d013b55a252d0bb7e158 9ea88f22fc6269854151c571162c5bcf958bee2b

![image](https://user-images.githubusercontent.com/126553776/234282899-63c68685-0c09-46f8-8387-ef188439de15.png)

## Задание 4
###  Перечислите хеши и комментарии всех коммитов которые были сделаны между тегами v0.12.23 и v0.12.24

git log v0.12.23..v0.12.24 --oneline

![image](https://user-images.githubusercontent.com/126553776/234283424-7d97c30b-2a36-4dc2-8cdd-b9c1ac16db2a.png)

## Задание 5
###  Найдите коммит в котором была создана функция `func providerSource`, ее определение в коде выглядит так `func providerSource(...)` (вместо троеточего перечислены аргументы).

$ git log -S'func providerSource(' --oneline

8c928e8358 main: Consult local directories as potential mirrors of providers

![image](https://user-images.githubusercontent.com/126553776/234284554-4dea4bb2-deb7-4016-8a40-7e6c3dbc21a8.png)

## Задание 6
###  Найдите все коммиты в которых была изменена функция globalPluginDirs.

8364383c35 Push plugin discovery down into command package

66ebff90cd move some more plugin search path logic to command

41ab0aef7a Add missing OS_ARCH dir to global plugin paths

52dbf94834 keep .terraform.d/plugins for discovery

78b1220558 Remove config.go and update things using its aliases

## Задание 7
###  Кто автор функции `synchronizedWriters`?

![image](https://user-images.githubusercontent.com/126553776/234289264-897b677b-11f6-4d44-b2c1-1a33cae2eab2.png)

git show bdfea50cc85161dea41be0fe3381fd98731ff786  удаление

![image](https://user-images.githubusercontent.com/126553776/234290149-8f855d06-6606-4cb2-860e-01df26065395.png)

git show 5ac311e2a91e381e2f52234668b49ba670aa0fe5 создание

![image](https://user-images.githubusercontent.com/126553776/234290426-9a7e774b-b5cf-4492-b39c-1f50f74f80b7.png)

### Ответ
Author: Martin Atkins <mart@degeneration.co.uk>



