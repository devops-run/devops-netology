---
## Ответы на домашнее задание "3.7 Компьютерные сети, лекция 2" 
---
                    
<strong>1. Проверьте список доступных сетевых интерфейсов на вашем компьютере. Какие команды есть для этого в Linux и в Windows?</strong>

#### ifconfig   
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500    
        inet 192.168.1.81  netmask 255.255.255.0  broadcast 192.168.1.255   
        inet6 fe80::709f:80ff:fef9:a7f2  prefixlen 64  scopeid 0x20<link>   
        ether 72:9f:80:f9:a7:f2  txqueuelen 1000  (Ethernet)    
        RX packets 522166  bytes 112600401 (107.3 MiB)    
        RX errors 0  dropped 12  overruns 0  frame 0    
        TX packets 79663  bytes 9637706 (9.1 MiB)   
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0    
    
lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536    
        inet 127.0.0.1  netmask 255.0.0.0   
        inet6 ::1  prefixlen 128  scopeid 0x10<host>    
        loop  txqueuelen 1000  (Local Loopback)   
        RX packets 130  bytes 5670 (5.5 KiB)    
        RX errors 0  dropped 0  overruns 0  frame 0   
        TX packets 130  bytes 5670 (5.5 KiB)    
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0    
  
#### ip a   
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000   
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00   
    inet 127.0.0.1/8 scope host lo    
       valid_lft forever preferred_lft forever    
    inet6 ::1/128 scope host    
       valid_lft forever preferred_lft forever    
2: eth0@if47: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000   
    link/ether 72:9f:80:f9:a7:f2 brd ff:ff:ff:ff:ff:ff link-netnsid 0   
    inet 192.168.1.81/24 metric 1024 brd 192.168.1.255 scope global dynamic eth0    
       valid_lft 436sec preferred_lft 436sec    
    inet6 fe80::709f:80ff:fef9:a7f2/64 scope link   
       valid_lft forever preferred_lft forever    

#### $ netstat -i 
Kernel Interface table    
Iface             MTU    RX-OK RX-ERR RX-DRP RX-OVR    TX-OK TX-ERR TX-DRP TX-OVR Flg   
eth0             1500   523489      0     12 0         80407      0      0      0 BMRU    
lo              65536      130      0      0 0           130      0      0      0 LRU   
  
### windows   
#### PS C:\Users\vagrant> ipconfig /all   

Настройка протокола IP для Windows    

   Имя компьютера  . . . . . . . . . : HP-LAPTOP    
   Основной DNS-суффикс  . . . . . . :    
   Тип узла. . . . . . . . . . . . . : Гибридный    
   IP-маршрутизация включена . . . . : Нет    
   WINS-прокси включен . . . . . . . : Нет    
    
Адаптер Ethernet Ethernet:    
    
   DNS-суффикс подключения . . . . . :    
   Описание. . . . . . . . . . . . . : Realtek PCIe GBE Family Controller   
   Физический адрес. . . . . . . . . : `**-**-**-68-B3-**`    
   DHCP включен. . . . . . . . . . . : Да   
   Автонастройка включена. . . . . . : Да   
   IPv4-адрес. . . . . . . . . . . . : 192.168.1.3(Основной)    
   Маска подсети . . . . . . . . . . : 255.255.255.0    
   Аренда получена. . . . . . . . . . : 13 июля 2022 г. 18:58:26    
   Срок аренды истекает. . . . . . . . . . : 13 июля 2022 г. 20:18:27   
   Основной шлюз. . . . . . . . . : 192.168.1.1   
   DHCP-сервер. . . . . . . . . . . : 192.168.1.1   
   DNS-серверы. . . . . . . . . . . : 192.168.1.82    
                                       77.88.8.8    
   NetBios через TCP/IP. . . . . . . . : Включен    


<strong>2. Какой протокол используется для распознавания соседа по сетевому интерфейсу? Какой пакет и команды есть в Linux для этого?</strong>  
    
Для распознавания соседа в сети используется протокол канального уровня LLDP.   

