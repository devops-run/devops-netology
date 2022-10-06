# Домашнее задание к занятию "6.4. PostgreSQL"

## Задача 1

Используя docker поднимите инстанс PostgreSQL (версию 13). Данные БД сохраните в volume.

Подключитесь к БД PostgreSQL используя `psql`.

Воспользуйтесь командой `\?` для вывода подсказки по имеющимся в `psql` управляющим командам.

**Найдите и приведите** управляющие команды для:
- вывода списка БД
- подключения к БД
- вывода списка таблиц
- вывода описания содержимого таблиц
- выхода из psql

---
### Основные команды интерактивного режима PostgreSQL:
```

\connect db_name – подключиться к базе с именем db_name
\du – список пользователей
\dp (или \z) – список таблиц, представлений, последовательностей, прав доступа к ним
\di – индексы
\ds – последовательности
\dt – список таблиц
\dt+ — список всех таблиц с описанием
\dt *s* — список всех таблиц, содержащих s в имени
\dv – представления
\dS – системные таблицы
\d+ – описание таблицы
\o – пересылка результатов запроса в файл
\l – список баз данных
\i – читать входящие данные из файла
\e – открывает текущее содержимое буфера запроса в редакторе (если иное не указано в окружении переменной EDITOR, то будет использоваться по умолчанию vi)
\d “table_name” – описание таблицы
\i запуск команды из внешнего файла, например \i /my/directory/my.sql
\pset – команда настройки параметров форматирования
\echo – выводит сообщение
\set – устанавливает значение переменной среды. Без параметров выводит список текущих переменных (\unset – удаляет).
\? – справочник psql
\help – справочник SQL
\q (или Ctrl+D) – выход из программы
```

---

### Решение
docker-compose.yml
 
```
version: '3.9'
services:
  postgres:
    image: postgres:13
    container_name: postgres13
    ports:
      - "0.0.0.0:5432:5432"
    volumes:
      - ./data:/var/lib/postgresql/data
    environment:
      POSTGRES_USER: "admin"
      POSTGRES_PASSWORD: "admin"
      POSTGRES_DB: "test_db"
    restart: always

```
docker-compose up -d
psql -h 127.0.0.1 -U admin -d test_db

- вывода списка БД  
```
test_db=# \l
                             List of databases
   Name    | Owner | Encoding |  Collate   |   Ctype    | Access privileges
-----------+-------+----------+------------+------------+-------------------
 postgres  | admin | UTF8     | en_US.utf8 | en_US.utf8 |
 template0 | admin | UTF8     | en_US.utf8 | en_US.utf8 | =c/admin         +
           |       |          |            |            | admin=CTc/admin
 template1 | admin | UTF8     | en_US.utf8 | en_US.utf8 | =c/admin         +
           |       |          |            |            | admin=CTc/admin
 test_db   | admin | UTF8     | en_US.utf8 | en_US.utf8 |
(4 rows)

```
- подключения к БД
postgres=# \c postgres
psql (14.5 (Ubuntu 14.5-0ubuntu0.22.04.1), server 13.8 (Debian 13.8-1.pgdg110+1))
You are now connected to database "postgres" as user "admin".

- вывод списка таблиц (включая системные объекты)

postgres=# \dtS
 
