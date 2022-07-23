---
## Ответы на домашнее задание "3.9 Элементы безопасности информационных систем" 
---
                    
<strong>1. Установите Bitwarden плагин для браузера. Зарегестрируйтесь и сохраните несколько паролей.</strong>    

Зарегистрировался в системе Bitwarden. Установил, настроил браузерный плагин и desktop приложение. Включил Двухфакторную аутентификацию с помощью приложение-аутентификатора "Google Authenticator" 

![Bitwarden](https://i.ibb.co/KxcSfWT/Screenshot-6.png)


#### Web интерфейс    
![Bitwarden](https://i.ibb.co/b3HLgsN/Screenshot-7.png)

<strong>2. Установите Google authenticator на мобильный телефон. Настройте вход в Bitwarden акаунт через Google authenticator OTP.</strong>    

#### Google Authenticator   

![Bitwarden](https://i.ibb.co/GVMT7RF/Screenshot-8.png)

<strong>3. Установите apache2, сгенерируйте самоподписанный сертификат, настройте тестовый сайт для работы по HTTPS.</strong>   

Ставим  apache2:      
#### sudo apt install apache2   
  
Открываем  http, https порты:       
#### sudo ufw allow "Apache Full"      
    
Включение mod_ssl:    
#### a2enmod ssl       
    
Создание SSL-сертификата:     
#### openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/apache-selfsigned.key -out /etc/ssl/certs/apache-selfsigned.crt   
    
Cоздаю новый файл минимальной конфигурации:       
#### root@ubuntu2204:~# cat /etc/apache2/sites-available/ubuntu2204.conf      

```
<VirtualHost *:80>
        ServerName 192.168.1.13
        Redirect / https://192.168.1.13/
</VirtualHost>

<VirtualHost *:443>
   ServerName 192.168.1.13
   DocumentRoot /var/www/ubuntu2204
   SSLEngine on
   SSLCertificateFile /etc/ssl/certs/apache-selfsigned.crt
   SSLCertificateKeyFile /etc/ssl/private/apache-selfsigned.key
</VirtualHost>
root@ubuntu2204:~#

```
Создаю простейший html файл     
#### nano /var/www/ubuntu2204/index.html


Подключаю конфигурационный файл вирт. хоста     
#### a2ensite ubuntu2204.conf  
  
Тестирую конфигурацию web сервера     
```
root@ubuntu2204:~# apache2ctl configtest
Syntax OK   
root@ubuntu2204:~#    

``` 

Перезапускаю Апач   

#### systemctl reload apache2  


![Bitwarden](https://i.ibb.co/6nmDJnW/Screenshot-9.png)

<strong>4. Проверьте на TLS уязвимости произвольный сайт в интернете</strong>


#### root@ubuntu2204:/home/testssl.sh# ./testssl.sh -U --sneaky https://ya.ru   
```
 Start 2022-07-23 11:12:02        -->> 87.250.250.242:443 (ya.ru) <<--

 Further IP addresses:   2a02:6b8::2:242
 rDNS (87.250.250.242):  ya.ru.
 Service detected:       HTTP


 Testing vulnerabilities

 Heartbleed (CVE-2014-0160)                not vulnerable (OK), no heartbeat extension
 CCS (CVE-2014-0224)                       not vulnerable (OK)
 Ticketbleed (CVE-2016-9244), experiment.  not vulnerable (OK)
 ROBOT                                     not vulnerable (OK)
 Secure Renegotiation (RFC 5746)           supported (OK)
 Secure Client-Initiated Renegotiation     not vulnerable (OK)
 CRIME, TLS (CVE-2012-4929)                not vulnerable (OK)
 BREACH (CVE-2013-3587)                    potentially NOT ok, "br gzip deflate" HTTP compression detected. - only supplied "/" tested
                                           Can be ignored for static pages or if no secrets in the page
 POODLE, SSL (CVE-2014-3566)               not vulnerable (OK)
 TLS_FALLBACK_SCSV (RFC 7507)              Downgrade attack prevention supported (OK)
 SWEET32 (CVE-2016-2183, CVE-2016-6329)    VULNERABLE, uses 64 bit block ciphers
 FREAK (CVE-2015-0204)                     not vulnerable (OK)
 DROWN (CVE-2016-0800, CVE-2016-0703)      not vulnerable on this host and port (OK)
                                           make sure you don't use this certificate elsewhere with SSLv2 enabled services, see
                                           https://search.censys.io/search?resource=hosts&virtual_hosts=INCLUDE&q=9659D121304E46C0BF09A192A60D0682CC366788BAE86A2C3D0A451AB1CD79DF
 LOGJAM (CVE-2015-4000), experimental      not vulnerable (OK): no DH EXPORT ciphers, no DH key detected with <= TLS 1.2
 BEAST (CVE-2011-3389)                     TLS1: ECDHE-RSA-AES128-SHA AES128-SHA DES-CBC3-SHA
                                           VULNERABLE -- but also supports higher protocols  TLSv1.1 TLSv1.2 (likely mitigated)
 LUCKY13 (CVE-2013-0169), experimental     potentially VULNERABLE, uses cipher block chaining (CBC) ciphers with TLS. Check patches
 Winshock (CVE-2014-6321), experimental    not vulnerable (OK)
 RC4 (CVE-2013-2566, CVE-2015-2808)        no RC4 ciphers detected (OK)


 Done 2022-07-23 11:12:34 [  34s] -->> 87.250.250.242:443 (ya.ru) <<--


root@ubuntu2204:/home/testssl.sh#

```

<strong>5. Установите на Ubuntu ssh сервер, сгенерируйте новый приватный ключ. Скопируйте свой публичный ключ на другой сервер. Подключитесь к серверу по SSH-ключу.</strong>

Генерим ключ:   
#### ssh-keygen  

#### cat id_rsa.pub
```
root@ubuntu2204:~/.ssh# cat id_rsa.pub
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCzgqZpwwMLcYXhIDdi3DFHNEUup3edzYuuOg19dlQtPo4/nuStXCDaZiEAnhbZWL2olOL90JryMPTz102atZ1tvGfJvLCYBbpbmkdRGf+bgEA3QSeANohpcr65ROrw44YUWR+8vGnz6Gyhs8wibLMxKakFMBTphJUQMa0xxbKTnEwfxw2p8yevdR17qm8taB3cy7HlgalEckDSJK4jC1FCYraqNpGeQXBtK8rkxaK3HLTR3So/GXZhCiPX2h7JZ+rwlWmrqo+1fKuAzQUfl280vvNb8ptCZgziCRmyYlymSVNBemdmZcwbUEekQTA4kcLcA9sFjj6ze/+Nmti+VilGjfMGWmI5oDz5WYhRNz6gv4N/xu3xr/GfeVTNRXZFHMx5DQyfg/qLILaAXOP6PJI9gDRurlv8aGGI4RJL2/pl+zODmbDekz8/R7lPjeW4JiFg2mthYCuZOIO3QyrkaQjnZuM0UABgSnLRbKVwIzLTJmOPqnyFVyYjX+k04ydTfR0= root@ubuntu2204
root@ubuntu2204:~/.ssh#

```

Копируем публичный ключ на удаленный сервер
#### ssh-copy-id root@192.168.1.14


Подключаемся к удалённому серверу:
#### `ssh 'root@192.168.1.14'`


<strong>6. Переименуйте файлы ключей из задания 5. Настройте файл конфигурации SSH клиента, так чтобы вход на удаленный сервер осуществлялся по имени сервера.</strong>

#### cd ~/.ssh/

#### mv id_rsa id_rsa_renamed

#### mv id_rsa.pub id_rsa.pub_renamed


#### nano ~/.ssh/config

```
Host home-ssh
    HostName 192.168.1.14
    User root
    IdentityFile ~/.ssh/id_rsa_renamed

```

Теперь можно подключаться гораздо удобней:    
#### ssh home-ssh   


<strong>7. Соберите дамп трафика утилитой tcpdump в формате pcap, 100 пакетов. Откройте файл pcap в Wireshark.</strong>


#### sudo apt install wireshark

#### tcpdump -nnei any -c 100 -w 100.pcap

#### wireshark --help | grep file

```
Input file:
  -r <infile>, --read-file <infile>
                           set the filename to read from (no pipes or stdin!)
```
#### wireshark -r 100.pcap

![Bitwarden](https://i.ibb.co/KVW1wsw/Screenshot-10.png)










