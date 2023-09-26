## Подготовка к выполнению
#### 1. Получить бесплатную версию Jira.
#### 2. Настроить её для своей команды разработки.
#### 3. Создать доски Kanban и Scrum.

## Основная часть
#### Необходимо создать собственные workflow для двух типов задач: bug и остальные типы задач. Задачи типа bug должны проходить жизненный цикл:
1. Open -> On reproduce.
2. On reproduce -> Open, Done reproduce.
3. Done reproduce -> On fix.
4. On fix -> On reproduce, Done fix.
5. Done fix -> On test.
6. On test -> On fix, Done.
7. Done -> Closed, Open.
![image](https://github.com/dikalov/devops-28/assets/126553776/329b40d8-6931-4ee2-a533-468e7a336b2a)
## Остальные задачи должны проходить по упрощённому workflow:
1. Open -> On develop.
2. On develop -> Open, Done develop.
3. Done develop -> On test.
4. On test -> On develop, Done.
5. Done -> Closed, Open.











