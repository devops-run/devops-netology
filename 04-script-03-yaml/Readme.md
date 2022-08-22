# Домашнее задание к занятию "4.3. Языки разметки JSON и YAML"


## Обязательная задача 1
Мы выгрузили JSON, который получили через API запрос к нашему сервису:
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
#################
{
  "info": "Sample JSON output from our service\t",
  "elements": [
      {
          "name": "first",
          "type": "server",
          "ip": "71.75.22.40"
      },
      {
          "name": "second",
          "type": "proxy",
          "ip": "71.78.22.43"
      }
  ]
}
#################
Нужно найти и исправить все ошибки, которые допускает наш сервис

### Ответ (ошибки в синтаксисе, и в фомате данных)
```json
    { "info" : "Sample JSON output from our service\t",
        "elements" :[
            { "name" : "first",
            "type" : "server",
            "ip" : 7175             // Ошибка 1 - "7175" - не формат IP адреса. 
            },                      // Ошибка 2 - Пропущена запятая после пеерчисления элементов },  
            { "name" : "second",
            "type" : "proxy",
            "ip" : "71.78.22.43"    // Ошибка 3 - Не хватало кавычек
            }
        ]
    }
```



## Обязательная задача 2
В прошлый рабочий день мы создавали скрипт, позволяющий опрашивать веб-сервисы и получать их IP. К уже реализованному функционалу нам нужно добавить возможность записи JSON и YAML файлов, описывающих наши сервисы. Формат записи JSON по одному сервису: `{ "имя сервиса" : "его IP"}`. Формат записи YAML по одному сервису: `- имя сервиса: его IP`. Если в момент исполнения скрипта меняется IP у сервиса - он должен так же поменяться в yml и json файле.

### Ваш скрипт:
```python
#!/usr/bin/env python3

import socket
import time
import json
import yaml

servers = {"drive.google.com": "", "mail.google.com": "", "google.com": ""}
while True:
    for url, ip in servers.items():
        ip_addr = socket.gethostbyname(url)
        if ip == "":
            servers[url] = ip_addr
            print("{} - {}".format(url, ip_addr))
        elif ip != ip_addr:
            print("[ERROR] {} IP mismatch: {} -> {}".format(url, ip, ip_addr))
            servers[url] = ip_addr
    with open('ip.json', 'w') as json_file:
        json_file.write(json.dumps(servers, indent=2))
    with open('ip.yaml', 'w') as yaml_file:
        yaml_file.write(yaml.dump(servers, indent=2, explicit_start=True, explicit_end=True))
    time.sleep(1)

```

### Вывод скрипта при запуске при тестировании:
```
[fedora@Fedora-36-server 222]$ ./111.py
drive.google.com - 64.233.164.194
mail.google.com - 64.233.161.83
google.com - 142.251.1.113
[ERROR] mail.google.com IP mismatch: 64.233.161.83 -> 64.233.161.17
[ERROR] google.com IP mismatch: 142.251.1.113 -> 142.251.1.139
[ERROR] mail.google.com IP mismatch: 64.233.161.17 -> 64.233.161.18
[ERROR] google.com IP mismatch: 142.251.1.139 -> 142.251.1.138
[ERROR] google.com IP mismatch: 142.251.1.138 -> 142.251.1.139
[ERROR] google.com IP mismatch: 142.251.1.139 -> 142.251.1.102
[ERROR] mail.google.com IP mismatch: 64.233.161.18 -> 64.233.161.83
[ERROR] google.com IP mismatch: 142.251.1.102 -> 142.251.1.100
[ERROR] google.com IP mismatch: 142.251.1.100 -> 142.251.1.101

```

### json-файл(ы), который(е) записал ваш скрипт:
```json
{
  "drive.google.com": "64.233.164.194",
  "mail.google.com": "74.125.131.17",
  "google.com": "142.250.150.100"

```

### yml-файл(ы), который(е) записал ваш скрипт:
```yaml
---
drive.google.com: 64.233.164.194
google.com: 142.250.150.100
mail.google.com: 74.125.131.17
...

```

## Дополнительное задание (со звездочкой*) - необязательно к выполнению

Так как команды в нашей компании никак не могут прийти к единому мнению о том, какой формат разметки данных использовать: JSON или YAML, нам нужно реализовать парсер из одного формата в другой. Он должен уметь:
   * Принимать на вход имя файла
   * Проверять формат исходного файла. Если файл не json или yml - скрипт должен остановить свою работу
   * Распознавать какой формат данных в файле. Считается, что файлы *.json и *.yml могут быть перепутаны
   * Перекодировать данные из исходного формата во второй доступный (из JSON в YAML, из YAML в JSON)
   * При обнаружении ошибки в исходном файле - указать в стандартном выводе строку с ошибкой синтаксиса и её номер
   * Полученный файл должен иметь имя исходного файла, разница в наименовании обеспечивается разницей расширения файлов

### Ваш скрипт:
```python
???
```

### Пример работы скрипта:
???
