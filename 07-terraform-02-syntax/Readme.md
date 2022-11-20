# Домашнее задание к занятию "7.2. Облачные провайдеры и синтаксис Terraform."

## Задача 1 (Вариант с Yandex.Cloud). Регистрация в ЯО и знакомство с основами (необязательно, но крайне желательно).

1. Подробная инструкция на русском языке содержится [здесь](https://cloud.yandex.ru/docs/solutions/infrastructure-management/terraform-quickstart).
2. Обратите внимание на период бесплатного использования после регистрации аккаунта. 
3. Используйте раздел "Подготовьте облако к работе" для регистрации аккаунта. Далее раздел "Настройте провайдер" для подготовки
базового терраформ конфига.
4. Воспользуйтесь [инструкцией](https://registry.terraform.io/providers/yandex-cloud/yandex/latest/docs) на сайте терраформа, что бы 
не указывать авторизационный токен в коде, а терраформ провайдер брал его из переменных окружений.

### Решение
1. Подготовил OS (Rocky Linux 9.0), установил YC    
2. Выполнил команду yc init для настройки профиля CLI.
3. yc config list

```bash
token: y0_**********************kM
cloud-id: b1gbhgrs90i9vorv6i8c
folder-id: b1gfs7kff96rdbes0mnv
compute-default-zone: ru-central1-a
[root@terra ~]#

```

4. Чтобы не указывать авторизационный токен в коде, а терраформ провайдер брал его из переменных окружений, добавил строку в конец файла ~/.bashrc и перелогинился:   
```bash
export IAM_TOKEN=`yc iam create-token`

```
#### curl -H "Authorization: Bearer ${IAM_TOKEN}" https://resource-manager.api.cloud.yandex.net/resource-manager/v1/clouds
```bash
[root@terra ~]# curl -H "Authorization: Bearer ${IAM_TOKEN}" https://resource-manager.api.cloud.yandex.net/resource-manager/v1/clouds
{
 "clouds": [
  {
   "id": "b1gbhgrs90i9vorv6i8c",
   "createdAt": "2022-03-27T04:27:13Z",
   "name": "cloud-devops-run",
   "organizationId": "bpfln6dbjahbnrdlcb7m"
  }
 ]
}
[root@terra ~]#
```
5. Создал сервисный аккаунт, назначил права на ресурс (через web интерфейс):         

```bash
yc iam service-account create --name terra

yc iam service-account list
+----------------------+------------+
|          ID          |    NAME    |
+----------------------+------------+
| ajejiia73mf8r5f7b2nt | devops-run |
| ajeu77i4iaqa84psgumk | terra      |
+----------------------+------------+
```

6. Создал сеть и подсеть в облаке:      
```bash
[root@terra terra]# yc vpc network create --name terra-network --labels my-label=terra-network --description "yandex terra network"
id: enp7fceva56gtj601nbi
folder_id: b1gfs7kff96rdbes0mnv
created_at: "2022-11-12T12:02:41Z"
name: terra-network
description: yandex terra network
labels:
  my-label: terra-network

[root@terra terra]# yc vpc subnet create --name terra-subnet-a --zone ru-central1-a --range 10.10.10.0/24 --network-name terra-network --description "subnet terra-network"
id: e9bj570s9dc93u13t053
folder_id: b1gfs7kff96rdbes0mnv
created_at: "2022-11-12T12:05:43Z"
name: terra-subnet-a
description: subnet terra-network
network_id: enp7fceva56gtj601nbi
zone_id: ru-central1-a
v4_cidr_blocks:
  - 10.10.10.0/24

[root@terra terra]# yc vpc network list && yc vpc subnet list
+----------------------+---------------+
|          ID          |     NAME      |
+----------------------+---------------+
| enp7fceva56gtj601nbi | terra-network |
+----------------------+---------------+

+----------------------+----------------+----------------------+----------------+---------------+-----------------+
|          ID          |      NAME      |      NETWORK ID      | ROUTE TABLE ID |     ZONE      |      RANGE      |
+----------------------+----------------+----------------------+----------------+---------------+-----------------+
| e9bj570s9dc93u13t053 | terra-subnet-a | enp7fceva56gtj601nbi |                | ru-central1-a | [10.10.10.0/24] |
+----------------------+----------------+----------------------+----------------+---------------+-----------------+


```


## Задача 2. Создание aws ec2 или yandex_compute_instance через терраформ. 

1. В каталоге `terraform` вашего основного репозитория, который был создан в начале курсе, создайте файл `main.tf` и `versions.tf`.
2. Зарегистрируйте провайдер 
   1. для [aws](https://registry.terraform.io/providers/hashicorp/aws/latest/docs). В файл `main.tf` добавьте
   блок `provider`, а в `versions.tf` блок `terraform` с вложенным блоком `required_providers`. Укажите любой выбранный вами регион 
   внутри блока `provider`.
   2. либо для [yandex.cloud](https://registry.terraform.io/providers/yandex-cloud/yandex/latest/docs). Подробную инструкцию можно найти 
   [здесь](https://cloud.yandex.ru/docs/solutions/infrastructure-management/terraform-quickstart).
3. Внимание! В гит репозиторий нельзя пушить ваши личные ключи доступа к аккаунту. Поэтому в предыдущем задании мы указывали
их в виде переменных окружения. 
4. В файле `main.tf` воспользуйтесь блоком `data "aws_ami` для поиска ami образа последнего Ubuntu.  
5. В файле `main.tf` создайте рессурс 
   1. либо [ec2 instance](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/instance).
   Постарайтесь указать как можно больше параметров для его определения. Минимальный набор параметров указан в первом блоке 
   `Example Usage`, но желательно, указать большее количество параметров.
   2. либо [yandex_compute_image](https://registry.terraform.io/providers/yandex-cloud/yandex/latest/docs/resources/compute_image).
6. Также в случае использования aws:
   1. Добавьте data-блоки `aws_caller_identity` и `aws_region`.
   2. В файл `outputs.tf` поместить блоки `output` с данными об используемых в данный момент: 
       * AWS account ID,
       * AWS user ID,
       * AWS регион, который используется в данный момент, 
       * Приватный IP ec2 инстансы,
       * Идентификатор подсети в которой создан инстанс.  
7. Если вы выполнили первый пункт, то добейтесь того, что бы команда `terraform plan` выполнялась без ошибок. 


В качестве результата задания предоставьте:
1. Ответ на вопрос: при помощи какого инструмента (из разобранных на прошлом занятии) можно создать свой образ ami?
1. Ссылку на репозиторий с исходной конфигурацией терраформа.  

### Решение:
```bash

```

1. Создал авторизованный ключ для сервисного аккаунта и записал его файл:

```bash
[root@terra terra]# yc iam key create --service-account-id ajeu77i4iaqa84psgumk --folder-name devops-run --output key.json
id: ajehuqj779pk7a0jr51f
service_account_id: ajeu77i4iaqa84psgumk
created_at: "2022-11-12T13:05:16.172459499Z"
key_algorithm: RSA_2048

```
2. Создал файл main.tf +доп. файлы для инфраструктуры и подключения 
```xml
terraform {
  required_providers {
    yandex = {
      source = "yandex-cloud/yandex"
    }
  }
  required_version = ">= 0.13"
}

provider "yandex" {
  zone = "ru-central1-a"
}
```
3. Создал/привёл к виду конфиг файлы.   

- Конфигурация terraform:
  - [main.tf](src/terra/main.tf)
  - [infrastructure.tf](src/terra/infrastructure.tf)
  - [meta.yml](src/terra/meta.yml)
  - [key.json](src/terra/key.json)

```
(В ДЗ полная копия рабочего каталога со спец. повреждёнными ключами. 
В "бою" использовал бы .gitignore  для отсечки файлов-ключей.)

4. Проверил и применил конфигурацию 
terraform validate    
terraform plan  
terraform apply 
 
5. Авторизовался на вновь созданной ВМ  

```bash
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '84.201.156.78' (ED25519) to the list of known hosts.
Welcome to Ubuntu 22.04.1 LTS (GNU/Linux 5.15.0-52-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Sat Nov 12 05:16:00 PM UTC 2022

  System load:  0.080078125       Processes:             126
  Usage of /:   86.1% of 4.84GB   Users logged in:       0
  Memory usage: 5%                IPv4 address for eth0: 192.168.100.9
  Swap usage:   0%

  => / is using 86.1% of 4.84GB


0 updates can be applied immediately.


Last login: Sat Nov 12 16:32:47 2022 from 46.36.218.77
ubuntu@fhmi7s8jf85qi1diuor9:~$ top
top - 17:16:23 up 25 min,  1 user,  load average: 0.06, 0.01, 0.01
Tasks: 124 total,   1 running, 123 sleeping,   0 stopped,   0 zombie
%Cpu(s):  3.2 us,  0.0 sy,  0.0 ni, 96.8 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
MiB Mem :   3924.0 total,   3527.6 free,    181.5 used,    214.9 buff/cache
MiB Swap:      0.0 total,      0.0 free,      0.0 used.   3515.5 avail Mem

```
6. Изменил конфигурацию оборудования ВМ (добавил ресурсов) и применил конфиг к облаку: 

terraform apply 

``` 
data.yandex_compute_image.ubuntu_image: Reading...
yandex_vpc_network.network_terraform: Refreshing state... [id=enp7eets2v3tqmcthe9g]
data.yandex_compute_image.ubuntu_image: Read complete after 1s [id=fd8njkcpguboriofo0nm]
yandex_vpc_subnet.subnet_terraform: Refreshing state... [id=e9bvupprfo7rckf6ue56]
yandex_compute_instance.vm-test1: Refreshing state... [id=fhmi7s8jf85qi1diuor9]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  ~ update in-place

Terraform will perform the following actions:

  # yandex_compute_instance.vm-test1 will be updated in-place
  ~ resource "yandex_compute_instance" "vm-test1" {
        id                        = "fhmi7s8jf85qi1diuor9"
        name                      = "test1"
        # (10 unchanged attributes hidden)

      ~ resources {
          ~ cores         = 2 -> 4
          ~ memory        = 2 -> 8
            # (2 unchanged attributes hidden)
        }

        # (4 unchanged blocks hidden)
    }

Plan: 0 to add, 1 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

yandex_compute_instance.vm-test1: Modifying... [id=fhmi7s8jf85qi1diuor9]
yandex_compute_instance.vm-test1: Still modifying... [id=fhmi7s8jf85qi1diuor9, 10s elapsed]
yandex_compute_instance.vm-test1: Modifications complete after 18s [id=fhmi7s8jf85qi1diuor9]

Apply complete! Resources: 0 added, 1 changed, 0 destroyed.

``` 
7. Удалил ресурсы:  
```bash
Do you really want to destroy all resources?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value: yes

yandex_compute_instance.vm-test1: Destroying... [id=fhmi7s8jf85qi1diuor9]
yandex_compute_instance.vm-test1: Still destroying... [id=fhmi7s8jf85qi1diuor9, 10s elapsed]
yandex_compute_instance.vm-test1: Still destroying... [id=fhmi7s8jf85qi1diuor9, 20s elapsed]
yandex_compute_instance.vm-test1: Destruction complete after 25s
yandex_vpc_subnet.subnet_terraform: Destroying... [id=e9bvupprfo7rckf6ue56]
yandex_vpc_subnet.subnet_terraform: Destruction complete after 2s
yandex_vpc_network.network_terraform: Destroying... [id=enp7eets2v3tqmcthe9g]
yandex_vpc_network.network_terraform: Destruction complete after 0s

Destroy complete! Resources: 3 destroyed.

```

Ответ на вопрос: при помощи какого инструмента (из разобранных на прошлом занятии) можно создать свой образ ami? - Packer from Terraform        
