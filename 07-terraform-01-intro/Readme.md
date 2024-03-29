# Домашнее задание к занятию "7.1. Инфраструктура как код"

## Задача 1. Выбор инструментов. 
 
### Легенда
 
Через час совещание на котором менеджер расскажет о новом проекте. Начать работу над которым надо 
будет уже сегодня. 
На данный момент известно, что это будет сервис, который ваша компания будет предоставлять внешним заказчикам.
Первое время, скорее всего, будет один внешний клиент, со временем внешних клиентов станет больше.

Так же по разговорам в компании есть вероятность, что техническое задание еще не четкое, что приведет к большому
количеству небольших релизов, тестирований интеграций, откатов, доработок, то есть скучно не будет.  
   
Вам, как девопс инженеру, будет необходимо принять решение об инструментах для организации инфраструктуры.
На данный момент в вашей компании уже используются следующие инструменты: 
- остатки Сloud Formation, 
- некоторые образы сделаны при помощи Packer,
- год назад начали активно использовать Terraform, 
- разработчики привыкли использовать Docker, 
- уже есть большая база Kubernetes конфигураций, 
- для автоматизации процессов используется Teamcity, 
- также есть совсем немного Ansible скриптов, 
- и ряд bash скриптов для упрощения рутинных задач.  

Для этого в рамках совещания надо будет выяснить подробности о проекте, что бы в итоге определиться с инструментами:

1. Какой тип инфраструктуры будем использовать для этого проекта: изменяемый или не изменяемый?
1. Будет ли центральный сервер для управления инфраструктурой?
1. Будут ли агенты на серверах?
1. Будут ли использованы средства для управления конфигурацией или инициализации ресурсов? 
 
В связи с тем, что проект стартует уже сегодня, в рамках совещания надо будет определиться со всеми этими вопросами.

### В результате задачи необходимо

1. Ответить на четыре вопроса представленных в разделе "Легенда". 
1. Какие инструменты из уже используемых вы хотели бы использовать для нового проекта? 
1. Хотите ли рассмотреть возможность внедрения новых инструментов для этого проекта? 

Если для ответа на эти вопросы недостаточно информации, то напишите какие моменты уточните на совещании.

#### Решение:

1. Какой тип инфраструктуры: На мой взгляд - изменяемый. Т.к это старт сервиса. Грядёт много "интеграций, откатов, доработок".
1. Будет ли центральный сервер для управления инфраструктурой? - Да, я бы использовал такой сервер. Мощный, высоко-доступный, чтобы работать было быстро, эффективно, комфортно.
1. Будут ли агенты на серверах? - Нет. Почти все инструмернты работают по SSH. Этого должно хватить, а лишние демоны - больше точек отказа.  
1. Да. я бы задействовал GitLab CI/CD, Ansible, Terraform.

Новые инструменты: GitLab CI/CD (код надо не только писать и разворачивать, но и версионировать)
 

## Задача 2. Установка терраформ. 

Официальный сайт: https://www.terraform.io/

Установите терраформ при помощи менеджера пакетов используемого в вашей операционной системе.
В виде результата этой задачи приложите вывод команды `terraform --version`.

#### Решение:


