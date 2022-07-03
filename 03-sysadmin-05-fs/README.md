---
## Ответы на домашнее задание "3.5. Файловые системы" 
---
                    
<strong>1. Узнайте о sparse (разряженных) файлах.</strong>     

После прочтения Wiki поэксперементировал с  разряжёнными файлами.  
    
#### dd if=/dev/zero of=./sparse-file bs=1 count=0 seek=5G   
0+0 records in  
0+0 records out 
0 bytes copied, 3.3308e-05 s, 0.0 kB/s  

vagrant vagrant 5368709120 Jul  2 18:48 sparse-file     
    
du -hs sparse-file      
0       sparse-file     
    
#### stat sparse-file    

File: sparse-file   
Size: 5368709120      Blocks: 0          IO Block: 4096   regular file      
Device: 801h/2049d      Inode: 544         Links: 1     
Access: (0664/-rw-rw-r--)  Uid: ( 1000/ vagrant)   Gid: ( 1000/ vagrant)    
Access: 2022-07-02 18:48:30.770996488 +0000     
Modify: 2022-07-02 18:48:30.770996488 +0000     
Change: 2022-07-02 18:48:30.770996488 +0000     
Birth: 2022-07-02 18:48:30.770996488 +0000   
    
Перенаправил вывод разряженного файла в обычный:    
#### cat sparse-file > simple-file   
du -hs simple-file  
5.1G    simple-file 

stat simple-file    
File: simple-file   
Size: 5368709120      Blocks: 10485768   IO Block: 4096   regular file  
Device: 801h/2049d      Inode: 1860        Links: 1 
Access: (0664/-rw-rw-r--)  Uid: ( 1000/ vagrant)   Gid: ( 1000/ vagrant)    
Access: 2022-07-02 18:52:55.612666690 +0000     
Modify: 2022-07-02 18:53:02.972602112 +0000     
Change: 2022-07-02 18:53:02.972602112 +0000     
Birth: 2022-07-02 18:52:55.612666690 +0000     

Преобразовал обычный файл в разрежённый     
#### cp --sparse=always ./simple-file ./sparse-file2     
    
#### du -hs sparse-file2  
0       sparse-file2        
    
stat sparse-file2   
File: sparse-file2  
Size: 5368709120      Blocks: 0          IO Block: 4096   regular file  
Device: 801h/2049d      Inode: 2186        Links: 1     
Access: (0664/-rw-rw-r--)  Uid: ( 1000/ vagrant)   Gid: ( 1000/ vagrant)    
Access: 2022-07-02 18:57:46.898115518 +0000     
Modify: 2022-07-02 18:57:50.078087700 +0000     
Change: 2022-07-02 18:57:50.078087700 +0000     
Birth: 2022-07-02 18:57:46.898115518 +0000      

<strong>2. Могут ли файлы, являющиеся жесткой ссылкой на один объект, иметь разные права доступа и владельца?</strong>     

В отличии от "мягких" ссылок нет, hard link не могут иметь разные права доступа и владельца.

#### wiki    
Жёсткой ссылкой (англ. hard link) в UFS-совместимых файловых системах называется структурная составляющая файла — описывающий его элемент каталога.     
Файл UFS представляет собой структуру блоков данных на диске, имеющую уникальный индексный дескриптор (inode) и набор атрибутов (метаинформацию).   
Жёсткая ссылка связывает индексный дескриптор файла с каталогом и даёт ему имя.     

для проверки создал простой файл:   
echo "текст текст текст текст" > source       
создал hard link на этот  файл  
ln source hardlink        
ls -lih    
total 8.0K          
3357 -rw-rw-r-- 2 vagrant vagrant 44 Jul  2 19:34 hardlink      
3357 -rw-rw-r-- 2 vagrant vagrant 44 Jul  2 19:34 source       


<strong>3. Виртуальная машинa с двумя дополнительными неразмеченными дисками по 2.5 Гб создана</strong>     

