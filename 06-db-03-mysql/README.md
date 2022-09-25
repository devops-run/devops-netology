# Домашнее задание к занятию "6.3. MySQL"

## Введение

Перед выполнением задания вы можете ознакомиться с 
[дополнительными материалами](https://github.com/netology-code/virt-homeworks/tree/master/additional/README.md).

## Задача 1

Используя docker поднимите инстанс MySQL (версию 8). Данные БД сохраните в volume.

Изучите [бэкап БД](https://github.com/netology-code/virt-homeworks/tree/master/06-db-03-mysql/test_data) и 
восстановитесь из него.

Перейдите в управляющую консоль `mysql` внутри контейнера.

Используя команду `\h` получите список управляющих команд.

Найдите команду для выдачи статуса БД и **приведите в ответе** из ее вывода версию сервера БД.

Подключитесь к восстановленной БД и получите список таблиц из этой БД.

**Приведите в ответе** количество записей с `price` > 300.

В следующих заданиях мы будем продолжать работу с данным контейнером.

#### Решение
1. Запустил контейнер с экземпляром MySQL v.8      
    
docker-compose.yml  
```yaml
version: '3.9'
services:
  db:
    image: mysql:8
    container_name: mysql-8
    environment:
      MYSQL_ROOT_PASSWORD: 1PassW0rd!
      MYSQL_DATABASE: test_db
      MYSQL_USER: user
      MYSQL_PASSWORD: 1passW0rd!
      MYSQL_ROOT_HOST: '%'
    ports:
      - "3306:3306"
    volumes:
      - ./dbdata:/var/lib/mysql
volumes:
  dbdata:

```
2. Восстановил базу из дампа test_dump.sql     

```
mysql# mysql -u root -h 127.0.0.1 -p test_db < test_dump.sql

```
3. Подключился к базе   
mysql -u root -h 127.0.0.1 -p   
```
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| test_db            |
+--------------------+
5 rows in set (0.00 sec)

mysql>
```
```
mysql> status;
--------------
mysql  Ver 8.0.30-0ubuntu0.22.04.1 for Linux on x86_64 ((Ubuntu))

Connection id:          11
Current database:
Current user:           root@172.18.0.1
SSL:                    Cipher in use is TLS_AES_256_GCM_SHA384
Current pager:          stdout
Using outfile:          ''
Using delimiter:        ;
Server version:         8.0.30 MySQL Community Server - GPL
Protocol version:       10
Connection:             127.0.0.1 via TCP/IP
Server characterset:    utf8mb4
Db     characterset:    utf8mb4
Client characterset:    utf8mb4
Conn.  characterset:    utf8mb4
TCP port:               3306
Binary data as:         Hexadecimal
Uptime:                 26 min 51 sec

Threads: 2  Questions: 42  Slow queries: 0  Opens: 164  Flush tables: 3  Open tables: 82  Queries per second avg: 0.026
--------------

mysql>

```
4. Переключился на базу и вывел список таблиц

```
mysql> use test_db;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> show tables;
+-------------------+
| Tables_in_test_db |
+-------------------+
| orders            |
+-------------------+
1 row in set (0.00 sec)

```
5. Сделал запрос SELECT 

```
mysql> SELECT price FROM orders WHERE price > 300;
+-------+
| price |
+-------+
|   500 |
+-------+
1 row in set (0.00 sec)

```
## Задача 2

Создайте пользователя test в БД c паролем test-pass, используя:
- плагин авторизации mysql_native_password
- срок истечения пароля - 180 дней 
- количество попыток авторизации - 3 
- максимальное количество запросов в час - 100
- аттрибуты пользователя:
    - Фамилия "Pretty"
    - Имя "James"

Предоставьте привелегии пользователю `test` на операции SELECT базы `test_db`.

Используя таблицу INFORMATION_SCHEMA.USER_ATTRIBUTES получите данные по пользователю `test` и
**приведите в ответе к задаче**.


#### Решение
1. Создал юзера     
```
Database changed
mysql> CREATE USER 'test'@'127.0.0.1' IDENTIFIED WITH mysql_native_password BY 'test-pass';
Query OK, 0 rows affected (0.02 sec)

```
2. Задал срок истечения пароля   
```
mysql> ALTER USER 'test'@'127.0.0.1' PASSWORD EXPIRE INTERVAL 180 DAY;
Query OK, 0 rows affected (0.01 sec)

```

3. Задал кол-во попыток неверной авторизации    
```
mysql> ALTER USER 'test'@'127.0.0.1' FAILED_LOGIN_ATTEMPTS 3 PASSWORD_LOCK_TIME 2;
Query OK, 0 rows affected (0.01 sec)

```

4. Задал максимальное кол-во запросов в час
```
mysql> ALTER USER 'test'@'127.0.0.1' WITH MAX_QUERIES_PER_HOUR 100;
Query OK, 0 rows affected (0.02 sec)
```

5. Задал атрибуты для пользователя  
```
mysql> ALTER USER 'test'@'127.0.0.1' ATTRIBUTE '{"Фамилия": "Pretty", "Имя": "James"}';
Query OK, 0 rows affected (0.02 sec)
```

6. Дал права SELECT на все таблицы базы test_db     
```
mysql> GRANT SELECT ON test_db.* TO 'test'@'127.0.0.1';
Query OK, 0 rows affected (0.02 sec)
```

7. Информация о пользователе test    
```
mysql> SELECT * FROM INFORMATION_SCHEMA.USER_ATTRIBUTES WHERE USER = 'test';
+------+-----------+-------------------------------------------------+
| USER | HOST      | ATTRIBUTE                                       |
+------+-----------+-------------------------------------------------+
| test | 127.0.0.1 | {"Имя": "James", "Фамилия": "Pretty"}           |
+------+-----------+-------------------------------------------------+
1 row in set (0.00 sec)

mysql>

```


## Задача 3

Установите профилирование `SET profiling = 1`.
Изучите вывод профилирования команд `SHOW PROFILES;`.

Исследуйте, какой `engine` используется в таблице БД `test_db` и **приведите в ответе**.

Измените `engine` и **приведите время выполнения и запрос на изменения из профайлера в ответе**:
- на `MyISAM`
- на `InnoDB`

### Решение

1. Включил режим  profiling  
```
mysql> SET profiling = 1;
Query OK, 0 rows affected, 1 warning (0.01 sec)

```
2. Переключился на базу и запросил статус
```
mysql> use test_db;
Database changed    

mysql> SHOW TABLE STATUS WHERE Name = 'orders'\G;


*************************** 1. row ***************************
           Name: orders
         Engine: InnoDB
        Version: 10
     Row_format: Dynamic
           Rows: 5
 Avg_row_length: 3276
    Data_length: 16384
Max_data_length: 0
   Index_length: 0
      Data_free: 0
 Auto_increment: 6
    Create_time: 2022-09-25 09:24:51
    Update_time: 2022-09-25 09:24:51
     Check_time: NULL
      Collation: utf8mb4_0900_ai_ci
       Checksum: NULL
 Create_options:
        Comment:
1 row in set (0.00 sec)

```

3. Сделал запрос на переключение ENGINE и вывел статус 
```
mysql> ALTER TABLE orders ENGINE = MyISAM;
Query OK, 5 rows affected (0.09 sec)
Records: 5  Duplicates: 0  Warnings: 0

mysql> SHOW TABLE STATUS WHERE Name = 'orders'\G;
*************************** 1. row ***************************
           Name: orders
         Engine: MyISAM
        Version: 10
     Row_format: Dynamic
           Rows: 5
 Avg_row_length: 3276
    Data_length: 16384
Max_data_length: 0
   Index_length: 0
      Data_free: 0
 Auto_increment: 6
    Create_time: 2022-09-25 10:23:53
    Update_time: 2022-09-25 09:24:51
     Check_time: NULL
      Collation: utf8mb4_0900_ai_ci
       Checksum: NULL
 Create_options:
        Comment:
1 row in set (0.00 sec)

```

2.
```

```

2.
```

```



## Задача 4 

Изучите файл `my.cnf` в директории /etc/mysql.

Измените его согласно ТЗ (движок InnoDB):
- Скорость IO важнее сохранности данных
- Нужна компрессия таблиц для экономии места на диске
- Размер буффера с незакомиченными транзакциями 1 Мб
- Буффер кеширования 30% от ОЗУ
- Размер файла логов операций 100 Мб

Приведите в ответе измененный файл `my.cnf`.

---
