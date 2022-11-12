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
[root@terra terra]# yc iam service-account list
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
2. Создал файл main.tf
```json
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
```bash
[root@terra ~]# terraform init
Terraform initialized in an empty directory!

The directory has no Terraform configuration files. You may begin working
with Terraform immediately by creating Terraform configuration files.
[root@terra ~]# 
```

 

