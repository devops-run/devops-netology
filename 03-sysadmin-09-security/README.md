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
#### Включение mod_ssl       
    
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

