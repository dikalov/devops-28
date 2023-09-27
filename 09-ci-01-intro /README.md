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
#### Остальные задачи должны проходить по упрощённому workflow:
1. Open -> On develop.
2. On develop -> Open, Done develop.
3. Done develop -> On test.
4. On test -> On develop, Done.
5. Done -> Closed, Open.
![image](https://github.com/dikalov/devops-28/assets/126553776/a5423a2e-658e-4d4d-92ae-b9fd1f6efbc6)

#### Что нужно сделать
1. Создайте задачу с типом bug, попытайтесь провести его по всему workflow до Done.
2. Создайте задачу с типом epic, к ней привяжите несколько задач с типом task, проведите их по всему workflow до Done.
3. При проведении обеих задач по статусам используйте kanban.
4. Верните задачи в статус Open.
5. Перейдите в Scrum, запланируйте новый спринт, состоящий из задач эпика и одного бага, стартуйте спринт, проведите задачи до состояния Closed. Закройте спринт.
6. Если всё отработалось в рамках ожидания — выгрузите схемы workflow для импорта в XML. Файлы с workflow и скриншоты workflow приложите к решению задания.
![image](https://github.com/dikalov/devops-28/assets/126553776/66970067-2337-4d73-b9ab-54a488bff455)

[Ссылка Bug](https://github.com/dikalov/devops-28/blob/main/09-ci-01-intro%20/Bug%20workflow.xml)

[Ссылка All_workflow](https://github.com/dikalov/devops-28/blob/main/09-ci-01-intro%20/All_workflow.xml)
