# Домашнее задание к занятию "6.5. Elasticsearch"

## Задача 1

Используя докер образ [elasticsearch:7](https://hub.docker.com/_/elasticsearch) как базовый:

- составьте Dockerfile-манифест для elasticsearch
- соберите docker-образ и сделайте `push` в ваш docker.io репозиторий
- запустите контейнер из получившегося образа и выполните запрос пути `/` c хост-машины

Требования к `elasticsearch.yml`:
- данные `path` должны сохраняться в `/var/lib` 
- имя ноды должно быть `netology_test`

В ответе приведите:
- текст Dockerfile манифеста
- ссылку на образ в репозитории dockerhub
- ответ `elasticsearch` на запрос пути `/` в json виде

Подсказки:
- при сетевых проблемах внимательно изучите кластерные и сетевые настройки в elasticsearch.yml
- при некоторых проблемах вам поможет docker директива ulimit
- elasticsearch в логах обычно описывает проблему и пути ее решения
- обратите внимание на настройки безопасности такие как `xpack.security.enabled` 
- если докер образ не запускается и падает с ошибкой 137 в этом случае может помочь настройка `-e ES_HEAP_SIZE`
- при настройке `path` возможно потребуется настройка прав доступа на директорию

### Решение
1. Подготовил конфиг elasticsearch.yml, разместил его рядом с Dockerfile

elasticsearch.yml
```bash
cluster.name: es-ubuntu_cluster
node.name: netology_test
path.data: /var/lib/data
bootstrap.memory_lock: true
network.host: 0.0.0.0
discovery.type: single-node

```

1. Подготовил Dockerfile и на его основе собрал свой образ (использовал VPN):   

docker build -t devopsrun/es-7.17-ubuntu:v1 .  

Dockerfile  
  
```bash
FROM ubuntu
EXPOSE 9200 9300
USER 0
RUN export ES_HOME="/var/lib/elasticsearch"
RUN apt-get -y update && apt-get -y upgrade
RUN apt -y install wget vim
RUN wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.17.7-linux-x86_64.tar.gz
RUN wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.17.7-linux-x86_64.tar.gz.sha512
RUN sha512sum -c elasticsearch-7.17.7-linux-x86_64.tar.gz.sha512
RUN tar -xzf elasticsearch-7.17.7-linux-x86_64.tar.gz
RUN useradd -m -u 1000 elasticsearch
RUN mv elasticsearch-7.17.7 /var/lib/elasticsearch
RUN mkdir /var/lib/data
RUN mkdir /var/lib/elasticsearch/snapshots
COPY ./elasticsearch.yml /var/lib/elasticsearch/config
RUN chown -R elasticsearch:elasticsearch /var/lib/data
RUN chown -R elasticsearch:elasticsearch /var/lib/elasticsearch
USER 1000
ENV ES_HOME="/var/lib/elasticsearch" \
    ES_PATH_CONF="/var/lib/elasticsearch/config"
WORKDIR ${ES_HOME}
CMD ["sh", "-c", "${ES_HOME}/bin/elasticsearch"]

```

3. Запушил имидж на ДокерХаб     
docker login -u "devopsrun" -p "*****" docker.io    
docker push devopsrun/es-7.17-ubuntu:v1     

### Cсылка на образ в репозитории dockerhub     
https://hub.docker.com/r/devopsrun/es-7.17-ubuntu/tags  
 
4. Запустил контейнер на основе образа с помощь docker-compose    

```bash 
docker-compose up -d    
``` 

docker-compose.yaml

```yaml
version: "3"
services:
  elasticsearch:
    container_name: netology_test
    image: devopsrun/es-7.17-ubuntu:v1
    restart: always
    environment:
      - discovery.type=single-node
    ports:
      - 9200:9200
      - 9300:9300

```

```bash
[root@fedora-docker es]# docker ps
CONTAINER ID   IMAGE                         COMMAND                  CREATED          STATUS          PORTS                                                                                  NAMES
370a3b19c683   devopsrun/es-7.17-ubuntu:v1   "sh -c ${ES_HOME}/bi…"   31 minutes ago   Up 31 minutes   0.0.0.0:9200->9200/tcp, :::9200->9200/tcp, 0.0.0.0:9300->9300/tcp, :::9300->9300/tcp   netology_test


```

[root@fedora-docker ~]# curl localhost:9200/        
```json 
{
  "name" : "netology_test",
  "cluster_name" : "es-ubuntu_cluster",
  "cluster_uuid" : "Strad_1zSCqKRrjjsjpf6w",
  "version" : {
    "number" : "7.17.7",
    "build_flavor" : "default",
    "build_type" : "tar",
    "build_hash" : "78dcaaa8cee33438b91eca7f5c7f56a70fec9e80",
    "build_date" : "2022-10-17T15:29:54.167373105Z",
    "build_snapshot" : false,
    "lucene_version" : "8.11.1",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}

```

## Задача 2

В этом задании вы научитесь:
- создавать и удалять индексы
- изучать состояние кластера
- обосновывать причину деградации доступности данных

Ознакомтесь с [документацией](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-create-index.html) 
и добавьте в `elasticsearch` 3 индекса, в соответствии со таблицей:

| Имя | Количество реплик | Количество шард |
|-----|-------------------|-----------------|
| ind-1| 0 | 1 |
| ind-2 | 1 | 2 |
| ind-3 | 2 | 4 |

Получите список индексов и их статусов, используя API и **приведите в ответе** на задание.

Получите состояние кластера `elasticsearch`, используя API.

Как вы думаете, почему часть индексов и кластер находится в состоянии yellow?

Удалите все индексы.

**Важно**

При проектировании кластера elasticsearch нужно корректно рассчитывать количество реплик и шард,
иначе возможна потеря данных индексов, вплоть до полной, при деградации системы.

#### Решение    
- ind-1     
curl -X PUT "localhost:9200/ind-1?pretty" -H 'Content-Type: application/json' -d'   

```bash
> {
  "settings": {
    "number_of_shards": 1,
    "number_of_replicas": 0
  }
}
> '
{
  "acknowledged" : true,
  "shards_acknowledged" : true,
  "index" : "ind-1"
}

```
- ind-2      
curl -X PUT "localhost:9200/ind-2?pretty" -H 'Content-Type: application/json' -d'   
    
```bash
> {
  "settings": {
    "number_of_shards": 2,
    "number_of_replicas": 1
  }
}
> '
{
  "acknowledged" : true,
  "shards_acknowledged" : true,
  "index" : "ind-2"
}

```

- ind-3     
curl -X PUT "localhost:9200/ind-3?pretty" -H 'Content-Type: application/json' -d'   

```bash
> {
  "settings": {
    "number_of_shards": 4,
    "number_of_replicas": 2
  }
}
> '
{
  "acknowledged" : true,
  "shards_acknowledged" : true,
  "index" : "ind-3"
}
```

- Cписок индексов и их статусов:    
curl 'localhost:9200/_cat/indices?v'    

```bash
[root@fedora-docker es]# curl 'localhost:9200/_cat/indices?v'
health status index            uuid                   pri rep docs.count docs.deleted store.size pri.store.size
green  open   .geoip_databases d4aThjt6T5aMgG6w0ZHJHQ   1   0         41            0       39mb           39mb
green  open   ind-1            8TaXIF8BQ3e5aQahpeoTiA   1   0          0            0       226b           226b
yellow open   ind-3            4cer4QUoTuO0Mtt4cYiMIA   4   2          0            0       226b           226b
yellow open   ind-2            BbuxUHyeSXOUG_d_iPvOxg   2   1          0            0       452b           452b

```

- Состояние кластера

curl -X GET "localhost:9200/_cluster/health?pretty"
```json
{
  "cluster_name" : "es-ubuntu_cluster",
  "status" : "yellow",
  "timed_out" : false,
  "number_of_nodes" : 1,
  "number_of_data_nodes" : 1,
  "active_primary_shards" : 10,
  "active_shards" : 10,
  "relocating_shards" : 0,
  "initializing_shards" : 0,
  "unassigned_shards" : 10,
  "delayed_unassigned_shards" : 0,
  "number_of_pending_tasks" : 0,
  "number_of_in_flight_fetch" : 0,
  "task_max_waiting_in_queue_millis" : 0,
  "active_shards_percent_as_number" : 50.0
}

```
- Часть индексов и кластер находится в состоянии yellow т.к:    
По факту существует только один узел, кластер не может разместить копию базы и не является "Высокодоступным".

- Удалить все индексы:

```bash
curl -X DELETE 'http://localhost:9200/_all' 
{"acknowledged":true}

```

## Задача 3

В данном задании вы научитесь:
- создавать бэкапы данных
- восстанавливать индексы из бэкапов

Создайте директорию `{путь до корневой директории с elasticsearch в образе}/snapshots`.

Используя API [зарегистрируйте](https://www.elastic.co/guide/en/elasticsearch/reference/current/snapshots-register-repository.html#snapshots-register-repository) 
данную директорию как `snapshot repository` c именем `netology_backup`.

**Приведите в ответе** запрос API и результат вызова API для создания репозитория.

Создайте индекс `test` с 0 реплик и 1 шардом и **приведите в ответе** список индексов.

[Создайте `snapshot`](https://www.elastic.co/guide/en/elasticsearch/reference/current/snapshots-take-snapshot.html) 
состояния кластера `elasticsearch`.

**Приведите в ответе** список файлов в директории со `snapshot`ами.

Удалите индекс `test` и создайте индекс `test-2`. **Приведите в ответе** список индексов.

[Восстановите](https://www.elastic.co/guide/en/elasticsearch/reference/current/snapshots-restore-snapshot.html) состояние
кластера `elasticsearch` из `snapshot`, созданного ранее. 

**Приведите в ответе** запрос к API восстановления и итоговый список индексов.

Подсказки:
- возможно вам понадобится доработать `elasticsearch.yml` в части директивы `path.repo` и перезапустить `elasticsearch`

### Решение

1. Залогинился в контейнер, добавил строку в конфиг файл, подправил права на каталог и перезапустил контейнер:       

```bash
docker exec -u root -it netology_test bash
        
echo path.repo: [ "/var/lib/elasticsearch/snapshots" ] >> "$ES_HOME/config/elasticsearch.yml"
        
chown -R elasticsearch:elasticsearch /var/lib/elasticsearch

docker restart netology_test
```
2. Регистрация каталога netology_backup как snapshot repository: 

```bash
curl -X PUT "localhost:9200/_snapshot/netology_backup?pretty" -H 'Content-Type: application/json' -d'
> {
  "type": "fs",
  "settings": {
    "location": "/var/lib/elasticsearch/snapshots",
    "compress": true
  }
}
> '
{
  "acknowledged" : true

```
3. Создайте индекс test с 0 реплик и 1 шардом и приведите в ответе список индексов:

```
curl -X PUT "localhost:9200/test?pretty" -H 'Content-Type: application/json' -d'
> {
  "settings": {
    "number_of_shards": 1,
    "number_of_replicas": 0
  }
}
> '
{
  "acknowledged" : true,
  "shards_acknowledged" : true,
  "index" : "test"
}

```
```
curl 'localhost:9200/_cat/indices?v'
health status index            uuid                   pri rep docs.count docs.deleted store.size pri.store.size
green  open   .geoip_databases vMcCjYq8Sdy9bkVfLw4BlA   1   0         41            0     39.1mb         39.1mb
green  open   test             z7Pe6mDbROa-NqG2ftv3WQ   1   0          0            0       226b           226b
```

4. Snapshot состояния кластера elasticsearch.

```
curl -X PUT "localhost:9200/_snapshot/netology_backup/snapshot_1?wait_for_completion=true&pretty"
{
  "snapshot" : {
    "snapshot" : "snapshot_1",
    "uuid" : "fUlfWOdCTJW0wDFy9QdBiQ",
    "repository" : "netology_backup",
    "version_id" : 7170799,
    "version" : "7.17.7",
    "indices" : [
      ".geoip_databases",
      ".ds-ilm-history-5-2022.10.29-000001",
      "test",
      ".ds-.logs-deprecation.elasticsearch-default-2022.10.29-000001"
    ],
    "data_streams" : [
      "ilm-history-5",
      ".logs-deprecation.elasticsearch-default"
    ],
    "include_global_state" : true,
    "state" : "SUCCESS",
    "start_time" : "2022-10-29T12:38:23.512Z",
    "start_time_in_millis" : 1667047103512,
    "end_time" : "2022-10-29T12:38:24.713Z",
    "end_time_in_millis" : 1667047104713,
    "duration_in_millis" : 1201,
    "failures" : [ ],
    "shards" : {
      "total" : 4,
      "failed" : 0,
      "successful" : 4
    },
    "feature_states" : [
      {
        "feature_name" : "geoip",
        "indices" : [
          ".geoip_databases"
        ]
      }
    ]
  }
}

```
5. Листинг каталога со снапшётами:  
```
docker exec -it netology_test ls -l /var/lib/elasticsearch/snapshots/
total 24
-rw-r--r-- 1 elasticsearch elasticsearch 1422 Oct 29 12:38 index-0
-rw-r--r-- 1 elasticsearch elasticsearch    8 Oct 29 12:38 index.latest
drwxr-xr-x 6 elasticsearch elasticsearch  126 Oct 29 12:38 indices
-rw-r--r-- 1 elasticsearch elasticsearch 9703 Oct 29 12:38 meta-fUlfWOdCTJW0wDFy9QdBiQ.dat
-rw-r--r-- 1 elasticsearch elasticsearch  454 Oct 29 12:38 snap-fUlfWOdCTJW0wDFy9QdBiQ.dat

```