```bash
[root@alma9 ~]# yum-config-manager --add-repo https://rpm.releases.hashicorp.com/RHEL/hashicorp.repo
Adding repo from: https://rpm.releases.hashicorp.com/RHEL/hashicorp.repo
[root@alma9 ~]# yum -y install terraform
Hashicorp Stable - x86_64                                                                                904 kB/s | 911 kB     00:01
Dependencies resolved.
=========================================================================================================================================
 Package                          Architecture                  Version                           Repository                        Size
=========================================================================================================================================
Installing:
 terraform                        x86_64                        1.3.4-1                           hashicorp                         13 M

Transaction Summary
=========================================================================================================================================
Install  1 Package

Total download size: 13 M
Installed size: 58 M
Downloading Packages:
terraform-1.3.4-1.x86_64.rpm                                                                             5.4 MB/s |  13 MB     00:02
-----------------------------------------------------------------------------------------------------------------------------------------
Total                                                                                                    5.4 MB/s |  13 MB     00:02
Hashicorp Stable - x86_64                                                                                 10 kB/s | 3.1 kB     00:00
Importing GPG key 0xA3219F7B:
 Userid     : "HashiCorp Security (HashiCorp Package Signing) <security+packaging@hashicorp.com>"
 Fingerprint: E8A0 32E0 94D8 EB4E A189 D270 DA41 8C88 A321 9F7B
 From       : https://rpm.releases.hashicorp.com/gpg
Key imported successfully
Running transaction check
Transaction check succeeded.
Running transaction test
Transaction test succeeded.
Running transaction
  Preparing        :                                                                                                                 1/1
  Installing       : terraform-1.3.4-1.x86_64                                                                                        1/1
  Verifying        : terraform-1.3.4-1.x86_64                                                                                        1/1

Installed:
  terraform-1.3.4-1.x86_64

Complete!
[root@alma9 ~]#

```

```bash
[root@alma9 ~]# date
Tue Nov  8 11:16:11 PM +05 2022
[root@alma9 ~]# terraform --version
Terraform v1.3.4
on linux_amd64
[root@alma9 ~]#

```

## Задача 3. Поддержка легаси кода. 

В какой-то момент вы обновили терраформ до новой версии, например с 0.12 до 0.13. 
А код одного из проектов настолько устарел, что не может работать с версией 0.13. 
В связи с этим необходимо сделать так, чтобы вы могли одновременно использовать последнюю версию терраформа установленную при помощи
штатного менеджера пакетов и устаревшую версию 0.12. 

В виде результата этой задачи приложите вывод `--version` двух версий терраформа доступных на вашем компьютере 
или виртуальной машине.

#### Решение:

Для решения использовал переключатель версий terraform:         
https://github.com/tfutils/tfenv    

1. Подготовил прогу для работы:    

```bash
git clone --depth=1 https://github.com/tfutils/tfenv.git ~/.tfenv
ln -s ~/.tfenv/bin/* /usr/local/bin

```
2. Загрузил старшую версию terraform:   

```bash
[root@alma9 bin]# tfenv install 1.3.3
Installing Terraform v1.3.3
Downloading release tarball from https://releases.hashicorp.com/terraform/1.3.3/terraform_1.3.3_linux_amd64.zip
################################################################################################################################## 100.0%
Downloading SHA hash file from https://releases.hashicorp.com/terraform/1.3.3/terraform_1.3.3_SHA256SUMS
Not instructed to use Local PGP (/root/.tfenv/use-{gpgv,gnupg}) & No keybase install found, skipping OpenPGP signature verification
terraform_1.3.3_linux_amd64.zip: OK
Archive:  /tmp/tfenv_download.zdp0cc/terraform_1.3.3_linux_amd64.zip
  inflating: /root/.tfenv/versions/1.3.3/terraform
Installation of terraform v1.3.3 successful. To make this your default version, run 'tfenv use 1.3.3'

[root@alma9 bin]# tfenv list
  1.3.4
  1.3.3

```

3. Tекущая версия:
```
[root@alma9 bin]# terraform --version
Terraform v1.3.4
on linux_amd64

```
3. Переключение на старшую версию:
```
[root@alma9 bin]# tfenv use 1.3.3
Switching default version to v1.3.3
Default version (when not overridden by .terraform-version or TFENV_TERRAFORM_VERSION) is now: 1.3.3

```
4. Tекущая версия:
```
[root@alma9 ~]# terraform --version
Terraform v1.3.3
on linux_amd64

```
5. Список доступных версий
```
[root@alma9 bin]# tfenv list
  1.3.4
* 1.3.3 (set by /root/.tfenv/bin/.terraform-version)

```

