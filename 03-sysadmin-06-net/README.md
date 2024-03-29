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

#### Что означает полученный HTTP код?   
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
Для получения информации об автономной системе воспользовался базaми данных whois.radb.net и whois.cymru.com.                
    
#### whois -h whois.radb.net 178.47.143.143     
route:          178.47.128.0/20   
descr:          Rostelecom networks   
origin:         AS12389   
notify:         ripe@rt.ru    
mnt-by:         ROSTELECOM-MNT    
created:        2018-10-31T11:47:00Z    
last-modified:  2018-10-31T11:47:00Z    
source:         RIPE    
  
#### whois -h whois.cymru.com -- '-v AS12389'   
AS      | CC | Registry | Allocated  | AS Name    
12389   | RU | ripencc  | 2005-07-01 | ROSTELECOM-AS, RU    
  
Имя AS: ROSTELECOM-AS    
Номер AS: AS12389   
Описание: Rostelecom networks     
Провайдер: Rostelecom     
сеть провайдера: 178.47.128.0/20      
     
<strong>5. Через какие сети проходит пакет, отправленный с вашего компьютера на адрес 8.8.8.8?</strong> 
     
#### traceroute  --as-path-lookups 8.8.8.8 | grep AS
 2  87.226.151.27 (87.226.151.27) **[AS12389]**  4.270 ms  4.265 ms  4.328 ms   
 3  87.226.151.26 (87.226.151.26) **[AS12389]**  7.828 ms  6.429 ms 87.226.151.48 (87.226.151.48) **[AS12389]**  9.089 ms   
 5  72.14.205.132 (72.14.205.132) **[AS15169]**  29.469 ms  29.464 ms  29.459 ms    
 7  72.14.233.90 (72.14.233.90) **[AS15169]**  27.897 ms 108.170.250.33 (108.170.250.33) **[AS15169**]  30.175 ms  30.171 ms    
 8  108.170.250.99 (108.170.250.99) **[AS15169]**  28.457 ms 108.170.250.130 (108.170.250.130) **[AS15169]**  29.537 ms 108.170.250.66 (108.170.250.66) **[AS15169]**  28.448 ms    
 9  172.253.66.116 (172.253.66.116) **[AS15169]**  42.885 ms 142.250.238.214 (142.250.238.214) **[AS15169]**  45.392 ms 209.85.249.158 (209.85.249.158) **[AS15169]**  45.386 ms    
10  172.253.66.108 (172.253.66.108) **[AS15169]**  43.744 ms 72.14.238.168 (72.14.238.168) **[AS15169]**  42.745 ms  43.734 ms    
11  142.250.232.179 (142.250.232.179) **[AS15169]**  43.859 ms 216.239.58.69 (216.239.58.69) **[AS15169]**  43.892 ms 216.239.62.13 (216.239.62.13) **[AS15169]**  39.516 ms    
21  dns.google (8.8.8.8) **[AS15169]**  41.984 ms *  43.621 ms    
         
<strong>6. Повторите задание 5 в утилите mtr. На каком участке наибольшая задержка - delay?</strong>  
mtr --help | grep AS    
-z, --aslookup   display AS number    

