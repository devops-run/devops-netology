---
## Ответы на домашнее задание "3.4. Операционные системы, лекция 2" 
---
                    
<strong>1. Cоздайте самостоятельно простой unit-файл</strong>      
Doc: https://www.freedesktop.org/software/systemd/man/systemd.service.html   

wget node_exporter-1.3.1.linux-amd64.tar.gz  
tar xfz node_exporter-1.3.1.linux-amd64.tar.gz && cd node_exporter-1.3.1.linux-amd64    
cp node_exporter /usr/sbin/    
### ДОРАБОТКА
получил параметры запуска бин.файла ./node_exporter -h и добавил их в файл  
vi /etc/node/node.conf  
##################################  
ARG1=--web.listen-address=":9200"  
ARG2=--collector.ethtool  
##################################  
vi /lib/systemd/system/node.service      
###########################################     
[Unit]  
Description=node_exporter daemon      
Wants=network-online.target     
After=remote-fs.target nss-user-lookup.target 
    
[Service]   
EnvironmentFile=/etc/node/node.conf  
ExecStart=/usr/sbin/node_exporter $ARG1 $ARG2   #Запуск с аргументами из файла  
Type=simple      
KillMode=process        
Restart=on-failure      
    
[Install]   
WantedBy=multi-user.target   
###########################################     
     
systemctl daemon-reload     
systemctl enable node   
systemctl start node     
systemctl status node   
    
● node.service - node_exporter daemon  
     Loaded: loaded (/etc/systemd/system/node.service; enabled; vendor preset: enabled)  
     Active: active (running) since Fri 2022-07-08 14:14:03 UTC; 6s ago  
   Main PID: 1847 (node_exporter)  
      Tasks: 4 (limit: 3521)  
     Memory: 2.3M  
     CGroup: /system.slice/node.service  
             └─1847 /usr/sbin/node_exporter --web.listen-address=:9200 --collector.ethtool  

Jul 08 14:14:03 vagrant node_exporter[1847]: ts=2022-07-08T14:14:03.383Z caller=node_exporter.go:115 level=info collector=thermal_zone  
Jul 08 14:14:03 vagrant node_exporter[1847]: ts=2022-07-08T14:14:03.383Z caller=node_exporter.go:115 level=info collector=time  
Jul 08 14:14:03 vagrant node_exporter[1847]: ts=2022-07-08T14:14:03.383Z caller=node_exporter.go:115 level=info collector=timex  
Jul 08 14:14:03 vagrant node_exporter[1847]: ts=2022-07-08T14:14:03.383Z caller=node_exporter.go:115 level=info collector=udp_queues  
Jul 08 14:14:03 vagrant node_exporter[1847]: ts=2022-07-08T14:14:03.383Z caller=node_exporter.go:115 level=info collector=uname  
Jul 08 14:14:03 vagrant node_exporter[1847]: ts=2022-07-08T14:14:03.383Z caller=node_exporter.go:115 level=info collector=vmstat  
Jul 08 14:14:03 vagrant node_exporter[1847]: ts=2022-07-08T14:14:03.383Z caller=node_exporter.go:115 level=info collector=xfs  
Jul 08 14:14:03 vagrant node_exporter[1847]: ts=2022-07-08T14:14:03.383Z caller=node_exporter.go:115 level=info collector=zfs  
Jul 08 14:14:03 vagrant node_exporter[1847]: ts=2022-07-08T14:14:03.383Z caller=node_exporter.go:199 level=info msg="Listening on" address=:9200  
Jul 08 14:14:03 vagrant node_exporter[1847]: ts=2022-07-08T14:14:03.383Z caller=tls_config.go:195 level=info msg="TLS is disabled." http2=false  

#### root@vagrant:~# ps ax | grep node
   1847 ?        Ssl    0:00 /usr/sbin/node_exporter --web.listen-address=:9200 --collector.ethtool  
   
#### root@vagrant:~# lsof -i | grep node  
   node_expo 1847            root    7u  IPv6  30436      0t0  TCP *:9200 (LISTEN)  
    
