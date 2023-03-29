# Скачал и установил VirtualBox и Vagrant
# Создал папку vagrant и изменил содержание файла, как в д.з.
# без VPN конечно не получилось, потом через него всё прошло без ошибок
#
# По умолчанию были выделены ресурсы
# ОЗУ 2048 МБ, 2 процессора
# порядок загрузки: жёсткий(64 Гб), оптический
# видеопамять 33 МБ
# Сеть один адаптер; общая папка 1
#
# Чтобы добавить оперативную память или ресурсы процессора нужно внести следующие настройки
# в конфигурацию

config.vm.provider "virtualbox" do |v|
  v.memory = 1024
  v.cpus = 2
end

# Переменная HISTFILESIZE описывается на 627-628 строках
# Она задаёт длину журнала history
#
# Директива ingnoreboth является сокращением для ingnorespace и ignoredups
# ignorespace - игнорирование строк, которые начинаются с пробела, 
# ignoredups - игнорирование дубликатов предыдущих строк.
#
# Фигурные скобки {} описаны на 206 строке, используются для вызова списка в командах.
#
#создать 100000 файлов командой    touch {1..100000}
# При создании 300000 файлов вышла ошибка: Argument list too long.
# Видимо есть ограничение на максимальное количество аргументов в команде - 2097152.
#
#[[ -d /tmp ]] возвращает 1 усли выражение в скобках верное и 0 если неверное.
#
# Задание 9
mkdir /tmp/new_path_directory
cp /bin/bash /tmp/new_path_directory/bash
export PATH="/tmp/new_path_directory:$PATH"
#
# Задание 10
# at осуществляет запуск команды в назначенное время
# batch запускает команду когда достигается определённый уровень нагрузки системы