Пакет в Ubuntu: lldpd     
#### apt install lldpd      
Команды:      
Usage:   **lldpcli** [OPTIONS ...] [COMMAND ...]    
    
Usage:   **lldpctl** [OPTIONS ...] [COMMAND ...]       

<strong>3. Какая технология используется для разделения L2 коммутатора на несколько виртуальных сетей? Какой пакет и команды есть в Linux для этого? Приведите пример конфига.</strong>   
  
Для разделения L2 коммутатора на несколько виртуальных сетей используется технология VLAN.    
Пакет в Ubuntu: vlan    
Команды:    
#### vconfig --help   

Usage: add             [interface-name] [vlan_id]   
       rem             [vlan-name]    
       set_flag        [interface-name] [flag-num]       [0 | 1]    
       set_egress_map  [vlan-name]      [skb_priority]   [vlan_qos]   
       set_ingress_map [vlan-name]      [skb_priority]   [vlan_qos]   
       set_name_type   [name-type]    
    
Настройки подынтерфейсов VLANов в Ubuntu точно так же, как и для сетевых интерфейсов, указываются в файле /etc/network/interfaces.    

#### man vlan-interfaces 5    
VLAN CREATION 
Vlan  interface  definitions  exist of the vlan interface name, and an optional 'raw-device' parameter.  Vlan interfaces are numbered 1 to 4095. You have the option to have interface names zero-padded to 4 numbers, or just the plain digits without leading zero.  The following example shows four ways to create a vlan with id 1 on interface eth0.  They all result in different names.   

        iface eth0.1 inet static    
            address 192.168.1.1   
            netmask 255.255.255.0   
    
        iface vlan1 inet static   
            vlan-raw-device eth0    
            address 192.168.1.1   
            netmask 255.255.255.0   
    
        iface eth0.0001 inet static   
            address 192.168.1.1   
            netmask 255.255.255.0   
    
        iface vlan0001 inet static    
            vlan-raw-device eth0    
            address 192.168.1.1   
            netmask 255.255.255.0   

<strong>4. Какие типы агрегации интерфейсов есть в Linux? Какие опции есть для балансировки нагрузки? Приведите пример конфига.</strong>

LAG – агрегация портов
Типы LAG:   
статический (на Cisco mode on).   
динамический – LACP протокол (на Cisco mode active).    
Служит для увеличения пропускной способности и/или отказоустойчивость сети.   
    
#### Типы агрегации интерфейсов в Linux   
Bonding – это объединение сетевых интерфейсов по определенному типу агрегации, Служит для увеличения пропускной способности и/или отказоустойчивость сети.    

**Mode-0(balance-rr)** – Данный режим используется по умолчанию. Balance-rr обеспечивается балансировку нагрузки и отказоустойчивость. В данном режиме сетевые пакеты отправляются “по кругу”, от первого интерфейса к последнему. Если выходят из строя интерфейсы, пакеты отправляются на остальные оставшиеся. Дополнительной настройки коммутатора не требуется при нахождении портов в одном коммутаторе. При разностных коммутаторах требуется дополнительная настройка.    

**Mode-1(active-backup)** – Один из интерфейсов работает в активном режиме, остальные в ожидающем. При обнаружении проблемы на активном интерфейсе производится переключение на ожидающий интерфейс. Не требуется поддержки от коммутатора.   

**Mode-2(balance-xor)** – Передача пакетов распределяется по типу входящего и исходящего трафика по формуле ((MAC src) XOR (MAC dest)) % число интерфейсов. Режим дает балансировку нагрузки и отказоустойчивость. Не требуется дополнительной настройки коммутатора/коммутаторов.    

**Mode-3(broadcast)** – Происходит передача во все объединенные интерфейсы, тем самым обеспечивая отказоустойчивость. Рекомендуется только для использования MULTICAST трафика.   

**Mode-4(802.3ad)** – динамическое объединение одинаковых портов. В данном режиме можно значительно увеличить пропускную способность входящего так и исходящего трафика. Для данного режима необходима поддержка и настройка коммутатора/коммутаторов.    

