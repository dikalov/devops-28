## Задание 1
### Опишите кратко, в чём основное отличие полной (аппаратной) виртуализации, паравиртуализации и виртуализации на основе ОС.
Полная виртуализация (аппаратная виртуализация) - это метод виртуализации, в котором гипервизор (программный слой, который создает виртуальные машины) создает полную виртуальную среду, 
в которой каждая виртуальная машина имеет свой собственный виртуальный процессор, память, сетевые интерфейсы и другие устройства. 
В этом случае гипервизор напрямую управляет оборудованием физического сервера и управляет доступом виртуальных машин к общим ресурсам.

Полная виртуализация (аппаратная виртуализация) - это метод виртуализации, в котором гипервизор (программный слой, который создает виртуальные машины) создает полную виртуальную среду, 
в которой каждая виртуальная машина имеет свой собственный виртуальный процессор, память, сетевые интерфейсы и другие устройства. 
В этом случае гипервизор напрямую управляет оборудованием физического сервера и управляет доступом виртуальных машин к общим ресурсам.

Виртуализация на основе ОС - это метод виртуализации, в котором несколько виртуальных машин работают на одном экземпляре операционной системы, которая управляет доступом виртуальных машин к общим ресурсам. 
В этом случае каждая виртуальная машина имеет свои собственные процессы и приложения, но ОС делит ресурсы между ними.

Главное отличие между этими методами заключается в том, как они управляют общими ресурсами на физическом сервере. Полная виртуализация обеспечивает наибольшую изоляцию и безопасность между виртуальными машинами, но требует больше ресурсов для управления. Паравиртуализация может быть более эффективной, но менее безопасной, так как виртуальные машины могут взаимодействовать напрямую друг с другом. Виртуализация на основе ОС может быть более эффективной, но менее безопасной, поскольку виртуальные машины могут воздействовать на друг друга через общую ОС.

## Задание 2
### Выберите один из вариантов использования организации физических серверов в зависимости от условий использования.

Организация серверов:

физические сервера,
паравиртуализация,
виртуализация уровня ОС.

Условия использования:

высоконагруженная база данных, чувствительная к отказу;
различные web-приложения;
Windows-системы для использования бухгалтерским отделом;
системы, выполняющие высокопроизводительные расчёты на GPU.

Опишите, почему вы выбрали к каждому целевому использованию такую организацию.

1) Высоконагруженная база данных, чувствительная к отказу - физические сервера. Виртуализация даёт оверхед, а в высоконагруженной системе это нежелательно. Достичь отказоустойчивости можно резервированием. Постоянно нагруженной системе потребуется максимум ресурсов хоста.

2) Различные web-приложения - виртуализация уровня ОС.  Лучше всего подойдет контейнеризация, поскольку их гораздо легче масштабировать в заисимости от нагрузки сервисов, быстрое развёртывание.

3) Windows-системы для использования бухгалтерским отделом - паравиртуализация. Виртуализация поможет системе быть более отказоустойчивой, из предложенных вариантов для Windows возможна только паравиртуализация. Ну и  быстрота и простота развертывания и обслуживания.

4) Системы, выполняющие высокопроизводительные расчёты на GPU - виртуализация уровня ОС. Лучше всего размещать на аппаратной виртуализации для того чтобы задействовать максимум ресурсов.

## Задание 3
### Выберите подходящую систему управления виртуализацией для предложенного сценария. Детально опишите ваш выбор.

Сценарии:

1) 100 виртуальных машин на базе Linux и Windows, общие задачи, нет особых требований. Преимущественно Windows based-инфраструктура, требуется реализация программных балансировщиков нагрузки, репликации данных и автоматизированного механизма создания резервных копий.

2) Требуется наиболее производительное бесплатное open source-решение для виртуализации небольшой (20-30 серверов) инфраструктуры на базе Linux и Windows виртуальных машин.

3) Необходимо бесплатное, максимально совместимое и производительное решение для виртуализации Windows-инфраструктуры.

4) Необходимо рабочее окружение для тестирования программного продукта на нескольких дистрибутивах Linux.

1) Подойдёт например, vSphere. Поддержка виртуальных машин с Windows и Linux, есть встроенные требуемые возможности (балансировка,
репликация, бэкапы) и может работать в кластере гипервизоров, что необходимо для работы 100 виртуальных машин.

2) Подойдёт Proxmox в режиме KVM: open source решение, поддерживает Linux и Windows гостевые ОС, по управлению сравним
с платными гипервизорами.

3) Например Free Windows Hyper-V Server ввиду того что управлять им не сильно сложно, эта система имеет много вариантов бэкапа и не требовательна к железу.

4) Хорошо подойдёт LXD, т.к. содержит огромную библиотеку с разными дистрибутивами в большом количестве конфигураций контейнеров, а также позволяющий довольно просто создавать контейнеры для тестирования с использованием автоматизации.

## Задание 4
### Опишите возможные проблемы и недостатки гетерогенной среды виртуализации (использования нескольких систем управления виртуализацией одновременно) и что необходимо сделать для минимизации этих рисков и проблем. Если бы у вас был выбор, создавали бы вы гетерогенную среду или нет? Мотивируйте ваш ответ примерами.

Гетерогенная среда виртуализации может иметь следующие проблемы и недостатки:

1. Сложность управления: Управление гетерогенной средой виртуализации может быть более сложным, так как разные системы управления виртуализацией имеют разные интерфейсы и настройки, что может усложнить управление и мониторинг.

2. Низкая производительность: Использование нескольких систем управления виртуализацией может привести к снижению производительности, так как каждая система управления требует ресурсов для работы.

3. Проблемы совместимости: Различные системы виртуализации могут иметь проблемы совместимости между собой, так как они могут использовать разные форматы дисков, сетевые протоколы и т.д.

Для минимизации этих рисков и проблем необходимо принять следующие меры:

1. Стандартизация: Использование стандартных форматов дисков, сетевых протоколов и других параметров может уменьшить проблемы совместимости.

2. Автоматизация: Использование автоматизированных инструментов для управления гетерогенной средой виртуализации может упростить управление и мониторинг.

3. Оптимизация ресурсов: Использование системы управления ресурсами может помочь оптимизировать использование ресурсов и уменьшить нагрузку на систему.

Если бы у меня был выбор, я бы не создавал гетерогенную среду виртуализации, так как это может усложнить управление и привести к проблемам совместимости и производительности. Вместо этого, я бы использовал единую систему управления виртуализацией для упрощения управления и мониторинга.

