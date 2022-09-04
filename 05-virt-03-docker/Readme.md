# Домашнее задание к занятию "5.3. Введение. Экосистема. Архитектура. Жизненный цикл Docker контейнера"

---

## Задача 1

Сценарий выполения задачи:

- создайте свой репозиторий на https://hub.docker.com;
- выберете любой образ, который содержит веб-сервер Nginx;
- создайте свой fork образа;
- реализуйте функциональность:
запуск веб-сервера в фоне с индекс-страницей, содержащей HTML-код ниже:

```html
<html>
<head>
Hey, Netology
</head>
<body>
<h1>I’m DevOps Engineer!</h1>
</body>
</html>
```
Опубликуйте созданный форк в своем репозитории и предоставьте ответ в виде ссылки на https://hub.docker.com/username_repo.

### Решение 
Подготовил контейнер на базе образа nginx:latest с помощью Dockerfile и файла index.html  
  
Запустил контейнер на 80 порту и проверил работу.  
#### docker run -d -p 80:80 webtest   
Создал имидж на базе текущего контейнера    
#### docker build -t webtest .
Присвоил тег 
#### docker tag webtest devopsrun/webtest:latest    
Авторизовался на https://hub.docker.com   
Отправил имидж  в свой публичный репозиторий    
    
https://hub.docker.com/r/devopsrun/webtest
#### docker push devopsrun/webtest    
 
#### Dockerfile
```docker
FROM nginx:latest
COPY index.html /usr/share/nginx/html/index.html

```  

## Задача 2

Посмотрите на сценарий ниже и ответьте на вопрос:
"Подходит ли в этом сценарии использование Docker контейнеров или лучше подойдет виртуальная машина, физическая машина? Может быть возможны разные варианты?"

Детально опишите и обоснуйте свой выбор.

--

Сценарий:

- Высоконагруженное монолитное java веб-приложение;
- Nodejs веб-приложение;
- Мобильное приложение c версиями для Android и iOS;
- Шина данных на базе Apache Kafka;
- Elasticsearch кластер для реализации логирования продуктивного веб-приложения - три ноды elasticsearch, два logstash и две ноды kibana;
- Мониторинг-стек на базе Prometheus и Grafana;
- MongoDB, как основное хранилище данных для java-приложения;
- Gitlab сервер для реализации CI/CD процессов и приватный (закрытый) Docker Registry.

### Решение
На мой взгляд для всех этих сервисов, кроме мобильных приложений, отлично подойдёт платформа Docker c её микро сервисной архитектурой. Для тестирования мобильных приложений в зависимости от требований ПО я бы воспользовался физической машиной или одной из платформ виртуализации.

## Задача 3

- Запустите первый контейнер из образа ***centos*** c любым тэгом в фоновом режиме, подключив папку ```/data``` из текущей рабочей директории на хостовой машине в ```/data``` контейнера;
- Запустите второй контейнер из образа ***debian*** в фоновом режиме, подключив папку ```/data``` из текущей рабочей директории на хостовой машине в ```/data``` контейнера;
- Подключитесь к первому контейнеру с помощью ```docker exec``` и создайте текстовый файл любого содержания в ```/data```;
- Добавьте еще один файл в папку ```/data``` на хостовой машине;
- Подключитесь во второй контейнер и отобразите листинг и содержание файлов в ```/data``` контейнера.

### Решение
1. Создал каталог /data на host машине debian11-docker  
2. Запустил два контейнера с параметрами "-d -v"
 
```bash
docker run -d -v /data:/data centos
docker run -d -v /data:/data debian
``` 
#### Список запущенных контейнеров (docker ps)
```bash
root@debian11-docker:/data# docker ps
CONTAINER ID   IMAGE     COMMAND       CREATED          STATUS          PORTS     NAMES
58ad294bc9e2   centos    "/bin/bash"   15 minutes ago   Up 15 minutes             peaceful_boyd
1229e999cd72   debian    "bash"        15 minutes ago   Up 15 minutes             gifted_driscoll
```
3. Подключился к первому контейнеру 
```bash
docker exec -it 58ad294bc9e2 bash
```
4. Создал файл test.txt 
```
[root@58ad294bc9e2 ~]# touch /data/test.txt && echo ${PWD} > /data/test.txt 
```
5. Подключился кo второму контейнеру
```bash
docker exec -it 1229e999cd72 bash
```
6. Создал файл 
```
root@1229e999cd72:~# touch /data/test2.txt && echo ${PWD} > /data/test2.txt
```
7. На host машине создал третий файл
```
root@debian11-docker:/data# touch /data/test3.txt
```
8. Листинг каталогов с 2-х контейнеров и хост-машины:   
```bash
root@debian11-docker:~# ls -l /data/
итого 8
-rw-r--r-- 1 root root 6 сен  4 06:51 test2.txt
-rw-r--r-- 1 root root 0 сен  4 06:56 test3.txt
-rw-r--r-- 1 root root 4 сен  4 06:22 test.txt
-----------------------
[root@58ad294bc9e2 ~]# ls -l /data/
total 8
-rw-r--r-- 1 root root 4 Sep  4 06:22 test.txt
-rw-r--r-- 1 root root 6 Sep  4 06:51 test2.txt
-rw-r--r-- 1 root root 0 Sep  4 06:56 test3.txt
-----------------------
root@1229e999cd72:~# ls -l /data/
total 8
-rw-r--r-- 1 root root 4 Sep  4 06:22 test.txt
-rw-r--r-- 1 root root 6 Sep  4 06:51 test2.txt
-rw-r--r-- 1 root root 0 Sep  4 06:56 test3.txt

```
### Вывод:
При запуске контейнера с монтированием одного общего каталога из хост машины (опция  -v, --volume Bind mount a volume) все контейнеры имеют полный доступ к файловой сисиеме хост машины (чтение/запись) в рамках подмонтированного при запуске каталога.


## Задача 4 (*)

Воспроизвести практическую часть лекции самостоятельно.

Соберите Docker образ с Ansible, загрузите на Docker Hub и пришлите ссылку вместе с остальными ответами к задачам.

### Решение 

1. Собрал Docker image с помощью Dockerfile (Версии ПО изменены на актуальные)

```yaml
FROM alpine:3.16

RUN CARGO_NET_GIT_FETCH_WITH_CLI=1 && \
    apk --no-cache add \
        sudo \
        python3\
        py3-pip \
        openssl \
        ca-certificates \
        sshpass \
        openssh-client \
        rsync \
        git && \
    apk --no-cache add --virtual build-dependencies \
        python3-dev \
        libffi-dev \
        musl-dev \
        gcc \
        cargo \
        openssl-dev \
        libressl-dev \
        build-base && \
    pip install --upgrade pip wheel && \
    pip install --upgrade cryptography cffi && \
    pip uninstall ansible-base && \
    pip install ansible-core && \
    pip install ansible==2.10.7 && \
    pip install mitogen ansible-lint jmespath && \
    pip install --upgrade pywinrm && \
    apk del build-dependencies && \
    rm -rf /var/cache/apk/* && \
    rm -rf /root/.cache/pip && \
    rm -rf /root/.cargo

RUN mkdir /ansible && \
    mkdir -p /etc/ansible && \
    echo 'localhost' > /etc/ansible/hosts

WORKDIR /ansible

CMD [ "ansible-playbook", "--version" ]
``` 

