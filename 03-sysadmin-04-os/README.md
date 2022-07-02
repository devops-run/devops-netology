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
`#` TYPE node_cpu_seconds_total counter
node_cpu_seconds_total{cpu="0",mode="idle"} 6305.29
node_cpu_seconds_total{cpu="0",mode="iowait"} 0.46
node_cpu_seconds_total{cpu="0",mode="irq"} 0
node_cpu_seconds_total{cpu="0",mode="nice"} 0.47
node_cpu_seconds_total{cpu="0",mode="softirq"} 7.44
node_cpu_seconds_total{cpu="0",mode="steal"} 0
node_cpu_seconds_total{cpu="0",mode="system"} 6.71
node_cpu_seconds_total{cpu="0",mode="user"} 3.8
node_cpu_seconds_total{cpu="1",mode="idle"} 6319.44
node_cpu_seconds_total{cpu="1",mode="iowait"} 0.6
node_cpu_seconds_total{cpu="1",mode="irq"} 0
node_cpu_seconds_total{cpu="1",mode="nice"} 0
node_cpu_seconds_total{cpu="1",mode="softirq"} 1.6
node_cpu_seconds_total{cpu="1",mode="steal"} 0
node_cpu_seconds_total{cpu="1",mode="system"} 4.43
node_cpu_seconds_total{cpu="1",mode="user"} 2.86
node_cpu_seconds_total{cpu="2",mode="idle"} 6323.44
node_cpu_seconds_total{cpu="2",mode="iowait"} 0.4
node_cpu_seconds_total{cpu="2",mode="irq"} 0
node_cpu_seconds_total{cpu="2",mode="nice"} 0
node_cpu_seconds_total{cpu="2",mode="softirq"} 4.52
node_cpu_seconds_total{cpu="2",mode="steal"} 0
node_cpu_seconds_total{cpu="2",mode="system"} 3.8
node_cpu_seconds_total{cpu="2",mode="user"} 2.07

curl http://127.0.0.1:9100/metrics | grep -i memory | grep -i available         
`#` TYPE process_virtual_memory_max_bytes gauge     
process_virtual_memory_max_bytes 1.8446744073709552e+19     

curl http://127.0.0.1:9100/metrics | grep -i memory | grep -i free     
`#` HELP node_memory_HugePages_Free Memory information field HugePages_Free.    
`#` TYPE node_memory_HugePages_Free gauge   
node_memory_HugePages_Free 0    
`#` HELP node_memory_MemFree_bytes Memory information field MemFree_bytes.  
`#` TYPE node_memory_MemFree_bytes gauge    
node_memory_MemFree_bytes 2.478301184e+09   
`#` HELP node_memory_SwapFree_bytes Memory information field SwapFree_bytes.    
`#` TYPE node_memory_SwapFree_bytes gauge   
node_memory_SwapFree_bytes 0    
    






<strong>3. Установите в свою виртуальную машину Netdata.</strong>       
    
<strong>4. Можно ли по выводу `dmesg` понять, осознает ли ОС, что загружена не на настоящем оборудовании, а на системе виртуализации?</strong>      
    
<strong>5. Как настроен sysctl `fs.nr_open` на системе по-умолчанию?</strong>   
    
<strong>6. Запустите любой долгоживущий процесс в отдельном неймспейсе процессов.</strong>      
    
<strong>7. Найдите информацию о том, что такое `:(){ :|:& };:`.</strong>             
