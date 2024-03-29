# Домашнее задание к занятию "5.1. Введение в виртуализацию. Типы и функции гипервизоров. Обзор рынка вендоров и областей применения."
---
## Задача 1

Опишите кратко, как вы поняли: в чем основное отличие полной (аппаратной) виртуализации, паравиртуализации и виртуализации на основе ОС.

```txt
В толщине прослойки, состоящей из гипервизора, драйверов, модификаций OS, и принципах её взаимодействия с конечной виртуальной машиной, физическим сервером. 

Полная – виртуализируется/эмулируется всё оборудование, требуемое для работы гостевой OS/вирт. машины (CPU, RAM, HDD, NIC). 
OS Вирт. машины при этом не модифицируется.

Паравиртуализации – ПО (гипервизор), являющийся надстройкой/прослойкой между OS физического сервера и OS конечной вирт. машины. 
Для работы виртуалки требуется модификация ядра OS вирт. машины.

Виртуализации на основе ОС - по сути это отдельный процесс в OS физического сервера. Как таковой прослойки между железом хост машины и вирт. сервером нет. 
За все операции вирт. машины отвечает ядро OS физического сервера.

```


## Задача 2

Выберите один из вариантов использования организации физических серверов, в зависимости от условий использования.

Организация серверов:
- физические сервера,
- паравиртуализация,
- виртуализация уровня ОС.

Условия использования:
- Высоконагруженная база данных, чувствительная к отказу.
- Различные web-приложения.
- Windows системы для использования бухгалтерским отделом.
- Системы, выполняющие высокопроизводительные расчеты на GPU.

Опишите, почему вы выбрали к каждому целевому использованию такую организацию.

### Решение
#### Высоконагруженная база данных, чувствительная к отказу. 
Высокодоступный кластер из физическиих серверов. 
Или НА кластер из вирт. машин 1-го типа (Hyper-V, vSphere) 

#### Различные web-приложения.
Зависит от типа приложений и решаемых ими задач. 
Но если взять за пример web-сервер, то эффективно нарезать ресурсы физ. сервера, 
при этом не тратиться на эмуляцию флоппи дисковода и USB портов (nginx отлично работает и без них),
то подойдут контейнеры OpenVZ, LXC.

####  Windows системы для использования бухгалтерским отделом.
Лучшая и безпроблемная эмуляция windows - виртуализация на базе windows (Hyper-V).
Т.к у нас нет Hyper-V, то используем паравиртуализацию.

#### Системы, выполняющие высокопроизводительные расчеты на GPU.
Физический сервера. 
Так получим максимальную производительность. В конвейере нет прослоек. 
GPU<-->драйвер<-->ядро OS.

## Задача 3

Выберите подходящую систему управления виртуализацией для предложенного сценария. Детально опишите ваш выбор.

Сценарии:

1. 100 виртуальных машин на базе Linux и Windows, общие задачи, нет особых требований. Преимущественно Windows based инфраструктура, требуется реализация программных балансировщиков нагрузки, репликации данных и автоматизированного механизма создания резервных копий.
2. Требуется наиболее производительное бесплатное open source решение для виртуализации небольшой (20-30 серверов) инфраструктуры на базе Linux и Windows виртуальных машин.
3. Необходимо бесплатное, максимально совместимое и производительное решение для виртуализации Windows инфраструктуры.
4. Необходимо рабочее окружение для тестирования программного продукта на нескольких дистрибутивах Linux.

### Решение
#### 1. VMWare vSphere.  
На мой взгляд, VMWare -  лидер в этой технологии. Все заявленные условия покрываются этой системой виртуализации.  
Удобство управления.
  
#### 2. Proxmox. 
Free решение ентерпрайз уровня. 
Возможность использования как высокопроизводительной виртуализации на основе ОС (для Linux)
так и паравиртуализации для windows.    
Универсальная система с удобным и приятным интерфейсом.

#### 3. VMWare ESXI.   
Хорошо зарекомендовавший себя продукт на протяжении солидного отрезка  времени.
Знаю это не по наслышке, частично использую его в своих текущих задачах на работе.
Стабильная работа, продуманный интерфейс, простота управления VM.

#### 4. Proxmox.
Free решение ентерпрайз уровня. 
При тестировании без API не обойтись.  
В Proxmox VE есть возможность использования API.  

## Задача 4

Опишите возможные проблемы и недостатки гетерогенной среды виртуализации (использования нескольких систем управления виртуализацией одновременно) и что необходимо сделать для минимизации этих рисков и проблем. Если бы у вас был выбор, то создавали бы вы гетерогенную среду или нет? Мотивируйте ваш ответ примерами.

### Ответ
#### Проблемы и недостатки
Согласен с мнением, что гетерогенные виртуальные среды неэффективны и сложны в управлении.  
Внедряя "винегрет" из нескольких решения от различных производителей требуется целый набор специалистов различного профиля и уровня ЗП.   
Данный способ виртуализации инфраструктуры является дорогостоящим и неэффективным в долгосрочной перспективе, поскольку, по мере развития технологий, компании стремятся обеспечить легкость в управлении, совместимость различных систем и масштабируемость инфраструктуры.
    
Разнородность директорий хранения данных и произвольное расположение виртуальных машин в инфраструктуре значительно усложняет задачу управления огромными объемами динамических данных. Эта проблема особенно ощутима, когда ИТ-отделу компании необходимо обеспечить оперативную миграцию данных и виртуальных машин между виртуальными и физическими средами в рамках физической ИТ-инфраструктуры предприятия, в ЦОДах или «облаке».
    
#### Что необходимо сделать
При условии экономической целесообразности перевести ифраструктуру и сервисы в "облако". (Внешнее, либо частное).
Управлять инфраструктурой как кодом. Для удобства выявления ошибок, контролировать историю изменения версий, т.к за каждой ошибкой стоит конкретная фамилия )).
    
#### Если бы был выбор  
Мне кажется, что в том или ином виде любая миграция на VM и в облака - это использование гетерогенной среды.  
Миновать, пропустить этот этап (для компании с реалтайм сервисами) скорее невозможно.   
Был инициатором и непосредственно участвовал в переводе части сервисов телефонной компании/инет-провайдера с "корзины" железных серверов на HA кластер на базе VMWare vSphere.