```

                  List of relations
   Schema   |          Name           | Type  | Owner
------------+-------------------------+-------+-------
 pg_catalog | pg_aggregate            | table | admin
 pg_catalog | pg_am                   | table | admin
 pg_catalog | pg_amop                 | table | admin
 pg_catalog | pg_amproc               | table | admin
 pg_catalog | pg_attrdef              | table | admin

```
- вывода описания содержимого таблиц
postgres=# \dS+ pg_aggregate
```
                                   Table "pg_catalog.pg_aggregate"
      Column      |   Type   | Collation | Nullable | Default | Storage  | Stats target | Description
------------------+----------+-----------+----------+---------+----------+--------------+-------------
 aggfnoid         | regproc  |           | not null |         | plain    |              |
 aggkind          | "char"   |           | not null |         | plain    |              |
 aggnumdirectargs | smallint |           | not null |         | plain    |              |
 aggtransfn       | regproc  |           | not null |         | plain    |              |
 aggfinalfn       | regproc  |           | not null |         | plain    |              |
 aggcombinefn     | regproc  |           | not null |         | plain    |              |
 aggserialfn      | regproc  |           | not null |         | plain    |              |
 aggdeserialfn    | regproc  |           | not null |         | plain    |              |
 aggmtransfn      | regproc  |           | not null |         | plain    |              |
 aggminvtransfn   | regproc  |           | not null |         | plain    |              |
 aggmfinalfn      | regproc  |           | not null |         | plain    |              |
 aggfinalextra    | boolean  |           | not null |         | plain    |              |
 aggmfinalextra   | boolean  |           | not null |         | plain    |              |
 aggfinalmodify   | "char"   |           | not null |         | plain    |              |
 aggmfinalmodify  | "char"   |           | not null |         | plain    |              |
 aggsortop        | oid      |           | not null |         | plain    |              |
 aggtranstype     | oid      |           | not null |         | plain    |              |
 aggtransspace    | integer  |           | not null |         | plain    |              |
 aggmtranstype    | oid      |           | not null |         | plain    |              |
 aggmtransspace   | integer  |           | not null |         | plain    |              |
 agginitval       | text     | C         |          |         | extended |              |
 aggminitval      | text     | C         |          |         | extended |              |
Indexes:
    "pg_aggregate_fnoid_index" UNIQUE, btree (aggfnoid)
Access method: heap

```
- выхода из psql

\q
### Основные команды интерактивного режима PostgreSQL:
```

\connect db_name – подключиться к базе с именем db_name
\du – список пользователей
\dp (или \z) – список таблиц, представлений, последовательностей, прав доступа к ним
\di – индексы
\ds – последовательности
\dt – список таблиц
\dt+ — список всех таблиц с описанием
\dt *s* — список всех таблиц, содержащих s в имени
\dv – представления
\dS – системные таблицы
\d+ – описание таблицы
\o – пересылка результатов запроса в файл
\l – список баз данных
\i – читать входящие данные из файла
\e – открывает текущее содержимое буфера запроса в редакторе (если иное не указано в окружении переменной EDITOR, то будет использоваться по умолчанию vi)
\d “table_name” – описание таблицы
\i запуск команды из внешнего файла, например \i /my/directory/my.sql
\pset – команда настройки параметров форматирования
\echo – выводит сообщение
\set – устанавливает значение переменной среды. Без параметров выводит список текущих переменных (\unset – удаляет).
\? – справочник psql
\help – справочник SQL
\q (или Ctrl+D) – выход из программы
```
  
## Задача 2

Используя `psql` создайте БД `test_database`.

Изучите [бэкап БД](https://github.com/netology-code/virt-homeworks/tree/master/06-db-04-postgresql/test_data).

Восстановите бэкап БД в `test_database`.

Перейдите в управляющую консоль `psql` внутри контейнера.

Подключитесь к восстановленной БД и проведите операцию ANALYZE для сбора статистики по таблице.

Используя таблицу [pg_stats](https://postgrespro.ru/docs/postgresql/12/view-pg-stats), найдите столбец таблицы `orders` 
с наибольшим средним значением размера элементов в байтах.

**Приведите в ответе** команду, которую вы использовали для вычисления и полученный результат.

## Задача 3

Архитектор и администратор БД выяснили, что ваша таблица orders разрослась до невиданных размеров и
поиск по ней занимает долгое время. Вам, как успешному выпускнику курсов DevOps в нетологии предложили
провести разбиение таблицы на 2 (шардировать на orders_1 - price>499 и orders_2 - price<=499).

Предложите SQL-транзакцию для проведения данной операции.

Можно ли было изначально исключить "ручное" разбиение при проектировании таблицы orders?

## Задача 4

Используя утилиту `pg_dump` создайте бекап БД `test_database`.

Как бы вы доработали бэкап-файл, чтобы добавить уникальность значения столбца `title` для таблиц `test_database`?

---