#### mtr -znr 8.8.8.8   
Start: 2022-07-09T16:40:25+0500   
HOST: Fedora-36-server            Loss%   Snt   Last   Avg  Best  Wrst StDev    
  1. AS???    192.168.1.1          0.0%    10    0.3   0.3   0.3   0.4   0.0    
  2. AS12389  87.226.151.27        0.0%    10   38.9  10.0   3.4  38.9  12.2    
  3. AS12389  87.226.151.26        0.0%    10    5.4   6.3   5.0   9.5   1.7    
  4. AS12389  185.140.148.155     90.0%    10   28.3  28.3  28.3  28.3   0.0    
  5. AS15169  72.14.205.132        0.0%    10   27.8  27.8  27.4  28.5   0.3    
  6. AS15169  108.170.250.129      0.0%    10   29.0  28.6  27.5  29.0   0.4    
  7. AS15169  108.170.250.130      0.0%    10   27.9  28.8  27.8  29.7   0.8    
  8. AS15169  142.250.238.214      0.0%    10   44.2  51.8  43.5  83.2  12.8    
  9. AS15169  142.250.235.68       0.0%    10   45.6  45.5  44.9  46.3   0.5    
 10. AS15169  172.253.51.241       0.0%    10   42.0  42.1  41.8  42.6   0.2    
 11. AS???    ???                 100.0    10    0.0   0.0   0.0   0.0   0.0    
 12. AS???    ???                 100.0    10    0.0   0.0   0.0   0.0   0.0    
 13. AS???    ???                 100.0    10    0.0   0.0   0.0   0.0   0.0    
 14. AS???    ???                 100.0    10    0.0   0.0   0.0   0.0   0.0    
 15. AS???    ???                 100.0    10    0.0   0.0   0.0   0.0   0.0    
 16. AS???    ???                 100.0    10    0.0   0.0   0.0   0.0   0.0    
 17. AS???    ???                 100.0    10    0.0   0.0   0.0   0.0   0.0    
 18. AS???    ???                 100.0    10    0.0   0.0   0.0   0.0   0.0    
 19. AS???    ???                 100.0    10    0.0   0.0   0.0   0.0   0.0    
 20. AS???    ???                 100.0    10    0.0   0.0   0.0   0.0   0.0    
 21. AS???    ???                 100.0    10    0.0   0.0   0.0   0.0   0.0    
 22. AS15169  8.8.8.8              0.0%    10   44.4  46.7  44.0  68.4   7.6    
    
Максимальная задерзка на 9-м хопе AS15169  142.250.235.68   

**7. Какие DNS сервера отвечают за доменное имя dns.google? Какие A записи? воспользуйтесь утилитой dig** 
   
DNS сервера   
#### dig NS dns.google  
;; ANSWER SECTION:    
dns.google.             21579   IN      NS      ns1.zdns.google.    
dns.google.             21579   IN      NS      ns2.zdns.google.    
dns.google.             21579   IN      NS      ns3.zdns.google.    
dns.google.             21579   IN      NS      ns4.zdns.google.    

A записи      
#### dig A dns.google    
;; ANSWER SECTION:  
dns.google.             600     IN      A       8.8.8.8   
dns.google.             600     IN      A       8.8.4.4   

**8. Проверьте PTR записи для IP адресов из задания 7. Какое доменное имя привязано к IP? воспользуйтесь утилитой dig**   


Получил IP  
#### dig ns1.zdns.google ns2.zdns.google ns4.zdns.google ns4.zdns.google dns.google | grep IN     

ns1.zdns.google.        343138  IN      A       216.239.32.114    
ns2.zdns.google.        335828  IN      A       216.239.34.114    
ns4.zdns.google.        345560  IN      A       216.239.38.114      
ns4.zdns.google.        343883  IN      A       216.239.38.114     
dns.google.             108     IN      A       8.8.4.4   
dns.google.             108     IN      A       8.8.8.8   
  

Получил PTR записи для этих IP 
#### dig -x 216.239.32.114 | grep PTR; dig -x 216.239.34.114 | grep PTR; dig -x 216.239.38.114 | grep PTR; dig -x 8.8.8.8 | grep PTR;  dig -x 8.8.4.4 | grep PTR      
114.32.239.216.in-addr.arpa. 86317 IN   PTR     ns1.zdns.google.  
114.34.239.216.in-addr.arpa. 86400 IN   PTR     ns2.zdns.google.  
114.38.239.216.in-addr.arpa. 86400 IN   PTR     ns4.zdns.google.  
8.8.8.8.in-addr.arpa.   76903   IN      PTR     dns.google.   
4.4.8.8.in-addr.arpa.   79567   IN      PTR     dns.google.   

#### В итоге:   
Домен **dns.google**, котоый доступен по IP 8.8.8.8 и 8.8.4.4 облуживают 4 NS сервера ns1-ns4.zdns.google. 
