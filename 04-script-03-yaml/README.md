
## Задание 1

Мы выгрузили JSON, который получили через API-запрос к нашему сервису:

```
    { "info" : "Sample JSON output from our service\t",
        "elements" :[
            { "name" : "first",
            "type" : "server",
            "ip" : 7175 
            }
            { "name" : "second",
            "type" : "proxy",
            "ip : 71.78.22.43
            }
        ]
    }
```
  Нужно найти и исправить все ошибки, которые допускает наш сервис.

### Ваш скрипт:

```
{
 "info" : "Sample JSON output from our service\\t",
  "elements" : [
    {
     "name" : "first",
     "type" : "server",
     "ip" : "71.75.22.1" 
    },
    {
     "name" : "second",
     "type" : "proxy",
     "ip" : "71.78.22.43"
    }
  ]
}
```
Правки:
1) Информация в поле "info" является просто описанием содержимого конфигурации. Обратите внимание, что символы \t в строке "Sample JSON output from our service\\t" используются для вставки символа табуляции.

2) На 2 строке пропущен пробел между `:` и `[` 

3) На 1,3 и 7 строке после скобки `{` сделать перенос для лучшего восприятия скрипта.

4) На 5 строке неверно записан `ip` адрес да и без кавычек.

5) На 9 строке пропущены закрывающиеся кавычки `ip"` и кавычки на значении

Данный JSON-конфиг является примером выходных данных от сервиса и содержит два элемента (объекта) в массиве "elements". Первый элемент содержит информацию о сервере с именем "first", его тип - "server", и IP-адрес - "71.75.22.1". Второй элемент содержит информацию о прокси-сервере с именем "second", его тип - "proxy", и IP-адрес - "71.78.22.43".

---

## Задание 2

В прошлый рабочий день мы создавали скрипт, позволяющий опрашивать веб-сервисы и получать их IP. К уже реализованному функционалу нам нужно добавить возможность записи JSON и YAML-файлов, описывающих наши сервисы. 

Формат записи JSON по одному сервису: `{ "имя сервиса" : "его IP"}`. 

Формат записи YAML по одному сервису: `- имя сервиса: его IP`. 

Если в момент исполнения скрипта меняется IP у сервиса — он должен так же поменяться в YAML и JSON-файле.

### Ваш скрипт:

```python
# !/usr/bin/env python3

import os
import json
import yaml
import time


site_list = ['drive.google.com', 'mail.google.com', 'google.com']
site_dict = {}

def check_list_of_sites(site_list, site_dict):

    for site_url in site_list:
        site_dict=check_site_dns(site_url, site_dict)
    return site_dict


def check_site_dns(site_url, site_dict):
    site_new_ip = []


    result_os = os.popen(f'dig +short {site_url} | grep  -E \'[0-9]\'')

    for result in result_os:
        # For any ip delete \n
        site_new_ip.append(result.replace("\n",""))

    print('site_new_ip: ', site_new_ip)

    if site_dict.get(site_url) != None:

        site_old_ip = site_dict[site_url]
        i = 0
        ip_changed = False
        while i < len(site_old_ip):
            if site_old_ip[i] == site_new_ip[i]:
                print(f'{site_url} - {site_old_ip[i]}.')
            else:
                print(f'[ERROR] {site_url} IP mismatch: {site_old_ip[i]} {site_new_ip[i]}.')                
                ip_changed = True
            i = i + 1

    else:
        #If it's first execution
        site_dict[site_url] = site_new_ip
        print('site_dict==', site_dict)
        ip_changed = True

    if ip_changed == True:
        with open("servers_ip.json", "w") as fp_json:
            json.dump(site_dict, fp_json, indent=2)
        with open("servers_ip.yaml", "w") as fp_yaml:
            yaml.dump(site_dict, fp_yaml, explicit_start=True, explicit_end=True)

    return site_dict

while True:
    site_dict = check_list_of_sites(site_list, site_dict)
    print("site_dict==", site_dict)
    time.sleep(5)
```

### Вывод скрипта при запуске во время тестирования:

```
vagrant@vagrant:~/yaml$ python3 ip.py
site_new_ip:  ['64.233.165.194']
site_dict== {'drive.google.com': ['64.233.165.194']}
site_new_ip:  ['74.125.205.83', '74.125.205.18', '74.125.205.19', '74.125.205.17']
site_dict== {'drive.google.com': ['64.233.165.194'], 'mail.google.com': ['74.125.205.83', '74.125.205.18', '74.125.205.19', '74.125.205.17']}
site_new_ip:  ['64.233.161.113', '64.233.161.102', '64.233.161.138', '64.233.161.139', '64.233.161.100', '64.233.161.101']
site_dict== {'drive.google.com': ['64.233.165.194'], 'mail.google.com': ['74.125.205.83', '74.125.205.18', '74.125.205.19', '74.125.205.17'], 'google.com': ['64.233.161.113', '64.233.161.102', '64.233.161.138', '64.233.161.139', '64.233.161.100', '64.233.161.101']}
site_dict== {'drive.google.com': ['64.233.165.194'], 'mail.google.com': ['74.125.205.83', '74.125.205.18', '74.125.205.19', '74.125.205.17'], 'google.com': ['64.233.161.113', '64.233.161.102', '64.233.161.138', '64.233.161.139', '64.233.161.100', '64.233.161.101']}
site_new_ip:  ['64.233.165.194']
drive.google.com - 64.233.165.194.

```

![image](https://user-images.githubusercontent.com/126553776/233852689-f827bfa8-9fc9-45f8-b073-032c992eb37a.png)

### JSON-файл(ы), который(е) записал ваш скрипт:

```json
???
```

### YAML-файл(ы), который(е) записал ваш скрипт:

```yaml
???
```

---

## Задание со звёздочкой* 

Это самостоятельное задание, его выполнение необязательно.
____

Так как команды в нашей компании никак не могут прийти к единому мнению о том, какой формат разметки данных использовать: JSON или YAML, нам нужно реализовать парсер из одного формата в другой. Он должен уметь:

   * принимать на вход имя файла;
   * проверять формат исходного файла. Если файл не JSON или YAML — скрипт должен остановить свою работу;
   * распознавать, какой формат данных в файле. Считается, что файлы *.json и *.yml могут быть перепутаны;
   * перекодировать данные из исходного формата во второй доступный —  из JSON в YAML, из YAML в JSON;
   * при обнаружении ошибки в исходном файле указать в стандартном выводе строку с ошибкой синтаксиса и её номер;
   * полученный файл должен иметь имя исходного файла, разница в наименовании обеспечивается разницей расширения файлов.

### Ваш скрипт:

```python
???
```

### Пример работы скрипта:

???

----

