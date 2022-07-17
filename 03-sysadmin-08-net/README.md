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

```  network:    
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


