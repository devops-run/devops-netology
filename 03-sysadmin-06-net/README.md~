---
## Ответы на домашнее задание "3.6 Компьютерные сети, лекция 1" 
---
                    
<strong>1. Работа c HTTP через телнет</strong>   

#### telnet stackoverflow.com 80    
Trying 151.101.65.69...   
Connected to stackoverflow.com.   
Escape character is '^]'.   
GET /questions HTTP/1.0   
HOST: stackoverflow.com   

HTTP/1.1 301 Moved Permanently    
cache-control: no-cache, no-store, must-revalidate    
location: https://stackoverflow.com/questions   
x-request-guid: 23ec652f-8127-4d9d-bc9b-4d5b831d71f3    
feature-policy: microphone 'none'; speaker 'none'   
content-security-policy: upgrade-insecure-requests; frame-ancestors 'self' https://stackexchange.com    
Accept-Ranges: bytes    
Date: Sat, 09 Jul 2022 04:43:48 GMT   
Via: 1.1 varnish    
Connection: close   
X-Served-By: cache-fra19125-FRA   
X-Cache: MISS   
X-Cache-Hits: 0   
X-Timer: S1657341828.193667,VS0,VE93    
Vary: Fastly-SSL    
X-DNS-Prefetch-Control: off   
Set-Cookie: prov=a21bc76b-f894-2cc0-bc70-e3df550f26f3; domain=.stackoverflow.com; expires=Fri, 01-Jan-2055 00:00:00 GMT; path=/; HttpOnly   

#### Что он означает полученный HTTP код?   
1. Подключился по сетевому протоколу для реализации текстового терминального интерфейса по сети (telnet) при помощи транспорта TCP к хосту stackoverflow.com на порт 80 (http).   
2. Сформировал и выполнил запрос "GET /questions HTTP/1.0" к "HOST: stackoverflow.com"  
3. В ответе хост указал, что ко всем запросам на этот порт применяется правило редиректа 301. И все запросы на постоянной основе перемещаются в новое месторасположение location: https://stackoverflow.com/questions. Также host анонсировал в HTTP-заголовоках "Cache-Control", "feature-policy", "content-security-policy", "Accept-Ranges" параметры взаимодейтсвия (при каких условиях) он будет взаимодействовать с клиентами (в моём случае telnet) или браузерами.                       
4. Также в ответе указана дата, время запроса и другие параметры взаимодействия с клиентами.               

<strong>2. Работа в консоли разработчика F12</strong>          
Полученный в ответе код: HTTP/2 200 OK    
Время загрузки страницы:  Передано за: 5,59 с DOMContentLoaded: 571 мс load: 3,47 с   

    
![Netdata](https://i.ibb.co/n0CMrxW/Screenshot-3.png)   

    
    
#### Раздел Синхронизации запроса разбивает сетевой запрос на следующее подмножество этапов, определенных в спецификации HTTP Archive:   
Заблокирован: Время, проведенное в очереди в ожидании сетевого подключения.   
Разрешение DNS: Время, затраченное на разрешение имени хоста.   
Подключение: Время, затраченное на создание TCP-соединения.   
Отправка: Время, затраченное на отправку HTTP-запроса на сервер.    
Ожидание: Ожидание ответа от сервера.   
Получение: Время, необходимое для чтения всего ответа с сервера (или кэша).   


![Netdata](https://i.ibb.co/zXzN7w4/Screenshot-4.png)   
#### Время запроса:   

В моём случае больше всего времени потрачено на ожидание сетевого подключения. 
    
<strong>3. Какой IP адрес у вас в интернете?</strong>     
Команда в консоли выдаёт мой внешний ip адрес.  
#### wget -qO- eth0.me 
178.47.143.143 
(это не мой реальный IP, взят наугад)    
У меня статический IP, по понятным причинам публиковать его тут не стану.      

<strong>4. Какому провайдеру принадлежит ваш IP адрес? Какой автономной системе AS?</strong>    
Для получения информации об автономной системе воспользовался базой данных whois.radb.net     
    
#### whois -h whois.radb.net 178.47.143.143     
route:          178.47.128.0/20   
descr:          Rostelecom networks   
origin:         AS12389   
notify:         ripe@rt.ru    
mnt-by:         ROSTELECOM-MNT    
created:        2018-10-31T11:47:00Z    
last-modified:  2018-10-31T11:47:00Z    
source:         RIPE    
remarks:        ****************************    

#### whois -h whois.cymru.com -- '-v AS12389'   
AS      | CC | Registry | Allocated  | AS Name    
12389   | RU | ripencc  | 2005-07-01 | ROSTELECOM-AS, RU    
  
Имя AS: ROSTELECOM-AS    
Номер AS: AS12389   
Описание: Rostelecom networks     
Провайдер: Rostelecom     
сеть провайдера: 178.47.128.0/20      
     
<strong>5. Через какие сети проходит пакет, отправленный с вашего компьютера на адрес 8.8.8.8?</strong>  
   
         
<strong>6. </strong>       

