# Домашнее задание к занятию "5. Тестирование roles"

## Подготовка к выполнению
1. Установите molecule: `pip3 install "molecule==3.5.2"`
2. Выполните `docker pull aragast/netology:latest` -  это образ с podman, tox и несколькими пайтонами (3.7 и 3.9) внутри    

#### Выполнено


## Основная часть

Наша основная цель - настроить тестирование наших ролей. Задача: сделать сценарии тестирования для vector. Ожидаемый результат: все сценарии успешно проходят тестирование ролей.

### Molecule

1. Запустите  `molecule test -s centos7` внутри корневой директории clickhouse-role, посмотрите на вывод команды.       
#### Склонировал репозиторий с прошлого задания на локальную машину:     
git clone https://github.com/devops-run/ansible-roles.git       

Перешел в директорию с clickhouse-role и выполнил:  __`molecule test -s centos7`__      
```bash
rocky91 clickhouse]$ molecule test -s centos7
CRITICAL 'molecule/centos7/molecule.yml' glob failed.  Exiting.

```

2. Перейдите в каталог с ролью vector-role и создайте сценарий тестирования по умолчанию при помощи     
`molecule init scenario --driver-name docker`.      

Ошибка:     
```bash
 molecule init scenario --driver-name docker
Usage: molecule init scenario [OPTIONS] [SCENARIO_NAME]
Try 'molecule init scenario --help' for help.

```
Требуется: pip3 install molecule-docker     

```bash
molecule init scenario --driver-name docker
INFO     Initializing new scenario default...
INFO     Initialized scenario in /home/user/work/ansible-roles/roles/vector/molecule/default successfully.

```

3. Добавьте несколько разных дистрибутивов (centos:8, ubuntu:latest) для инстансов и протестируйте роль, исправьте найденные ошибки, если они есть.     

Выполнено:      
#### cat roles/vector/molecule/default/molecule.yml  

```yaml
---
dependency:
  name: galaxy
driver:
  name: docker
lint: ansible-lint .
  yamllint .
platforms:
  - name: centos8
    image: docker.io/pycontribs/centos:8
    pre_build_image: true
  - name: centos7
    image: docker.io/pycontribs/centos:7
    pre_build_image: true
  - name: ubuntu
    image: ubuntu:latest
    pre_build_image: true
provisioner:
  name: ansible
verifier:
  name: ansible

```
#### Впечатлён простым (на первый взгляд), мощным, удобным инструментом "molekule" по выявлению и исправлению ошибок в сценариях ansible. Взял его себе на "вооружение".  

4. Добавьте несколько assert'ов в verify.yml файл для  проверки работоспособности vector-role (проверка, что конфиг валидный, проверка успешности запуска, etc). Запустите тестирование роли повторно и проверьте, что оно прошло успешно.
5. Добавьте новый тег на коммит с рабочим сценарием в соответствии с семантическим версионированием.

### Tox

1. Добавьте в директорию с vector-role файлы из [директории](./example)
2. Запустите `docker run --privileged=True -v <path_to_repo>:/opt/vector-role -w /opt/vector-role -it aragast/netology:latest /bin/bash`, где path_to_repo - путь до корня репозитория с vector-role на вашей файловой системе.
3. Внутри контейнера выполните команду `tox`, посмотрите на вывод.
5. Создайте облегчённый сценарий для `molecule` с драйвером `molecule_podman`. Проверьте его на исполнимость.
6. Пропишите правильную команду в `tox.ini` для того чтобы запускался облегчённый сценарий.
8. Запустите команду `tox`. Убедитесь, что всё отработало успешно.
9. Добавьте новый тег на коммит с рабочим сценарием в соответствии с семантическим версионированием.

После выполнения у вас должно получится два сценария molecule и один tox.ini файл в репозитории. Ссылка на репозиторий являются ответами на домашнее задание. Не забудьте указать в ответе теги решений Tox и Molecule заданий.

## Необязательная часть

1. Проделайте схожие манипуляции для создания роли lighthouse.
2. Создайте сценарий внутри любой из своих ролей, который умеет поднимать весь стек при помощи всех ролей.
3. Убедитесь в работоспособности своего стека. Создайте отдельный verify.yml, который будет проверять работоспособность интеграции всех инструментов между ними.
4. Выложите свои roles в репозитории. В ответ приведите ссылки.

---

### Как оформить ДЗ?

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

---