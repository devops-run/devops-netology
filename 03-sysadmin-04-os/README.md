---
## Ответы на домашнее задание "3.4. Операционные системы, лекция 2" 
---
                    
<strong>1. Cоздайте самостоятельно простой unit-файл</strong>      
Doc: https://www.freedesktop.org/software/systemd/man/systemd.service.html   

wget node_exporter-1.3.1.linux-amd64.tar.gz  
tar xfz node_exporter-1.3.1.linux-amd64.tar.gz && cd node_exporter-1.3.1.linux-amd64    
cp node_exporter /usr/sbin/    
touch /etc/default/node_exporter    
vi /lib/systemd/system/node.service     
###########################################     
[Unit]  
Description=node_exporter daemon    
After=remote-fs.target nss-user-lookup.target   
    
[Service]   
EnvironmentFile=-/etc/default/node_exporter       
ExecStart=/usr/sbin/node_exporter   
Type=simple      
KillMode=process        
Restart=on-failure      
    
[Install]   
WantedBy=multi-user.target   
###########################################     
     
systemctl daemon-reload     
systemctl start node     
systemctl status node   
    
● node.service - node_exporter daemon       
     Loaded: loaded (/lib/systemd/system/node.service; enabled; vendor preset: enabled)        
     Active: active (running) since Sat 2022-07-02 07:18:02 UTC; 6s ago      
   Main PID: 1755 (node_exporter)          
      Tasks: 6 (limit: 3532)        
     Memory: 2.4M       
        CPU: 7ms        
     CGroup: /system.slice/node.service     
             └─1755 /usr/sbin/node_exporter      



<strong>2. Ознакомьтесь с опциями node_exporter и выводом `/metrics` по-умолчанию. Приведите несколько опций, которые вы бы выбрали для базового мониторинга хоста по CPU, памяти, диску и сети.</strong>       

curl http://127.0.0.1:9100/metrics | grep -i cpu | less     
'#' TYPE go_memstats_gc_cpu_fraction gauge        
go_memstats_gc_cpu_fraction 3.6645821200029023e-06      
    
node_memory_Percpu_bytes 2.310144e+06       
'#' HELP node_pressure_cpu_waiting_seconds_total Total time in seconds that processes have waited for CPU time    
    
'#' HELP process_cpu_seconds_total Total user and system CPU time spent in seconds.   
'#' TYPE process_cpu_seconds_total counter    
process_cpu_seconds_total 0.65  


curl http://127.0.0.1:9100/metrics | grep -i memory | grep -i available     


<strong>3. Установите в свою виртуальную машину Netdata.</strong>       
    
<strong>4. Можно ли по выводу `dmesg` понять, осознает ли ОС, что загружена не на настоящем оборудовании, а на системе виртуализации?</strong>      
    
<strong>5. Как настроен sysctl `fs.nr_open` на системе по-умолчанию?</strong>   
    
<strong>6. Запустите любой долгоживущий процесс в отдельном неймспейсе процессов.</strong>      
    
<strong>7. Найдите информацию о том, что такое `:(){ :|:& };:`.</strong>             