#### root@vagrant:~# lsblk | grep sd    
sda                         8:0    0   64G  0 disk     
├─sda1                      8:1    0    1M  0 part     
├─sda2                      8:2    0  1.5G  0 part /boot    
└─sda3                      8:3    0 62.5G  0 part     
sdb                         8:16   0  2.5G  0 disk     
sdc                         8:32   0  2.5G  0 disk     
     
<strong>4. Используя fdisk, разбейте первый диск на 2 раздела: 2 Гб, оставшееся пространство.</strong>      
     
Created a new partition 2 of type 'Linux' and of size 511 MiB.   
     
Command (m for help): p  
Disk /dev/sdb: 2.51 GiB, 2684354560 bytes, 5242880 sectors  
Disk model: VBOX HARDDISK     
Units: sectors of 1 * 512 = 512 bytes        
Sector size (logical/physical): 512 bytes / 512 bytes       
I/O size (minimum/optimal): 512 bytes / 512 bytes      
Disklabel type: dos      
Disk identifier: 0xe380d15f        
     
Device     Boot   Start     End Sectors  Size Id Type       
/dev/sdb1          2048 4196351 4194304    2G 83 Linux      
/dev/sdb2       4196352 5242879 1046528  511M 83 Linux      
          
Command (m for help): wq      
     
#### root@vagrant:~# lsblk | grep sd    
sda                         8:0    0   64G  0 disk     
├─sda1                      8:1    0    1M  0 part     
├─sda2                      8:2    0  1.5G  0 part /boot    
└─sda3                      8:3    0 62.5G  0 part     
sdb                         8:16   0  2.5G  0 disk     
├─sdb1                      8:17   0    2G  0 part     
└─sdb2                      8:18   0  511M  0 part     
sdc                         8:32   0  2.5G  0 disk     
     
<strong>5. Используя sfdisk, перенесите данную таблицу разделов на второй диск.</strong>     
         
     
#### root@vagrant:~# sfdisk -d /dev/sdb | sfdisk /dev/sdc   
Checking that no-one is using this disk right now ... OK        
          
Disk /dev/sdc: 2.51 GiB, 2684354560 bytes, 5242880 sectors       
Disk model: VBOX HARDDISK     
Units: sectors of 1 * 512 = 512 bytes   
Sector size (logical/physical): 512 bytes / 512 bytes       
I/O size (minimum/optimal): 512 bytes / 512 bytes      
     
>>> Script header accepted.   
>>> Script header accepted.   
>>> Script header accepted.   
>>> Script header accepted.   
>>> Created a new DOS disklabel with disk identifier 0xe380d15f.      
/dev/sdc1: Created a new partition 1 of type 'Linux' and of size 2 GiB.     
/dev/sdc2: Created a new partition 2 of type 'Linux' and of size 511 MiB.       
/dev/sdc3: Done.         
          
New situation:      
Disklabel type: dos 
Disk identifier: 0xe380d15f   
     
Device     Boot   Start     End Sectors  Size Id Type       
/dev/sdc1          2048 4196351 4194304    2G 83 Linux      
/dev/sdc2       4196352 5242879 1046528  511M 83 Linux      
          
The partition table has been altered.        
Calling ioctl() to re-read partition table.  
Syncing disks. 
     
#### Имеем два одинаковых (по форме и содержанию :-)) диска, готовых для включения в Raid.      
     
#### root@vagrant:~# lsblk | grep sd    
sda                         8:0    0   64G  0 disk     
├─sda1                      8:1    0    1M  0 part     
├─sda2                      8:2    0  1.5G  0 part /boot    
└─sda3                      8:3    0 62.5G  0 part     
sdb                         8:16   0  2.5G  0 disk     
├─sdb1                      8:17   0    2G  0 part     
└─sdb2                      8:18   0  511M  0 part     
sdc                         8:32   0  2.5G  0 disk     
├─sdc1                      8:33   0    2G  0 part     
└─sdc2                      8:34   0  511M  0 part     

<strong>6. Соберите mdadm RAID1 на паре разделов 2 Гб.</strong>       

