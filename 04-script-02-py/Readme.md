# Домашнее задание к занятию "4.2. Использование Python для решения типовых DevOps задач"

## Обязательная задача 1

Есть скрипт:
```python
#!/usr/bin/env python3
a = 1
b = '2'
c = a + b
```

### Вопросы:
| Вопрос  | Ответ |
| ------------- | ------------- |
| Какое значение будет присвоено переменной `c`?  | Никакое. (TypeError: unsupported operand type(s) for +: 'int' and 'str') |
| Как получить для переменной `c` значение 12?  | c=str(a)+b  |
| Как получить для переменной `c` значение 3?  | c=a+int(b)  |

Проверка: 
```python
#!/usr/bin/env python3
a = 1
b = '2'
c=str(a)+b
print (c)
c=a+int(b)
print (c)

$ ./run.py
12
3

```

## Обязательная задача 2
Мы устроились на работу в компанию, где раньше уже был DevOps Engineer. Он написал скрипт, позволяющий узнать, какие файлы модифицированы в репозитории, относительно локальных изменений. Этим скриптом недовольно начальство, потому что в его выводе есть не все изменённые файлы, а также непонятен полный путь к директории, где они находятся. Как можно доработать скрипт ниже, чтобы он исполнял требования вашего руководителя?

```python
#!/usr/bin/env python3

import os

bash_command = ["cd ~/netology/sysadm-homeworks", "git status"]
result_os = os.popen(' && '.join(bash_command)).read()
is_change = False
for result in result_os.split('\n'):
    if result.find('modified') != -1:
        prepare_result = result.replace('\tmodified:   ', '')
        print(prepare_result)
        break
```


### Ваш скрипт:
```python
#!/usr/bin/env python3

import os
from os.path import expanduser
home = expanduser("~")

bash_command = ["cd ~/devops-netology", "git status", "pwd"]
result_os = os.popen(' && '.join(bash_command)).read()
#is_change = False  # лишняя  переменная 
for result in result_os.split('\n'):
    if result.find('modified') != -1:
        prepare_result = result.replace('\tmodified:   ', '')
        print(f'{home}/devops-netology/{prepare_result}')
#       break  # Цикл прерывался после первого найденного файла
```

### Вывод скрипта при запуске:
```
[fedora@Fedora-36-server ~]$ ./run.py
/home/fedora/devops-netology/01-intro-01/README.md
/home/fedora/devops-netology/03-sysadmin-06-net/README.md
/home/fedora/devops-netology/04-script-02-py/Readme.md
[fedora@Fedora-36-server ~]$

```

## Обязательная задача 3
1. Доработать скрипт выше так, чтобы он мог проверять не только локальный репозиторий в текущей директории, а также умел воспринимать путь к репозиторию, который мы передаём как входной параметр. Мы точно знаем, что начальство коварное и будет проверять работу этого скрипта в директориях, которые не являются локальными репозиториями.

### Ваш скрипт:
```python
#!/usr/bin/env python3
import os
import sys
import subprocess

git_dir = sys.argv[1]
bash_command = ["cd " + git_dir, "git status"]
os.chdir(git_dir)
with subprocess.Popen(['git', 'status'], stdout=subprocess.PIPE) as proc:
    result = proc.stdout.read().decode("utf-8")
if result.find('not') == -1:
    print('В данном каталоге нет репозитория GIT!')
else:
  result_os =  os.popen(' && '.join(bash_command)).read()
  for result in result_os.split('\n'):
      if result.find('modified') != -1:
          prepare_result = result.replace('\tmodified:   ', '')
          print(f'{git_dir}{prepare_result}')

```

### Вывод скрипта при запуске при тестировании:
```
[fedora@Fedora-36-server ~]$ ./run2.py /home/fedora/
fatal: not a git repository (or any of the parent directories): .git
В данном каталоге нет репозитория GIT!

[fedora@Fedora-36-server ~]$ ./run2.py /home/fedora/devops-netology/
/home/fedora/devops-netology/04-script-02-py/Readme.md

```

