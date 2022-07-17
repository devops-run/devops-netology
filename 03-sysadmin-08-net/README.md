---
## Ответы на домашнее задание "3.8 Компьютерные сети, лекция 3" 
---
                    
<strong>1. Подключитесь к публичному маршрутизатору в интернет. Найдите маршрут к вашему публичному IP</strong>

Мой внешний IP (VPN) 80.92.205.188    
    
**route-views>show ip route 80.92.205.188**     
Routing entry for 80.92.205.0/24      
  Known via "bgp 6447", distance 20, metric 0     
  Tag 6939, type external     
  Last update from 64.71.137.241 3w4d ago     
  Routing Descriptor Blocks:      
   **64.71.137.241, from 64.71.137.241, 3w4d ago**     
      Route metric is 0, traffic share count is 1     
      AS Hops 2     
      Route tag 6939      
      MPLS label: none      
        
     
**route-views>show bgp 80.92.205.188**      
BGP routing table entry for 80.92.205.0/24, version 2327992362    
Paths: (23 available, best #16, table default)    
  Not advertised to any peer    
  Refresh Epoch 1   
  20912 3257 174 52000    
    **212.66.96.126 from 212.66.96.126 (212.66.96.126)**    
      Origin IGP, localpref 100, valid, external    
      Community: 3257:8070 3257:30155 3257:50001 3257:53900 3257:53902 20912:65004    
      path 7FE115AA6288 RPKI State not found    
      rx pathid: 0, tx pathid: 0    
  Refresh Epoch 1   
  3333 1257 174 52000   
    **193.0.0.56 from 193.0.0.56 (193.0.0.56)**   
      Origin IGP, localpref 100, valid, external    
      Community: 174:21001 174:22013 1257:50 1257:51 1257:90 1257:2000 1257:3276 1257:4103    
      path 7FE0D9FA0810 RPKI State not found    
      rx pathid: 0, tx pathid: 0    
  Refresh Epoch 1   
  4901 6079 3356 52000    
    **162.250.137.254 from 162.250.137.254 (162.250.137.254)**    
    
......    
И далее несколько десятков строк        
  

<strong>2. Создайте dummy0 интерфейс в Ubuntu. Добавьте несколько статических маршрутов. Проверьте таблицу маршрутизации.</strong>

#### vi /etc/netplan/02-dummy.yaml    

```network:    
  version: 2    
  renderer: networkd    
  bridges:    
    dummy0:   
      dhcp4: no   
      dhcp6: no   
      accept-ra: no   
      addresses:    
        - 192.168.10.10/16    
      routes:   
        - to: 192.168.10.0/24   
          via: 192.168.10.1   
        - to: 192.168.20.0/24   
          via: 192.168.20.1   
          on-link: true   
```
#### root@ubuntu2204:~# ifconfig    

```
dummy0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
        inet 192.168.10.10  netmask 255.255.0.0  broadcast 192.168.255.255
        ether 3a:c6:d6:63:ec:58  txqueuelen 1000  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

ens18: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.1.13  netmask 255.255.255.0  broadcast 192.168.1.255
        ether 86:80:f8:c4:ab:ed  txqueuelen 1000  (Ethernet)
        RX packets 1463  bytes 116734 (116.7 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 1691  bytes 200995 (200.9 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 92  bytes 7208 (7.2 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 92  bytes 7208 (7.2 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```
#### root@ubuntu2204:~# route -n    
```
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         192.168.1.1     0.0.0.0         UG    0      0        0 ens18
192.168.0.0     0.0.0.0         255.255.0.0     U     0      0        0 dummy0
192.168.1.0     0.0.0.0         255.255.255.0   U     0      0        0 ens18
192.168.10.0    192.168.10.1    255.255.255.0   UG    0      0        0 dummy0
192.168.20.0    192.168.20.1    255.255.255.0   UG    0      0        0 dummy0
```
<strong>3. Проверьте открытые TCP порты в Ubuntu, какие протоколы и приложения используют эти порты? Приведите несколько примеров.</strong>

#### root@ubuntu2204:~# netstat -ant 
   
```
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State
tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN
tcp        0      0 127.0.0.1:6010          0.0.0.0:*               LISTEN
tcp        0      0 192.168.1.13:22         192.168.1.11:59689      ESTABLISHED
tcp        0     64 192.168.1.13:22         192.168.1.11:59688      ESTABLISHED
```
#### root@ubuntu2204:~# ss -tnlp    

```
root@ubuntu2204:~# ss -tnlp
State      Recv-Q     Send-Q         Local Address:Port         Peer Address:Port     Process
LISTEN     0          128                127.0.0.1:6010              0.0.0.0:*         users:(("sshd",pid=836,fd=5))
LISTEN     0          4096           127.0.0.53%lo:53                0.0.0.0:*         users:(("systemd-resolve",pid=584,fd=14))
LISTEN     0          128                  0.0.0.0:22                0.0.0.0:*         users:(("sshd",pid=668,fd=3))

```
#### root@ubuntu2204:~# ss -unap    

```
State          Recv-Q         Send-Q                  Local Address:Port                   Peer Address:Port         Process
UNCONN         0              0                       127.0.0.53%lo:53                          0.0.0.0:*             users:(("systemd-resolve",pid=584,fd=13))

```