#### mdadm --create --verbose /dev/md0 --level=1 --raid-devices=2 /dev/sdb1 /dev/sdc1     
mdadm: Note: this array has metadata at the start and       
    may not be suitable as a boot device.  If you plan to   
    store '/boot' on this device please ensure that    
    your boot-loader understands md/v1.x metadata, or use   
    --metadata=0.90 
mdadm: size set to 2094080K   
Continue creating array? y    
mdadm: Defaulting to version 1.2 metadata    
mdadm: array /dev/md0 started.     
     
#### root@vagrant:~# cat /proc/mdstat   
Personalities : [linear] [multipath] [raid0] [raid1] [raid6] [raid5] [raid4] [raid10]     
md0 : active raid1 sdc1[1] sdb1[0]      
      2094080 blocks super 1.2 [2/2] [UU]         

#### root@vagrant:~# lsblk         
sda                         8:0    0   64G  0 disk     
├─sda1                      8:1    0    1M  0 part     
├─sda2                      8:2    0  1.5G  0 part  /boot   
└─sda3                      8:3    0 62.5G  0 part     
  └─ubuntu--vg-ubuntu--lv 253:0    0 31.3G  0 lvm   /  
sdb                         8:16   0  2.5G  0 disk     
├─sdb1                      8:17   0    2G  0 part     
│ └─md0                     9:0    0    2G  0 raid1    
└─sdb2                      8:18   0  511M  0 part     
sdc                         8:32   0  2.5G  0 disk     
├─sdc1                      8:33   0    2G  0 part     
│ └─md0                     9:0    0    2G  0 raid1    
└─sdc2                      8:34   0  511M  0 part     

<strong>7. Соберите mdadm RAID0 на второй паре маленьких разделов.</strong>     
#### root@vagrant:~# mdadm --create --verbose /dev/md1 --level=0 --raid-devices=2 /dev/sdb2 /dev/sdc2
mdadm: chunk size defaults to 512K      
mdadm: Defaulting to version 1.2 metadata         
mdadm: array /dev/md1 started.          

#### root@vagrant:~# cat /proc/mdstat
Personalities : [linear] [multipath] [raid0] [raid1] [raid6] [raid5] [raid4] [raid10]     
md1 : active raid0 sdc2[1] sdb2[0]      
      1042432 blocks super 1.2 512k chunks        
          
md0 : active raid1 sdc1[1] sdb1[0]      
      2094080 blocks super 1.2 [2/2] [UU]         

unused devices: <none>        

#### root@vagrant:~# lsblk    
├─sda1                      8:1    0    1M  0 part     
├─sda2                      8:2    0  1.5G  0 part  /boot   
└─sda3                      8:3    0 62.5G  0 part     
  └─ubuntu--vg-ubuntu--lv 253:0    0 31.3G  0 lvm   /  
sdb                         8:16   0  2.5G  0 disk     
├─sdb1                      8:17   0    2G  0 part     
│ └─md0                     9:0    0    2G  0 raid1    
└─sdb2                      8:18   0  511M  0 part     
  └─md1                     9:1    0 1018M  0 raid0    
sdc                         8:32   0  2.5G  0 disk     
├─sdc1                      8:33   0    2G  0 part     
│ └─md0                     9:0    0    2G  0 raid1    
└─sdc2                      8:34   0  511M  0 part     
  └─md1                     9:1    0 1018M  0 raid0    

#### Презентация с урока      
Воспользуемся инструкцией для сохранения конфигурации:      
 echo 'DEVICE partitions containers' > /etc/mdadm/mdadm.conf     
 mdadm --detail --scan >> /etc/mdadm/mdadm.conf       
     
#### cat /etc/mdadm/mdadm.conf     
root@vagrant:~# cat /etc/mdadm/mdadm.conf    
DEVICE partitions containers       
ARRAY /dev/md0 metadata=1.2 name=vagrant:0 UUID=f7624870:7fe4b3be:d685f14a:a0125a89       
ARRAY /dev/md1 metadata=1.2 name=vagrant:1 UUID=f4030c2c:fe09a46e:06da0425:b2a429a8       

