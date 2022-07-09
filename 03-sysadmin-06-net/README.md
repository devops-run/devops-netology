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
3. В ответе хост указал, что ко всем запросам на этот порт применяется правило редиректа 301. И все запросы на постоянной основе перемещаются в новое месторасположение location: https://stackoverflow.com/questions. Также host анонсировал, что использует HTTP-заголовок "Cache-Control" с параметрами: no-cache, no-store, must-revalidate           
   
    

<strong>2. </strong>     

<strong>3. </strong>     

<strong>4. </strong>    
     
<strong>5. </strong>     
         
<strong>6. </strong>       

