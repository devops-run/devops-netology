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

<strong>8*. Просканируйте хост scanme.nmap.org. Какие сервисы запущены?</strong>

#### root@ubuntu2204:/home# nmap -A scanme.nmap.org    

```
Starting Nmap 7.80 ( https://nmap.org ) at 2022-07-23 13:52 UTC
Nmap scan report for scanme.nmap.org (45.33.32.156)
Host is up (0.22s latency).
Other addresses for scanme.nmap.org (not scanned): 2600:3c01::f03c:91ff:fe18:bb2f
Not shown: 996 closed ports
PORT      STATE SERVICE    VERSION
22/tcp    open  ssh        OpenSSH 6.6.1p1 Ubuntu 2ubuntu2.13 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   1024 ac:00:a0:1a:82:ff:cc:55:99:dc:67:2b:34:97:6b:75 (DSA)
|   2048 20:3d:2d:44:62:2a:b0:5a:9d:b5:b3:05:14:c2:a6:b2 (RSA)
|   256 96:02:bb:5e:57:54:1c:4e:45:2f:56:4c:4a:24:b2:57 (ECDSA)
|_  256 33:fa:91:0f:e0:e1:7b:1f:6d:05:a2:b0:f1:54:41:56 (ED25519)
80/tcp    open  http       Apache httpd 2.4.7 ((Ubuntu))
|_http-server-header: Apache/2.4.7 (Ubuntu)
|_http-title: Go ahead and ScanMe!
9929/tcp  open  nping-echo Nping echo
31337/tcp open  tcpwrapped
Aggressive OS guesses: HP P2000 G3 NAS device (93%), Linux 2.6.32 (92%), Linux 2.6.32 - 3.1 (92%), Ubiquiti AirOS 5.5.9 (92%), Ubiquiti Pico Station WAP (AirOS 5.2.6) (92%), Linux 2.6.32 - 3.13 (92%), Linux 3.0 - 3.2 (92%), MikroTik RouterOS 6.36 (91%), Linux 4.0 (91%), Infomir MAG-250 set-top box (91%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 15 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 80/tcp)
HOP RTT       ADDRESS
1   0.36 ms   _gateway (192.168.1.1)
2   4.45 ms   90.150.180.22
3   6.52 ms   87.226.151.28
4   13.67 ms  95.167.92.190
5   67.83 ms  217.161.68.34
6   69.98 ms  217.161.68.33
7   65.61 ms  telia-gw.fnt.cw.net (195.2.22.238)
8   66.64 ms  ffm-bb1-link.ip.twelve99.net (62.115.124.116)
9   77.27 ms  prs-bb2-link.ip.twelve99.net (62.115.122.138)
10  83.27 ms  ldn-bb1-link.ip.twelve99.net (62.115.135.24)
11  144.45 ms nyk-bb1-link.ip.twelve99.net (62.115.112.244)
12  214.57 ms sjo-b23-link.ip.twelve99.net (62.115.119.229)
13  212.01 ms linode-ic342731-sjo-b21.ip.twelve99-cust.net (62.115.172.133)
14  220.30 ms if-2-6.csw6-fnc1.linode.com (173.230.159.69)
15  222.25 ms scanme.nmap.org (45.33.32.156)

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 27.17 seconds


```