#### Сделал по инструкции, проверил после перезапуска сервера.    
Raid тома на месте, единственное, что Ubuntu предпочла их переименовать.   
     
#### root@vagrant:~# cat /proc/mdstat   
Personalities : [raid1] [raid0] [linear] [multipath] [raid6] [raid5] [raid4] [raid10]     
md126 : active raid0 sdb2[0] sdc2[1]    
      1042432 blocks super 1.2 512k chunks   
     
md127 : active (auto-read-only) raid1 sdb1[0] sdc1[1]  
      2094080 blocks super 1.2 [2/2] [UU]    

#### Победил это, добавив параметр HOMEHOST vagrant в /etc/mdadm/mdadm.conf    
#### и выполнив команду update-initramfs -u      
root@vagrant:~# cat /proc/mdstat   
Personalities : [raid0] [raid1] [linear] [multipath] [raid6] [raid5] [raid4] [raid10]     
md0 : active raid1 sdb1[0] sdc1[1] 
      2094080 blocks super 1.2 [2/2] [UU]    
     
md1 : active raid0 sdc2[1] sdb2[0] 
      1042432 blocks super 1.2 512k chunks   
          
#### Теперь всё ровно....как учили )).       


<strong>8. Создайте 2 независимых PV на получившихся md-устройствах.</strong>       

#### root@vagrant:~# pvcreate /dev/md0  
Physical volume "/dev/md0" successfully created.  
root@vagrant:~# pvcreate /dev/md1       
Physical volume "/dev/md1" successfully created.       

#### root@vagrant:~# pvs -a        
  PV         VG        Fmt  Attr PSize    PFree   
  /dev/md0             lvm2 ---    <2.00g   <2.00g          
  /dev/md1             lvm2 ---  1018.00m 1018.00m          
  /dev/sda2                 ---        0        0      
  /dev/sda3  ubuntu-vg lvm2 a--   <62.50g   31.25g          

          
<strong>9. Создайте общую volume-group на этих двух PV.</strong>      
    
#### root@vagrant:~# vgcreate group-md0md1 /dev/md0 /dev/md1     
Volume group "group-md0md1" successfully created  

#### root@vagrant:~# vgdisplay     
 --- Volume group ---    
VG Name               group-md0md1    
System ID                        
Format                lvm2       
Metadata Areas        2          
Metadata Sequence No  1          
VG Access             read/write      
VG Status             resizable       
MAX LV                0          
Cur LV                0          
Open LV               0          
Max PV                0          
Cur PV                2          
Act PV                2          
VG Size               <2.99 GiB       
PE Size               4.00 MiB        
Total PE              765        
Alloc PE / Size       0 / 0      
Free  PE / Size       765 / <2.99 GiB      
VG UUID               Zc6wFy-aj40-zp45-aIl3-V2Xq-LN1u-9iVv7w        

<strong>10. Создайте LV размером 100 Мб, указав его расположение на PV с RAID0.</strong>       
#### root@vagrant:~# lvcreate -L 100M group-md0md1 /dev/md1      
Logical volume "lvol0" created.    
     
<strong>11. Создайте mkfs.ext4 ФС на получившемся LV.</strong>   
     
<strong>12. Смонтируйте этот раздел в любую директорию, например, /tmp/new.</strong> 
     
<strong>13. Поместите туда тестовый файл</strong>      
     
<strong>14. Прикрепите вывод lsblk</strong>       
     
<strong>15. Протестируйте целостность файла:</strong>       
     
<strong>16. Используя pvmove, переместите содержимое PV с RAID0 на RAID1.</strong>   
     
<strong>17. Сделайте --fail на устройство в вашем RAID1 md.</strong>       
     
<strong>18. Подтвердите выводом dmesg, что RAID1 работает в деградированном состоянии.</strong>     
     
<strong>19. Протестируйте целостность файла, несмотря на "сбойный" диск он должен продолжать быть доступен:</strong>    
     
<strong>20. Погасите тестовый хост, vagrant destroy.</strong>    
#### Destroy после зачёта.
     