### КОНЕЦ ДОРАБОТКИ  


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
    
curl http://127.0.0.1:9100/metrics | grep -i space  
`#` HELP node_filesystem_avail_bytes Filesystem space available to non-root users in bytes.
node_filesystem_avail_bytes{device="vagrant",fstype="vboxsf",mountpoint="/vagrant"} 2.21055336448e+11       

curl http://127.0.0.1:9100/metrics | grep -i network | less     
curl http://127.0.0.1:9100/metrics | grep -i network | grep "Network device statistic"   
node_network_receive_compressed_total Network device statistic receive_compressed.  
node_network_receive_drop_total Network device statistic receive_drop.  
node_network_receive_errs_total Network device statistic receive_errs.  
node_network_receive_fifo_total Network device statistic receive_fifo.  
node_network_receive_frame_total Network device statistic receive_frame.    
node_network_receive_multicast_total Network device statistic receive_multicast.    
node_network_receive_packets_total Network device statistic receive_packets.    
node_network_transmit_bytes_total Network device statistic transmit_bytes.  
node_network_transmit_carrier_total Network device statistic transmit_carrier.  
node_network_transmit_colls_total Network device statistic transmit_colls.  
node_network_transmit_compressed_total Network device statistic transmit_compressed.    
node_network_transmit_drop_total Network device statistic transmit_drop.    
node_network_transmit_errs_total Network device statistic transmit_errs.    
node_network_transmit_fifo_total Network device statistic transmit_fifo.    
node_network_transmit_packets_total Network device statistic transmit_packets.  

<strong>3. Установите в свою виртуальную машину Netdata.</strong>       
PS F:\Vagrant-vm-conf\Ubuntu2204-oficial> vagrant up    
Bringing machine 'default' up with 'virtualbox' provider...     
==> default: Checking if box 'ubuntu/jammy64' version '20220616.0.0' is up to date...       
==> default: Clearing any previously set forwarded ports...     
==> default: Clearing any previously set network interfaces...      
==> default: Preparing network interfaces based on configuration...     
    default: Adapter 1: nat     
    default: Adapter 2: bridged     
==> default: Forwarding ports...        
    default: 19999 (guest) => 19999 (host) (adapter 1)      
    default: 22 (guest) => 2222 (host) (adapter 1)      
==> default: Running 'pre-boot' VM customizations...        
==> default: Booting VM...      
==> default: Waiting for machine to boot. This may take a few minutes...        
    default: SSH address: 127.0.0.1:2222        
    default: SSH username: vagrant      
    default: SSH auth method: private key       
==> default: Machine booted and ready!      
==> default: Checking for guest additions in VM...      
    default: The guest additions on this VM do not match the installed version of       
    default: VirtualBox! In most cases this is fine, but in rare cases it can       
    default: prevent things such as shared folders from working properly. If you see        
    default: shared folder errors, please make sure the guest additions within the      
    default: virtual machine match the version of VirtualBox you have installed on      
    default: your host and reload your VM.      
    default:    
    default: Guest Additions Version: 6.0.0 r127566     
    default: VirtualBox Version: 6.1        
==> default: Configuring and enabling network interfaces...     
==> default: Mounting shared folders...     
    default: /vagrant => F:/Vagrant-vm-conf/Ubuntu2204-oficial      

### Приятно удивлён обилием датчиков в этой простой, незатейливой (на первый взгляд) системе.     
    
