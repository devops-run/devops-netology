# Домашнее задание к занятию "3. Использование Yandex Cloud"

## Подготовка к выполнению

1. Подготовьте в Yandex Cloud три хоста: для `clickhouse`, для `vector` и для `lighthouse`.     
Выполнено:  
 
![Yandex-vm](img/screen.png)

Ссылка на репозиторий LightHouse: https://github.com/VKCOM/lighthouse

## Основная часть

1. Допишите playbook: нужно сделать ещё один play, который устанавливает и настраивает lighthouse.

2. При создании tasks рекомендую использовать модули: `get_url`, `template`, `yum`, `apt`.
3. Tasks должны: скачать статику lighthouse, установить nginx или любой другой webserver, настроить его конфиг для открытия lighthouse, запустить webserver.

4. Приготовьте свой собственный inventory файл `prod.yml`.
Выполнено:  
5. Запустите `ansible-lint site.yml` и исправьте ошибки, если они есть.
Сделано:    
6. Попробуйте запустить playbook на этом окружении с флагом `--check`.
```bash
ansible-work playbook]$ ansible-playbook -i inventory/prod.yml site.yml --check

PLAY [Install Nginx] *************************************************************************************************************************

TASK [Gathering Facts] ***********************************************************************************************************************
ok: [lighthouse-01]
ok: [vector-01]

TASK [NGINX | Install eper-release] **********************************************************************************************************
changed: [lighthouse-01]
changed: [vector-01]

TASK [NGINX | Install Nginx] *****************************************************************************************************************
fatal: [lighthouse-01]: FAILED! => {"changed": false, "msg": "No package matching 'nginx' found available, installed or updated", "rc": 126, "results": ["No package matching 'nginx' found available, installed or updated"]}
fatal: [vector-01]: FAILED! => {"changed": false, "msg": "No package matching 'nginx' found available, installed or updated", "rc": 126, "results": ["No package matching 'nginx' found available, installed or updated"]}

PLAY RECAP ***********************************************************************************************************************************
lighthouse-01              : ok=2    changed=1    unreachable=0    failed=1    skipped=0    rescued=0    ignored=0
vector-01                  : ok=2    changed=1    unreachable=0    failed=1    skipped=0    rescued=0    ignored=0

```
7. Запустите playbook на `prod.yml` окружении с флагом `--diff`.    
Убедитесь, что изменения на системе произведены.    

Выполнено.  
#### Последние строки вывода:    

```bash

TASK [VECTOR | Create systemd unit] **********************************************************************************************************
--- before
+++ after: /home/*****/.ansible/tmp/ansible-local-25142vjsdi_xw/tmpsc5jw62b/vector.service.j2
@@ -0,0 +1,12 @@
+[Unit]
+Description=Vector service
+After=network.target
+Requires=network-online.target
+[Service]
+User=root
+Group=root
+ExecStart=/usr/bin/vector --config-yaml /etc/vector/vector.yml
+Restart=always
+[Install]
+WantedBy=multi-user.target
+

changed: [vector-01]

TASK [VECTOR | Start service] ****************************************************************************************************************
changed: [vector-01]

PLAY RECAP ***********************************************************************************************************************************
clickhouse-01              : ok=11   changed=9    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
lighthouse-01              : ok=11   changed=9    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
vector-01                  : ok=12   changed=9    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

```

8. Повторно запустите playbook с флагом `--diff` и убедитесь, что playbook идемпотентен.
```bash
PLAY RECAP ***********************************************************************************************************************************
clickhouse-01              : ok=9    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
lighthouse-01              : ok=8    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
vector-01                  : ok=10   changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

```

#### Проверка базы:     

```bash
[ansible-work playbook]$ ssh user@84.252.143.80
Last login: Tue Jan 10 14:57:35 2023 from *.*.*.*
[kornik@clickhouse-01 ~]$ clickhouse-client -h 127.0.0.1
ClickHouse client version 22.8.11.15 (official build).
Connecting to 127.0.0.1:9000 as user default.
Connected to ClickHouse server version 22.8.11 revision 54460.

Warnings:
 * Linux transparent hugepages are set to "always". Check /sys/kernel/mm/transparent_hugepage/enabled
 * Linux threads max count is too low. Check /proc/sys/kernel/threads-max
 * Maximum number of threads is lower than 30000. There could be problems with handling a lot of simultaneous queries.
 * Table system.session_log is enabled. It's unreliable and may contain garbage. Do not use it for any kind of security monitoring.

clickhouse-01.ru-central1.internal :) show databases

SHOW DATABASES

Query id: 8a3879f1-18f1-42ed-8616-433343516440

┌─name───────────────┐
│ INFORMATION_SCHEMA │
│ default            │
│ information_schema │
│ logs               │
│ system             │
└────────────────────┘

5 rows in set. Elapsed: 0.001 sec.

USE logs

Query id: 176ee8de-1742-4b77-bb72-bd6fb41dab62

Ok.

0 rows in set. Elapsed: 0.001 sec.

clickhouse-01.ru-central1.internal :) show tables

SHOW TABLES

Query id: cd508117-ffae-40f2-a8d6-f34d0707ca22

┌─name────────┐
│ access_logs │
└─────────────┘

```


9. Подготовьте README.md файл по своему playbook. В нём должно быть описано: что делает playbook, какие у него есть параметры и теги.
Описание работы playbook в комментах файла site.yml     

10. Готовый playbook выложите в свой репозиторий, поставьте тег `08-ansible-03-yandex` на фиксирующий коммит, в ответ предоставьте ссылку на него.
Сделано 
---