**Mode-5(balance-tlb)** – Адаптивная балансировки нагрузки трафика. Входящий трафик получается только активным интерфейсом, исходящий распределяется в зависимости от текущей загрузки канала каждого интерфейса. Не требуется специальной поддержки и настройки коммутатора/коммутаторов.    

**Mode-6(balance-alb)** – Адаптивная балансировка нагрузки. Отличается более совершенным алгоритмом балансировки нагрузки чем Mode-5). Обеспечивается балансировку нагрузки как исходящего так и входящего трафика. Не требуется специальной поддержки и настройки коммутатора/коммутаторов.       
    
Конфигурационный файл   
Файл для настройки сети находится в каталоге /etc/netplan/    
vi /etc/netplan/01-netcfg.yaml          
```network:      
    version: 2    
    renderer: networkd    
    ethernets:    
        ens2f0: {}    
        ens2f1: {}    
    bonds:    
        bond0:    
            dhcp4: no   
            interfaces:   
            - ens2f0    
            - ens2f1    
            parameters:   
                mode: active-backup      
            addresses:    
                - 192.168.122.195/24      
            gateway4: 192.168.122.1     
            mtu: 1500     
            nameservers:      
                addresses:      
                    - 8.8.8.8     
                    - 77.88.8.8```     

В этом примере мы объединяем физические интерфейсы ens2f0 и ens2f1     
настройка parameters mode указываем на тип объединения        
     
<strong>5. Сколько IP адресов в сети с маской /29 ? Сколько /29 подсетей можно получить из сети с маской /24. Приведите несколько примеров /29 подсетей внутри сети 10.10.10.0/24.</strong>   
  
Сколько IP адресов в сети с маской /29:     
#### ipcalc -bc 192.168.1.0/29    
Address:   192.168.1.0    
Netmask:   255.255.255.248 = 29   
Wildcard:  0.0.0.7     
=>      
Network:   192.168.1.0/29     
HostMin:   192.168.1.1      
HostMax:   192.168.1.6      
Broadcast: 192.168.1.7      
Hosts/Net: 6                     Class C, Private Internet     

Ответ: 8 адресов        
c 192.168.1.0 по 192.168.1.7    
  
#### Сколько /29 подсетей можно получить из сети с маской /24      
В 24 подсеть (Минимальный IP	10.10.10.1 Максимальный IP	10.10.10.254) войдёт 31 подсеть с маской /29 (6+2 IP)     

#### Несколько примеров /29 подсетей внутри сети 10.10.10.0/24    
![Netdata](https://i.ibb.co/68gsD0v/Screenshot-2.png)
    
![Netdata](https://i.ibb.co/T1Py1s3/Screenshot-1.png)

<strong>6. Задача: вас попросили организовать стык между 2-мя организациями. Диапазоны 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16 уже заняты. Из какой подсети допустимо взять частные IP адреса? Маску выберите из расчета максимум 40-50 хостов внутри подсети.</strong>    

Можно использовать подсеть рекомендованную согласно RFC 6598 для использования в качестве адресов для CGN (Carrier-Grade NAT). 100.64.0.0/10      
    
**`Network:   100.64.0.0/26`**       
    
HostMin:   100.64.0.1   
HostMax:   100.64.0.62    
Broadcast: 100.64.0.63    
Hosts/Net: 62   
    
<strong>7. Как проверить ARP таблицу в Linux, Windows? Как очистить ARP кеш полностью? Как из ARP таблицы удалить только один нужный IP?</strong>  
  
### Проверка ARP таблицы. Команда работает в Linux, Windows.      
arp -a     
    
### Очистка ARP кеша Windows:          
netsh interface IP delete arpcache   
      
arp -d *    
      
### Удалить только один нужный IP. Команда работает в Linux, Windows.        
    
`arp -d <ip-address>`   
    
### Очистка ARP кеша Linux:     
ip -s -s neigh flush all      
*** Round 1, deleting 5 entries ***     
*** Flush is complete after 1 round ***   
    