![Netdata](https://i.ibb.co/N6CgwcN/Screen.png)


<strong>4. Можно ли по выводу `dmesg` понять, осознает ли ОС, что загружена не на настоящем оборудовании, а на системе виртуализации?</strong>      
#### Да, проверено на нескольких гипервизорах   
vagrant@ubuntu-jammy:~$ sudo dmesg | grep virtual          
[    0.001020] CPU MTRRs all blank - virtualized system.          
[    0.009367] Booting paravirtualized kernel on KVM            
[    3.478966] systemd[1]: Detected virtualization oracle.          
    
vagrant@vagrant:~# sudo dmesg | grep virtual    
[    0.015326] Booting paravirtualized kernel on Hyper-V        
[    6.373222] systemd[1]: Detected virtualization microsoft.       
    
vagrant@debian11:~$ sudo dmesg | grep virtual   
[    0.012736] Booting paravirtualized kernel on VMware hypervisor  
[    0.666431] VMware vmxnet3 virtual NIC driver - version 1.5.0.0-k-NAPI   
[    1.522126] systemd[1]: Detected virtualization vmware.  

<strong>5. Как настроен sysctl `fs.nr_open` на системе по-умолчанию?</strong>   
sysctl fs.nr_open       
fs.nr_open = 1048576        
https://www.kernel.org/doc/html/latest/admin-guide/sysctl/fs.html?highlight=fs+nr_open  
Это обозначает максимальное количество дескрипторов файлов, которые может выделить процесс. Значение по умолчанию - 1024*1024 (1048576), чего должно быть достаточно для большинства машин. Фактический лимит зависит от лимита ресурсов RLIMIT_NOFILE.     
    
-n the maximum number of open file descriptors  
vagrant@ubuntu-jammy:~$ ulimit -Sn  
1024    

Soft лимит не сможет превысить системный fs.nr_open = 1048576     
    
<strong>6. Запустите любой долгоживущий процесс в отдельном неймспейсе процессов.</strong>      
    
root@ubuntu-jammy:~# unshare -f --pid --mount-proc sleep 1h &   
[1] 1583    
    
Далее (в новом терминале):  
ps ax | grep sleep  
   1583 pts/2    S      0:00 unshare -f --pid --mount-proc sleep 1h      
   1584 pts/2    S      0:00 sleep 1h   
    
Подключаемся к процессу:        
nsenter -t 1584 -p -m      
Листинг процессов:  
ps ax   
    
    PID TTY      STAT   TIME COMMAND    
      1 pts/2    S+     0:00 sleep 1h   
      2 pts/3    S      0:00 -bash  
     13 pts/3    R+     0:00 ps ax  

<strong>7. Найдите информацию о том, что такое `:(){ :|:& };:`.</strong>         

### Understanding Bash fork() Bomb :(){ :|:& };: code   
Форк-бомба - это форма DoS-атаки типа "отказ в обслуживании" против систем на базе Linux или Unix. В нем используется операция fork. Это :(){ :|:& };:не что иное, как функция bash. Эта функция выполняется рекурсивно. Он часто используется системным администратором для проверки ограничений пользовательских процессов на сервере. Ограничения процесса Linux могут быть настроены через /etc/security/limits.conf и PAM, чтобы избежать bash fork() бомбы. После успешной активации fork-бомбы в системе может оказаться невозможным возобновить нормальную работу без перезагрузки системы, поскольку единственным решением для fork-бомбы является уничтожение всех ее экземпляров.    

После запуска этой конструкции, система пожила 3 мин. и ушла в "неадекват".     
Bash fork() Bomb создала огромное кол-во процессов, и убила систему (закончилась память)     

Ждал 20 мин. система не ожила. Автоматической стабилизации не произошло.    
Помогала только перезагрузка vagrant reload.    
Поиск в логе dmesg по слову fork, а также dmesg -l crit,err  ни чего не дал.    


Включил ограничения на кол-во запускаемых процессов в        
/etc/security/limits.conf   
`#*`            soft    core            0    
root            hard    core            100000    
`#*`            hard    rss             10000        
@vagrant        hard    nproc           50  
@vagrant        soft    nproc           50  

Это обезопасило систему. После перезагрузки и запуска Bash fork() Bomb под юзером vagrant система не зависла и продолжала работать. 
Максимальное кол-во процессов, запускаемых юзером vagrant = 50.  

#### Для себя сделал важный вывод: никогда не давать "безграничных" ресурсов юзеру на сервере.   
Ограничивать, и добавлять по требованию. Безконтрольность --> путь в ад ((.  