## Обязательная задача 4
1. Наша команда разрабатывает несколько веб-сервисов, доступных по http. Мы точно знаем, что на их стенде нет никакой балансировки, кластеризации, за DNS прячется конкретный IP сервера, где установлен сервис. Проблема в том, что отдел, занимающийся нашей инфраструктурой очень часто меняет нам сервера, поэтому IP меняются примерно раз в неделю, при этом сервисы сохраняют за собой DNS имена. Это бы совсем никого не беспокоило, если бы несколько раз сервера не уезжали в такой сегмент сети нашей компании, который недоступен для разработчиков. Мы хотим написать скрипт, который опрашивает веб-сервисы, получает их IP, выводит информацию в стандартный вывод в виде: <URL сервиса> - <его IP>. Также, должна быть реализована возможность проверки текущего IP сервиса c его IP из предыдущей проверки. Если проверка будет провалена - оповестить об этом в стандартный вывод сообщением: [ERROR] <URL сервиса> IP mismatch: <старый IP> <Новый IP>. Будем считать, что наша разработка реализовала сервисы: `drive.google.com`, `mail.google.com`, `google.com`.

### Ваш скрипт:
```python
#!/usr/bin/env python3
# Проверяем изменение IP адресов переданныйх на вход
import socket
from time import sleep
import os
import json
import yaml

tlist = {'drive.google.com': '0.0.0.0', 'mail.google.com': '0.0.0.0', 'google.com': '0.0.0.0'}
n = 1


# Заполненяем словарь актуальными IP
def fill_tlist(x):
    for node in x:
        ipaddres = socket.gethostbyname(node)
        x[node] = ipaddres
    return x


# запись в формате json \ yaml
def fill_json_yaml(y):
    with open('hosts.json', 'w') as jtmp:
        jtmp.write(str(json.dumps(y)))
    with open('hosts.yaml', 'w') as ytmp:
        ytmp.write(yaml.dump(y))
    return


# заполним словарь и запишем YAML\JSON чтобы был актуальный спикок всегда
fill_json_yaml(fill_tlist(tlist))


# цикл проверки изменения адреса. цикл прерывается при изменениях и записывает последние актуальные адреса в JSON \ YAML
while n != 0:
    tmp = fill_tlist(tlist)
    sleep(1)
    os.system('clear')
    for host in tmp:
        ipaddress = socket.gethostbyname(host)
        if ipaddress != tmp[host]:
            print(' [ERROR] ' + str(host) + ' IP mistmatch: ' + tmp[host] + ' ---> ' + ipaddress)
            tmp[host] = ipaddress
            fill_json_yaml(tmp)
            n = 0
        else:
            print(str(host) + ' ' + ipaddress + ' OK ')
       
```

### Вывод скрипта при тестировании:
```
[fedora@Fedora-36-server ~]$ ./run3.py
drive.google.com 142.251.1.194 OK
[ERROR] mail.google.com IP mistmatch: 209.85.233.83 ---> 209.85.233.17
[ERROR] google.com IP mistmatch: 173.194.220.113 ---> 173.194.220.100


```

## Дополнительное задание (со звездочкой*) - необязательно к выполнению

Так получилось, что мы очень часто вносим правки в конфигурацию своей системы прямо на сервере. Но так как вся наша команда разработки держит файлы конфигурации в github и пользуется gitflow, то нам приходится каждый раз переносить архив с нашими изменениями с сервера на наш локальный компьютер, формировать новую ветку, коммитить в неё изменения, создавать pull request (PR) и только после выполнения Merge мы наконец можем официально подтвердить, что новая конфигурация применена. Мы хотим максимально автоматизировать всю цепочку действий. Для этого нам нужно написать скрипт, который будет в директории с локальным репозиторием обращаться по API к github, создавать PR для вливания текущей выбранной ветки в master с сообщением, которое мы вписываем в первый параметр при обращении к py-файлу (сообщение не может быть пустым). При желании, можно добавить к указанному функционалу создание новой ветки, commit и push в неё изменений конфигурации. С директорией локального репозитория можно делать всё, что угодно. Также, принимаем во внимание, что Merge Conflict у нас отсутствуют и их точно не будет при push, как в свою ветку, так и при слиянии в master. Важно получить конечный результат с созданным PR, в котором применяются наши изменения. 

### Ваш скрипт:
```python
???
```

### Вывод скрипта при запуске при тестировании:
```
???
```
